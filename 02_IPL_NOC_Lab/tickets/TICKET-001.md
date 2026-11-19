[← Back to tickets](README.md) · [← Back to lab README](../README.md)

# TICKET-001 — Substation 1 SCADA workstation unreachable (loss of telemetry)

| Field | Value |
|-------|-------|
| Severity | **SEV-2 (P2)**, severity of record. Impact Moderate (one site, critical OT function) × Urgency Medium–High |
| Opened | 2026-11-18 15:20 PST |
| Reported by | SolarWinds node-down alert (15:20); Substation 1 operator call (15:22) |
| Affected | SCADA-PC1 (10.2.10.10), Substation 1 feeder telemetry |
| Analyst | Patrick Selvy, NOC Analyst 1 |
| Incident Commander | Ops Lead |
| Status | **Resolved, monitoring.** Service restored 15:34 PST. Security escalation with IC; [RCA](./TICKET-001-RCA.md) open pending Security review |

> Times are **PST (UTC−8)**. **Work notes** carry the time the analyst entered them (the ticket's record of analyst actions). **Device logs** are UTC and are the record of equipment events. Times marked ≈ are reconstructed from the order of events.

## Symptom

**15:20 PST, Monitoring (SolarWinds):**
> Node DOWN: SCADA-PC1 (10.2.10.10). ICMP polls failing, 3 consecutive. Site: Substation 1.

**15:22 PST, Substation 1 operator (phone):**
> "Our SCADA workstation lost its connection to the control center. We can't see the feeder telemetry from here. Nothing changed on our end. How long until it's back?"

## Impact

Substation 1 operators lost local visibility of feeder telemetry for about 14.5 minutes. Other sites unaffected.

## Severity history

| Time (PST) | SEV | Set by | Reason |
|------------|-----|--------|--------|
| 15:29 | P3 (SEV-3) | Analyst | Initial classification: "Substation 1 loss of telemetry" |
| 15:29 | **SEV-2 (P2)** | Ops Lead / IC | SCADA telemetry is a critical OT function at a utility. Impact Moderate × Urgency Medium–High. Raise to SEV-1 if the control center also lost visibility |
| 15:50 | SEV-2 kept as severity of record; status → Resolved, monitoring | Ops Lead / IC | Analyst recommended de-escalation to SEV-4 after restore. IC: restored ≠ de-escalated; severity of record stays SEV-2 (RCA required) |

## Communications

Full emails and replies, in order: **[TICKET-001 communications log](./TICKET-001-comms.md)**

| # | Time | From → To | Subject / type |
|---|------|-----------|----------------|
| [C1](./TICKET-001-comms.md#c1) | 15:29 | Analyst → Substation 1 | Initial notification |
| [C2](./TICKET-001-comms.md#c2) | 15:29 | Analyst → Ops Lead | Initial notification (P3) |
| [C3](./TICKET-001-comms.md#c3) | 15:29 | Ops Lead → Analyst | Raised to SEV-2, IC assigned |
| [C4](./TICKET-001-comms.md#c4) | 15:44 | Analyst → Substation 1 | Port restored, please verify |
| [C5](./TICKET-001-comms.md#c5) | 15:44 | Substation 1 → Analyst | Telemetry restored |
| [C6](./TICKET-001-comms.md#c6) | 15:45 | Ops Lead → Analyst | Logs / change check query |
| [C7](./TICKET-001-comms.md#c7) | 15:45 | Analyst → Ops Lead | Restore first at SEV-2 |
| [C8](./TICKET-001-comms.md#c8) | 15:50 | Analyst → Ops Lead | Status update, proposed SEV-4 |
| [C9](./TICKET-001-comms.md#c9) | 15:50 | Ops Lead → Analyst | Resolved, monitoring; RCA required |
| [C10](./TICKET-001-comms.md#c10) | 16:43 | Analyst → Ops Lead | Restoration and closure + RCA draft |
| [C11](./TICKET-001-comms.md#c11) | 16:49 | Ops Lead → Analyst | RCA review; escalated to Security |

## Work notes (as entered by the analyst)

System-timestamped at the time each note was entered (PST), as a ticketing system would record them. Verbatim.

**2026-11-18 15:20 · Patrick Selvy** (pre-incident check)
```
Step 1 sucessussfull
HQ-PC1>ping 10.2.10.10
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 2/2/2 ms
HQ-PC1>ping 172.16.50.10
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/202/1004 ms
HQ-PC1>ping 8.8.8.8
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/202/1005 ms
Moving to the ticket
```

**2026-11-18 15:29 · Patrick Selvy** (severity + notifications)
```
1. Substation 1 Loss of telemetry
2. Initial P3 - Moderate
[Initial notifications to Substation 1 and Ops Lead: see Communications]
```

**2026-11-18 15:35 · Patrick Selvy**
```
SUB-R1#show ip int bri
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            10.2.0.2        YES TFTP   up                    up
Ethernet0/1            10.2.10.1       YES TFTP   administratively down down
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  administratively down down
Loopback0              10.255.255.4    YES TFTP   up                    up

Interface E0/1 is Admin Down.
```

**2026-11-18 15:44 · Patrick Selvy** (resolution message to Substation 1: see Communications)

**2026-11-18 15:45 · Patrick Selvy**
```
As we are at Sev-2 the priority is to restore service first.
```

**2026-11-18 15:50 · Patrick Selvy** (status update to Ops Lead: see Communications)

**2026-11-18 15:52 · Patrick Selvy**
```
SUB-R1#show ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            10.2.0.2        YES TFTP   up                    up
Ethernet0/1            10.2.10.1       YES TFTP   up                    up
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  administratively down down
Loopback0              10.255.255.4    YES TFTP   up                    up

Interface change Logs
SUB-R1#show logging | include Ethernet0/1|CONFIG
[device log: see Device log below]
```

**2026-11-18 15:55 · Patrick Selvy**
```
Logs are UTC by design (common practice: UTC is constant). Will note for leadership; not proposing a change.
```

**2026-11-18 16:17 · Patrick Selvy** (late entry: ping run ≈15:35, after restore)
```
CORE-R2#ping 10.2.10.10
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/2 ms
```

**2026-11-18 16:43 · Patrick Selvy**
```
To: Ops Lead
Subject: TICKET-001: SCADA workstation connectivity, restoration and closure.
After restoration of service, I was able to find the SUB-R1 port E0/1 was admined down on
"*Nov 18 23:19:50.389: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down".
Unfortunately, I am unable to proceed further as the console does not need "User Authentication" to log in.
Also, the "*" indicates that the time is set to the device time not NTP time. I am not sure if these are by
designed or not and will leave escalation in your hands.
I have filled out the initial part of the RCA, for you to go over and fill in the rest.
[RCA draft attached: TICKET-001-RCA-analyst-draft.txt]
```

## Troubleshooting log

| Time (PST) | Device | Command | Result | Conclusion |
|------------|--------|---------|--------|------------|
| before 15:19:49 | HQ-PC1 | `ping 10.2.10.10`, `ping 172.16.50.10`, `ping 8.8.8.8` (pre-incident baseline) | All OK (first-packet ARP loss only) | Network healthy before the fault |
| ≈15:33 (before 15:34:20) | SUB-R1 | `show ip interface brief` | **Ethernet0/1 (10.2.10.1) administratively down / down**. E0/2, E0/3 admin down (unused, by design) | SCADA LAN gateway interface manually shut down |
| 15:34:20 (device log) | SUB-R1 | `interface Ethernet0/1` → `no shutdown` | Interface up | Standard change (Tier 1 runbook). Service restored |
| ≈15:35 per analyst (late entry, recorded 16:17) | CORE-R2 | `ping 10.2.10.10` | `.!!!!` 4/5, 1/1/2 ms | Reachable from the core across the WAN. First packet lost to ARP (normal). Ping output has no timestamp; time per analyst, consistent with running right after the 15:34:20 restore |
| 15:52 | SUB-R1 | `show ip interface brief` | Ethernet0/1 up / up | Fix verified on the device |
| 15:52 | SUB-R1 | `show logging \| include Ethernet0/1\|CONFIG` | See below | Fault time and source identified |


Note: the ticket would be stronger with the path evidence that led to SUB-R1 (`ping` / `traceroute 10.2.10.10` from the NOC side stopping at 10.2.0.2).

### Device log (SUB-R1, UTC)

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

| UTC | PST | Event |
|-----|-----|-------|
| 23:06:11 | 15:06 | Device boot, startup config loaded, E0/1 up (normal) |
| 23:19:41 | 15:19 | IOS-XE PnP agent at startup (normal) |
| **23:19:49** | **15:19:49** | **Configured from console (no username); E0/1 administratively down: the fault** |
| 23:34:20 | **15:34:20** | E0/1 up: service restored (NOC `no shutdown`) |
| 23:34:49 | 15:34 | NOC exits config mode |

## Incident timeline (PST)

| Time | Event | Source |
|------|-------|--------|
| 15:19:49 | SUB-R1 E0/1 shut down from console | SUB-R1 log (23:19:49 UTC) |
| 15:20 | SolarWinds: SCADA-PC1 node down | Monitoring |
| 15:22 | Substation 1 operator reports loss of telemetry | Phone |
| 15:29 | Initial notifications to Substation 1 and Ops Lead; IC sets SEV-2 | Comms |
| ≈15:33 | Analyst finds SUB-R1 E0/1 administratively down | `show ip int brief` |
| **15:34:20** | **E0/1 re-enabled, service restored** | SUB-R1 log (23:34:20 UTC) |
| ≈15:35 | Reachability confirmed from the core (CORE-R2 → SCADA-PC1) | `ping`; late entry, recorded 16:17 |
| 15:44 | Operator confirms telemetry on all feeders | Phone |
| 15:50 | Status update to Ops Lead; IC marks Resolved, monitoring | Comms |
| 15:52 | Post-fix verification and log collection | `show ip int brief`, `show logging` |
| 16:43 | RCA draft and findings sent to IC; Security escalation handed to IC | Comms |

**Key measures:** time to detect 11 s · time to acknowledge (customer) ≈7 min after the call · time to restore 14 min 31 s from fault (14 min 20 s from alert).

## Findings

1. **Cause:** SUB-R1 Ethernet0/1 (SCADA LAN gateway, 10.2.10.1/24) was manually shut down from the device console at **15:19:49 PST** (23:19:49 UTC). SolarWinds alerted 11 seconds later.
2. **No approved change** is known for SUB-R1 at that time.
3. **Attribution not possible:** the log reads "Configured from console by console". The console has no user authentication, so the person who made the change can't be identified.
4. **Outage duration:** 15:19:49 → 15:34:20 PST, **about 14.5 minutes**.
5. **Correction:** the 15:50 update gave the restore time as 15:44. The device log shows **15:34:20**. 15:44 is when the operator confirmed.

## Observations for leadership (not change proposals: may be by design)

- Device logs are in UTC. Consistent with common practice; the NOC converts to local time in tickets.
- Log timestamps carry `*`, meaning the device clock is **not NTP-synchronized**. Recommend leadership review the NTP standard so multi-device timelines can be trusted.
- Console changes are not attributed to a user. Recommend leadership review AAA (TACACS+ / local users) and change logging for substation routers.

## Resolution

Re-enabled SUB-R1 Ethernet0/1 (`no shutdown`) at 15:34 PST, a Tier 1 standard change. Verified: interface up/up; operator confirmed all feeders visible.

## Escalation (open)

**16:43:** Analyst reported the findings to the IC (log evidence, no console authentication, unsynchronized clock) and left the escalation decision with the IC, since the settings may be by design. **IC escalated to the Security team:** unattributed console change on SUB-R1 (SCADA network) at 15:19:49 PST with no approved change; review console access and assess as a possible security incident.

## RCA

**Required** (SEV-2). [TICKET-001 RCA](./TICKET-001-RCA.md): drafted by the analyst ([original draft](./TICKET-001-RCA-analyst-draft.txt)), reviewed and completed by the IC. Preliminary root cause: unapproved console change on a device with no console authentication. Final root cause pending the Security review (2026-11-20).

## Coaching notes (lab)

- Show the severity math (impact × urgency) and use the playbook terms SEV-X (PX).
- Each communication: what, impact, SEV + ticket, action, **next update time**. Proofread before sending.
- Restored ≠ de-escalated: status moves to Resolved / Monitoring / Closed; the severity of record stays. The IC makes severity and closure calls; the analyst recommends.
- "Administratively down" = a person did it. Restore first at SEV-2, but on OT networks a 30-second log/change check before reversing a manual change is good practice.
- Read log timestamps as UTC and convert. Verify times against device logs before putting them in an update.
- Record observations for leadership; don't propose changes to things that may be by design.

---

[← Back to tickets](README.md) · [← Back to lab README](../README.md)
