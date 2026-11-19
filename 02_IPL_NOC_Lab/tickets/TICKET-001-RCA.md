[← Back to TICKET-001](TICKET-001.md) · [← Back to lab README](../README.md)

# RCA — TICKET-001: Substation 1 SCADA workstation unreachable

Template: [Incident Response Playbook RCA Template](https://github.com/poselvy-ai/Incident-Response-Playbook/blob/main/RCA-Template.md)

| | |
|---|---|
| Drafted by | Patrick Selvy, NOC Analyst 1 ([original draft](./TICKET-001-RCA-analyst-draft.txt)) |
| Reviewed and completed by | Ops Lead (Incident Commander) |
| Status | **Open: preliminary root cause.** Final root cause pending Security review of the console change |

> Times are PST (UTC−8). Device logs are UTC and are the source of truth for event times.

---

## 1. Incident Summary
**Date:** 2026-11-18
**Start Time:** 15:19:49 PST (fault, SUB-R1 log 23:19:49 UTC). Detected 15:20
**End Time:** 15:34:20 PST (service restored, SUB-R1 log 23:34:20 UTC)
**Duration:** 14 min 31 s
**Severity (SEV-1 to SEV-5):** SEV-2 (P2)
**Systems Impacted:** SUB-R1 Ethernet0/1 (Substation 1 SCADA LAN gateway, 10.2.10.1/24); SCADA-PC1 (10.2.10.10)
**Business Impact Summary:** Substation 1 operators lost local visibility of feeder telemetry for about 14.5 minutes. Monitoring was affected; there is no evidence that control of field equipment was affected. Whether the control center also lost Substation 1 telemetry was not confirmed during the incident.

---

## 2. Timeline of Events

| Time (PST) | Event |
|------------|--------|
| 15:19:49 | SUB-R1 Ethernet0/1 shut down from the device console, no user recorded (log) |
| 15:20 | **Detection:** SolarWinds node-down alert, SCADA-PC1 (3 failed ICMP polls) |
| 15:22 | Substation 1 operator calls the NOC: loss of feeder telemetry |
| 15:29 | NOC Analyst opens TICKET-001, notifies Substation 1 and Ops Lead (initial P3) |
| 15:29 | **Escalation:** Ops Lead raises to SEV-2 (P2) and takes Incident Commander; 15-minute updates |
| — | **Bridge initiated:** none (see section 7) |
| ≈15:33 | Analyst finds SUB-R1 Ethernet0/1 administratively down |
| 15:34:20 | **Actions taken / Resolution:** Analyst re-enables Ethernet0/1 (`no shutdown`); service restored (log) |
| ≈15:35 | Reachability confirmed from the core (CORE-R2 → SCADA-PC1); late entry, recorded 16:17 |
| 15:44 | Substation 1 operator confirms telemetry on all feeders |
| 15:50 | Analyst status update; IC marks incident Resolved, monitoring (severity of record SEV-2) |
| 15:52 | Post-fix verification on SUB-R1 and log collection |
| 16:43 | Analyst submits RCA draft and observations to IC; Security escalation handed to IC |

---

## 3. Impact Analysis

### User Impact
- Substation 1 operators could not view feeder telemetry on SCADA-PC1 for about 14.5 minutes.

### System Impact
- SUB-R1 Ethernet0/1 (the only gateway for the Substation 1 SCADA LAN) was down; SCADA-PC1 was unreachable from the rest of the network.
- The change was logged with a time (15:19:49) but **no user identity**: the console requires no authentication.

### Business Impact
- Reduced situational awareness at a substation during the outage: a reliability and safety risk.
- Regulatory exposure: an unattributed configuration change on an operational technology (OT) device.

---

## 4. Root Cause

**Direct cause:** SUB-R1 Ethernet0/1 was manually shut down at the device console at 15:19:49 PST.

**Root cause (preliminary):** a configuration change was made on a SCADA network device **outside change control**, and the device's **console has no authentication**, so the change could neither be prevented nor attributed to a person.

5 Whys:
1. Why was SCADA-PC1 unreachable? Its gateway interface (SUB-R1 E0/1) was down.
2. Why was the interface down? Someone entered `shutdown` at the console.
3. Why could someone do that without approval? Console access requires no login, and no control enforces change approval on the device.
4. Why can't we tell who did it? No AAA / user accounting on the console.
5. Why wasn't the change caught before impact? No alerting on configuration changes; the first signal was the outage itself.

The final root cause depends on Security's review: who had console access, and whether the change was a mistake, unannounced field work, or unauthorized access.

---

## 5. Contributing Factors
- No user authentication or accounting (AAA) on the SUB-R1 console: changes can't be attributed.
- No configuration-change alerting: the change was only noticed through its impact.
- Device clocks not NTP-synchronized (`*` on log timestamps): multi-device timelines are less reliable.
- Single gateway for the Substation 1 SCADA LAN (SUB-R1 is single-homed to CORE-R2): no redundancy.
- Analyst reversed a manual change without first checking the logs or asking whether work was in progress (restore-first was correct at SEV-2; the 30-second check was skipped).

---

## 6. Detection & Monitoring Review
**How detected:** SolarWinds ICMP node-down alert at 15:20, 11 seconds after the fault and 2 minutes before the customer call.
**Monitoring gaps:**
- No alert on configuration changes (`%SYS-5-CONFIG_I`) or on an interface going administratively down; only the end host was monitored.
- Ticket opened at 15:29, about 9 minutes after the alert.
**Required improvements:**
- Forward device syslog to central logging and alert on configuration changes and `LINK-5-CHANGED ... administratively down` on SCADA infrastructure.
- Monitor SUB-R1 interfaces directly, not only the end host.
- Target: ticket opened within 5 minutes of a SCADA node-down alert.

---

## 7. Response Analysis

### What went well
- Monitoring detected the outage before the customer called.
- Analyst acknowledged the site and the Ops Lead within minutes.
- Fault isolated and service restored within about 15 minutes of the fault, using a Tier 1 standard change.
- Restoration verified on the device, by the customer and from the core.
- A forgotten log step was later recorded as a marked late entry, not inserted silently.
- Good evidence collection: before/after interface status and device log; correct UTC → PST conversion; observations (NTP, AAA) raised to leadership instead of changing them.

### What went poorly
- Initial severity under-classified (P3) without impact × urgency reasoning; corrected to SEV-2 by the IC.
- Control center visibility of Substation 1 was not confirmed (this decides SEV-1 vs SEV-2).
- Manual change reversed without a quick log/change check.
- Analyst proposed de-escalation to SEV-4 after restoration; the severity of record stays, and severity changes are the IC's call.
- First status update missed the 15-minute cadence; one update gave the restore time as 15:44 instead of 15:34.
- Written communications had typos and lacked next-update times.

### Bridge management review
- No bridge was opened. The playbook calls for a bridge at SEV-2. Because restoration came within 15 minutes, the IC managed the incident directly. **Finding:** the IC should open a bridge at SEV-2 declaration, or record the decision not to.

---

## 8. Corrective Actions (Fix Applied)
SUB-R1 Ethernet0/1 re-enabled (`no shutdown`) at 15:34:20 PST as a Tier 1 standard change. Verified up/up, customer-confirmed, and reachable from CORE-R2.

---

## 9. Preventative Actions (Long-Term Fix)
- Require authentication and accounting (AAA: TACACS+ with local fallback) on console and VTY lines of all routers and switches, so every change is tied to a person.
- Enable configuration change logging (`archive` / `log config`) and forward syslog to central logging with alerting.
- Synchronize all device clocks with NTP (keep UTC).
- Review physical and console-server access to substation equipment.
- Evaluate a redundant gateway path for SCADA LANs.
- NOC runbook: before reversing an administratively-down interface on OT equipment, check `show logging` and confirm with the IC or change calendar (no more than 1 minute).

These require approval by Network Engineering and Security leadership; they are recommendations, not changes made under this incident.

---

## 10. Lessons Learned
- "Administratively down" means a person made a change: restore first, but check the logs and ask before reversing it on OT equipment.
- Severity is impact × urgency; SCADA at a utility starts at SEV-2.
- Device logs are UTC and are the source of truth for times.
- Restored ≠ de-escalated: the incident moves to Resolved / Closed and keeps its severity of record.
- An outage that is fixed in 15 minutes can still have a root cause that needs Security.

---

## 11. Action Items & Owners

| Action Item | Owner | Due Date | Status | Closure Date |
|-------------|--------|----------|--------|--------------|
| Review console access to SUB-R1 at 15:19:49 PST; classify as error, unannounced work or security incident | Security team | 2026-11-20 | Open | |
| Standard for AAA (TACACS+ + local fallback) on all network devices | Network Engineering + Security | 2026-12-04 | Open | |
| NTP on all network devices (UTC) | Network Engineering | 2026-12-04 | Open | |
| Syslog to central logging; alert on config changes and admin-down on SCADA infrastructure | NOC Tooling / Monitoring | 2026-12-11 | Open | |
| Add "check logs + confirm before reversing admin-down on OT" to the NOC runbook | Ops Lead | 2026-11-25 | Open | |
| Clarify bridge requirement at SEV-2 in NOC procedures | Ops Lead | 2026-11-25 | Open | |
| Evaluate redundant gateway for SCADA LANs | Network Engineering | 2027-01-15 | Open | |

---

## 12. Customer Communications Review (Last 90 Days)

| Date | Incident | Severity | Customer Impact | Notes |
|------|----------|----------|------------------|-------|
| 2026-11-18 | TICKET-001 | SEV-2 | Substation 1 lost feeder telemetry ~14.5 min | First incident on the v2 lab; acknowledged 7 min after the call; restoration confirmed with the site |

---

## 13. Compliance / Regulatory Notes
- An unattributed configuration change on SCADA network equipment is relevant to utility security requirements for **access control, security event logging and configuration change management**. Security to determine whether reporting is required.
- Preserve SUB-R1 logs and configuration as evidence until Security closes its review.

---

## 14. Evidence / Attachments

**Before (≈15:33 PST):**
```
SUB-R1#show ip int bri
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            10.2.0.2        YES TFTP   up                    up
Ethernet0/1            10.2.10.1       YES TFTP   administratively down down
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  administratively down down
Loopback0              10.255.255.4    YES TFTP   up                    up
```

**After (15:52 PST):**
```
SUB-R1#show ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            10.2.0.2        YES TFTP   up                    up
Ethernet0/1            10.2.10.1       YES TFTP   up                    up
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  administratively down down
Loopback0              10.255.255.4    YES TFTP   up                    up
```
(E0/2 and E0/3 are unused and shut down by design.)

**SUB-R1 log (UTC):**
```
*Nov 18 23:06:11.692: %SYS-5-CONFIG_I: Configured from unix:config by console
*Nov 18 23:06:12.775: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
*Nov 18 23:06:13.776: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
*Nov 18 23:19:41.894: %SYS-5-CONFIG_P: Configured programmatically by process PnP Agent Discovery from console as vty0
*Nov 18 23:19:49.002: %SYS-5-CONFIG_I: Configured from console by console
*Nov 18 23:19:49.389: %LINK-5-CHANGED: Interface Ethernet0/1, changed state to administratively down
*Nov 18 23:19:50.389: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down
*Nov 18 23:34:20.635: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
*Nov 18 23:34:21.636: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
*Nov 18 23:34:49.721: %SYS-5-CONFIG_I: Configured from console by console
```

**Core-side verification (≈15:35 PST, late entry recorded 16:17):**
```
CORE-R2#ping 10.2.10.10
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/2 ms
```

Full incident record: [TICKET-001](./TICKET-001.md) · All communications: [TICKET-001 communications log](./TICKET-001-comms.md)

---

## 15. Follow-Up Review Date
**Scheduled Review Date:** 2026-11-20 (after the Security review)

---

## 16. Closure Date
**RCA Closure Date:** _Open, pending Security review and action-item owners_

---

[← Back to TICKET-001](TICKET-001.md) · [← Back to lab README](../README.md)
