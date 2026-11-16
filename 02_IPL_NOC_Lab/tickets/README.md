# Tickets — Workflow

Each ticket is worked like a NOC shift: alert → triage → isolate → fix or escalate → document.

1. **Read the symptom only.** Don't look at configs first.
2. **Confirm it.** Reproduce the failure yourself.
3. **Isolate by layer and by hop.** Follow the path in [`../design/addressing.md`](../design/addressing.md): source host → gateway → each router → firewall → destination, then the return path. Stop at the first hop that doesn't match what's expected.
4. **Decide:** Tier 1 fix (interface down, wrong VLAN, shutdown port, etc.) or escalate (routing design, firewall policy change).
5. **Document** using the template below. The escalation note is graded like the fix.

## Template

```markdown
# TICKET-NNN — <short summary>

| Field | Value |
|-------|-------|
| Priority | P1 / P2 / P3 / P4 |
| Opened | YYYY-MM-DD HH:MM |
| Reported by | Monitoring / user / site |
| Affected | Source → destination, service |
| Status | Open / In progress / Resolved / Escalated |

## Symptom
What was reported, and the first output that confirms it.

## Impact
Who or what can't work.

## Troubleshooting log
| Time | Device | Command | Result | Conclusion |
|------|--------|---------|--------|------------|

## Root cause
## Resolution (if Tier 1)
## Escalation note (if escalated)
To: Tier 2 / Network Engineering
Summary, evidence (commands + output), suspected cause, impact, what was already ruled out.

## Lesson learned
```
