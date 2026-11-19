[← Back to lab README](../README.md)

# Tickets — Workflow

Tickets in this lab follow the **[Incident Response Playbook](https://github.com/poselvy-ai/Incident-Response-Playbook)**:

| Step | Playbook reference |
|------|--------------------|
| Classify severity (impact × urgency → SEV-1 to SEV-5, ITIL P1–P5) | [Severity Matrix](https://github.com/poselvy-ai/Incident-Response-Playbook/blob/main/Severity-Matrix.md) |
| Who to engage, bridge, update cadence | [Escalation Workflow](https://github.com/poselvy-ai/Incident-Response-Playbook/blob/main/Escalation-Workflow.md) |
| Notifications and status updates | [Communication Templates](https://github.com/poselvy-ai/Incident-Response-Playbook/blob/main/Communication-Template.md) |
| Post-incident review (required SEV-1/SEV-2, optional SEV-3) | [RCA Template](https://github.com/poselvy-ai/Incident-Response-Playbook/blob/main/RCA-Template.md) |

## Roles and scope

I work every ticket as **NOC Analyst 1 (Tier 1)**. If I can't resolve it within Tier 1 scope, I escalate with evidence.

| Tier 1 can do | Tier 1 escalates |
|---------------|------------------|
| Read-only checks: `show`, `ping`, `traceroute`, `telnet <ip> <port>`, logs | Firewall policy, NAT, captures, `packet-tracer` (unless a runbook says so) |
| Classify severity, open the incident, send playbook communications | Routing design or protocol config changes (OSPF, BGP) |
| Standard changes in the runbook: `shutdown`/`no shutdown` a port, clear counters, bounce an err-disabled port | Server or application config (OS, services, host firewall) |
| Confirm the fix and close Tier 1 tickets | ISP / carrier circuit faults |

**Escalation targets** (played by Claude in the lab):

| Team | Owns |
|------|------|
| NOC Engineer (Tier 2) | Routing, switching, deeper network isolation |
| Firewall / Security team | FW1 policy, NAT, VPN, security events |
| Server team | DMZ-SRV1 and other hosts |
| ISP / carrier | ISP-A, ISP-B circuits and peering |

An escalation is complete when it states: what was reported, what I tested (commands + output), what I ruled out, the suspected area, severity, and impact.

## Logging convention

Log as you go. Any message to Claude that starts with **`TICKET-xxx Note:`** or **`TICKET-xxx Log:`** is added to that ticket's troubleshooting log in GitHub straight away, with the time it was sent and the output as pasted, and confirmed in one line. Event times are then checked against device logs (UTC), which are the source of truth.

**Forgot to log a step?** Send **`TICKET-xxx Late Log:`** with roughly when it happened. It's recorded as a **late entry** with two times, when it happened and when it was recorded (e.g. `≈15:33 (late entry, recorded 15:58)`). The "happened" time comes from evidence: `show logging`, `show history`, terminal scrollback or monitoring timestamps; otherwise it's marked approx. Original entries are never rewritten or backdated.

## How a ticket is worked

Each ticket is worked like a NOC shift: alert → classify → triage → isolate → fix or escalate → communicate → document.

1. **Read the symptom only.** Don't look at configs first.
2. **Confirm it.** Reproduce the failure yourself.
3. **Classify severity** with the Severity Matrix: rate impact (None / Low / Moderate / High) and urgency (Low / Medium / High / Immediate). Re-check it as you learn more: escalate (lower SEV number) or de-escalate (higher SEV number).
4. **Follow the escalation workflow** for that SEV: who to notify, whether a bridge opens, update cadence.
5. **Isolate by layer and by hop.** Follow the path in [`../design/addressing.md`](../design/addressing.md): source host → gateway → each router → firewall → destination, then the return path. Check **both ends** of the path. Stop at the first hop that doesn't match what's expected.
6. **Decide:** Tier 1 fix (interface down, wrong VLAN, shutdown port, etc.) or escalate (routing design, firewall policy, server config).
7. **Communicate** using the playbook templates at the cadence for the SEV level.
8. **Document** using the template below. The escalation note is graded like the fix. Changes to in-service devices go through a [change record](../changes/README.md).
9. **RCA** if the SEV level requires it.

## Template

```markdown
# TICKET-NNN — <short summary>

| Field | Value |
|-------|-------|
| Severity | SEV-X (ITIL PX): impact <None/Low/Moderate/High> × urgency <Low/Medium/High/Immediate> |
| Opened | YYYY-MM-DD HH:MM |
| Reported by | Monitoring / user / site |
| Affected | Source → destination, service |
| Status | Open / In progress / Escalated / Resolved / Closed |

## Symptom
What was reported, and the first output that confirms it.

## Impact
Who or what can't work. (Drives the impact rating.)

## Severity history
| Time | SEV | Reason |
|------|-----|--------|

## Communications
| Time | Template | Audience | Summary |
|------|----------|----------|---------|

## Troubleshooting log
| Time | Device | Command | Result | Conclusion |
|------|--------|---------|--------|------------|

## Root cause
## Resolution (if Tier 1) / Change record
## Escalation note (if escalated)
To: Tier 2 / Network Engineering / Server team
Summary, evidence (commands + output), suspected cause, impact, what was already ruled out.

## RCA
Required for SEV-1/SEV-2 (playbook RCA template); optional for SEV-3.

## Lesson learned
```

---

[← Back to lab README](../README.md)
