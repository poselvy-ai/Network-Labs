[← Back to tickets](README.md) · [← Back to lab README](../README.md)

# TICKET-002 — EDGE-R1 BGP session to ISP-A down (Established → Idle)

| Field | Value |
|-------|-------|
| Severity | **SEV-3 (P3)**: impact Low × urgency Medium |
| Opened | 2026-11-18 17:40 PST |
| Reported by | SolarWinds alerts (17:40, 17:41) |
| Affected | Internet edge: EDGE-R1 ↔ ISP-A (203.0.113.0/30, AS 65100) |
| Analyst | Patrick Selvy, NOC Analyst 1 |
| Status | **Resolved, pending closure.** Carrier fix (ISPA-INC-88213); ISP-A BGP restored 18:37:28 PST; verified 18:43 |

> Times are **PST (UTC−8)**. **Work notes** carry the time the analyst entered them. **Device logs** are UTC and are the record of equipment events.

## Symptom

**17:40 PST, SolarWinds:**
> BGP peer state change: EDGE-R1 → neighbor 203.0.113.1 (ISP-A, AS 65100). Established → Idle.

**17:41 PST, SolarWinds:**
> Interface traffic shift: EDGE-R1 Ethernet0/1 (to ISP-B) inbound traffic up 100% over baseline. Ethernet0/0 (to ISP-A) traffic dropped to 0.

No calls from users or sites.

## Impact

No user impact observed: internet reachable from the NOC. Internet edge running on the backup ISP only (**redundancy lost**). Failover to ISP-B confirmed (18:18).

## Severity history

| Time (PST) | SEV | Set by | Reason |
|------------|-----|--------|--------|
| 17:51 | SEV-3 (P3) | Analyst | Impact Low × Urgency Medium |

## Communications

Full emails and replies: **[TICKET-002 communications log](./TICKET-002-comms.md)**

| # | Time | From → To | Subject / type |
|---|------|-----------|----------------|
| [C1](./TICKET-002-comms.md#c1) | 17:51 | Analyst → Ops Lead | EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Investigating |
| [C2](./TICKET-002-comms.md#c2) | 18:25 | Analyst → ISP-A NOC | Carrier ticket opened: BGP IP unreachable |
| [C3](./TICKET-002-comms.md#c3) | 18:28 | ISP-A NOC → Analyst | ISPA-INC-88213 opened; circuit ID and details requested |
| [C4](./TICKET-002-comms.md#c4) | 18:28 | Ops Lead → Analyst | 18:21 update overdue |
| [C5](./TICKET-002-comms.md#c5) | 18:30 | Analyst → Ops Lead | Working with carrier; circuit ID requested |
| [C6](./TICKET-002-comms.md#c6) | 18:32 | Ops Lead → Analyst | Circuit ID and account provided; next update 19:00 |
| [C7](./TICKET-002-comms.md#c7) | 18:36 | Analyst → ISP-A NOC | Circuit ID, account, AS numbers |
| [C8](./TICKET-002-comms.md#c8) | 18:38 | ISP-A NOC → Analyst | Their interface was shut in error during maintenance; restored 18:37; please confirm |
| [C9](./TICKET-002-comms.md#c9) | 18:45 | Ops Lead → Analyst | Traceroute from inside stops at hop 1 by design |

## Work notes (as entered by the analyst)

**2026-11-18 17:51 · Patrick Selvy**
```
Acknowledge Solarwinds Alerts at 17:40 BGP peer state change: EDGE-R1 → neighbor 203.0.113.1 (ISP-A, AS 65100). Established → Idle.
Acknowledge SolareWinds Alert at 17:41 Interface traffic shift: EDGE-R1 Ethernet0/1 (to ISP-B) inbound traffic up 100% over baseline. Ethernet0/0 (to ISP-A) traffic dropped to 0.

Able to ping both internet and and DMZ gateway

LOG
HQ-PC1>ping 8.8.8.8
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 6/17/33 ms
HQ-PC1>ping 172..16.50.10
% Unrecognized host or address, or protocol not running.
HQ-PC1>ping 172.16.50.10
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/8/18 ms

TICKET-002 - SEV-3
Medium Urgency/ Low Impact
```

**2026-11-18 17:57 · Patrick Selvy**
```
Trace route failed on HQ-PC1 but ping was able to go through on AS 65300

LOG
HQ-PC1>traceroute 8.8.8.8
Tracing the route to 8.8.8.8
  1 10.0.10.1 2 msec 0 msec 1 msec
  2  *  *  *
  [hops 3-30: *  *  *]
HQ-PC1>ping 8.8.8.8
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/12/29 ms
```

**2026-11-18 18:14 · Patrick Selvy**
```
Assessment. We have a BGP configuration problem on ISP-A port E0/0. Next step will be to either escalate to the carrier as they would fix correct the BGP issue on their router

EDGE-R1 -> ISP-A
Neighbor 203.0.113.1 shows flapping.
Int E0/2 (203.0.113.2/30) is UP
Unable to ping ISP-A E0/0 ISP-A eBGP AS 65011

LOG
EDGE-R1#show log | include BGP | neighbor 203.0.113.1
*Nov 18 23:04:53.326: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Up
*Nov 19 01:34:42.390: %BGP-3-NOTIFICATION: sent to neighbor 203.0.113.1 4/0 (hold time expired) 0 bytes
*Nov 19 01:34:42.392: %BGP-5-NBR_RESET: Neighbor 203.0.113.5 reset (BGP Notification sent)
*Nov 19 01:34:42.392: %BGP-5-NBR_RESET: Neighbor 203.0.113.1 reset (BGP Notification sent)
*Nov 19 01:34:42.440: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Down BGP Notification sent
*Nov 19 01:34:42.440: %BGP_SESSION-5-ADJCHANGE: neighbor 203.0.113.1 IPv4 Unicast topology base removed from session  BGP Notification sent
*Nov 19 01:34:42.440: %BGP-5-ADJCHANGE: neighbor 203.0.113.5 Down BGP Notification sent
*Nov 19 01:34:42.440: %BGP_SESSION-5-ADJCHANGE: neighbor 203.0.113.5 IPv4 Unicast topology base removed from session  BGP Notification sent
*Nov 19 01:34:49.619: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Up

EDGE-R1#show ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            203.0.113.2     YES TFTP   up                    up
Ethernet0/1            203.0.113.6     YES TFTP   up                    up
Ethernet0/2            198.51.100.1    YES TFTP   up                    up
Ethernet0/3            unassigned      YES unset  administratively down down
*Nov 19 01:38:51.788: %BGP-3-NOTIFICATION: sent to neighbor 203.0.113.1 4/0 (hold time expired) 0 bytes
*Nov 19 01:38:51.788: %BGP-5-NBR_RESET: Neighbor 203.0.113.1 reset (BGP Notification sent)
*Nov 19 01:38:51.788: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Down BGP Notification sent
*Nov 19 01:38:51.788: %BGP_SESSION-5-ADJCHANGE: neighbor 203.0.113.1 IPv4 Unicast topology base removed from session  BGP Notification sent

EDGE-R1#Ping 203.0.113.1
.....
Success rate is 0 percent (0/5)

EDGE-R1 ->ISP-B
ISP-B to EDGE-R1 is Up - cant find its neighbor.
EDGE-R1 port E0/1 - is UP
Successfully ping ISP-B eBGP AS65200

LOG:
EDGE-R1#show log | include BGP | neighbor 203.0.113.6
[same 01:34:42 and 01:38:51 entries as above]

EDGE-R1#Ping 203.0.113.6
!!!!!
```

**2026-11-18 18:18 · Patrick Selvy**
```
Logs:
EDGE-R1#show bgp sum
% Command accepted but obsolete, unreleased or unsupported; see documentation.
BGP router identifier 198.51.100.1, local AS number 65001
[...]
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
203.0.113.1     4        65100       0       0        1    0    0 00:38:08 Active
203.0.113.5     4        65200      53      51       20    0    0 00:42:10        4
EDGE-R1#show ip bgp 8.8.8.8
BGP routing table entry for 8.8.8.8/32, version 18
Paths: (1 available, best #1, table default)
  65200 65300
    203.0.113.5 from 203.0.113.5 (100.64.2.1)
      Origin IGP, localpref 100, valid, external, best
      Updated on Nov 19 2026 01:35:20 UTC
EDGE-R1#ping 203.0.113.5
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

**2026-11-18 18:43 · Patrick Selvy**
```
EDGE-R1 ->ISP-A BGP has recovered after carrier fix.
Still not able to traceroute from the HQ-PC1 to 8.8.8.8 this could be by design will ask ops leads.
C72 carrier ticket.

Logs
EDGE-R1#show ip bgp summary
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
203.0.113.1     4        65100      11       5       20    0    0 00:00:25        4
203.0.113.5     4        65200      76      73       24    0    0 01:03:04        4

EDGE-R1#show ip bgp 8.8.8.8
Paths: (2 available, best #1, table default)
  65100 65300
    203.0.113.1 from 203.0.113.1 (100.64.1.1)
      Origin IGP, localpref 200, valid, external, best
      Updated on Nov 19 2026 02:37:28 UTC
  65200 65300
    203.0.113.5 from 203.0.113.5 (100.64.2.1)
      Origin IGP, localpref 100, valid, external
      Updated on Nov 19 2026 01:35:20 UTC

EDGE-R1#Ping 203.0.113.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

EDGE-R1# show log | include bgp |neighbor 203.0.113.1
[... 01:34:42 and 01:38:51 entries as before ...]
*Nov 19 02:37:28.455: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Up

HQ-PC1>ping 8.8.8.8
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/13/31 ms
```

## Troubleshooting log

| Time (PST) | Device | Command | Result | Conclusion |
|------------|--------|---------|--------|------------|
| 17:51 | HQ-PC1 (NOC) | `ping 8.8.8.8` | 5/5, 6/17/33 ms | Internet reachable from the NOC |
| 17:51 | HQ-PC1 (NOC) | `ping 172.16.50.10` | 5/5 | DMZ web server reachable |
| 17:57 | HQ-PC1 (NOC) | `traceroute 8.8.8.8` | Hop 1 10.0.10.1 (CORE-R1), then `* * *` to hop 30 | Expected: FW1 (ASA) hides itself and doesn't pass ICMP time-exceeded from outside routers, so a trace from inside can't show the ISP path. Not a fault |
| 17:57 | HQ-PC1 (NOC) | `ping 8.8.8.8` | 5/5, 4/12/29 ms | Internet (AS 65300) still reachable |
| 18:14 | EDGE-R1 | `show logging \| include BGP` | 01:34:42 UTC (17:34:42 PST): hold time expired to .1, **both** ISP-A and ISP-B sessions reset; .1 back Up 01:34:49. **01:38:51 UTC (17:38:51 PST): hold time expired to 203.0.113.1, session Down** | ISP-A session down since 17:38:51 PST (BGP hold timer = 180 s, so traffic from ISP-A stopped ≈17:35:51). Earlier reset of both sessions at 17:34:42 unexplained |
| 18:14 | EDGE-R1 | `show ip interface brief` | E0/0 (203.0.113.2, to ISP-A) up/up; E0/1 (203.0.113.6, to ISP-B) up/up | Our side of the ISP-A link is up |
| 18:14 | EDGE-R1 | `ping 203.0.113.1` | 0/5 | No IP reachability to the ISP-A peer across an up/up link: points to the carrier side |
| 18:14 | EDGE-R1 | `ping 203.0.113.6` | 5/5 | This is EDGE-R1's **own** E0/1 address; does not test ISP-B (peer is 203.0.113.5) |
| 18:18 | EDGE-R1 | `show bgp summary` (`show ip bgp summary` is the current form) | ISP-A 203.0.113.1: **Active**, 0 msgs, down 00:38:08. ISP-B 203.0.113.5: **Established**, up 00:42:10, **4 prefixes** received | ISP-A session down (Active = retrying, no TCP session). ISP-B healthy |
| 18:18 | EDGE-R1 | `show ip bgp 8.8.8.8` | 1 path: AS path 65200 65300 via **203.0.113.5**, localpref 100, best | **Failover confirmed:** internet traffic using ISP-B. Normal ISP-A path (localpref 200) gone |
| 18:18 | EDGE-R1 | `ping 203.0.113.5` | 5/5 | ISP-B peer reachable |
| 18:43 | EDGE-R1 | `show ip bgp summary` | ISP-A 203.0.113.1 **Established**, 4 prefixes (up 00:00:25 at time of command); ISP-B Established, 4 prefixes | Both sessions up: redundancy restored |
| 18:43 | EDGE-R1 | `show ip bgp 8.8.8.8` | 2 paths; **best via 203.0.113.1** (65100 65300, localpref 200); backup via 203.0.113.5 (localpref 100) | Traffic back on the primary ISP as designed |
| 18:43 | EDGE-R1 | `ping 203.0.113.1` | 5/5 | ISP-A peer reachable |
| 18:43 | EDGE-R1 | `show logging \| include bgp` | `02:37:28 UTC: neighbor 203.0.113.1 Up` | **ISP-A session restored 18:37:28 PST** |
| 18:43 | HQ-PC1 (NOC) | `ping 8.8.8.8` | 5/5 | End-to-end verified from the NOC |

## Incident timeline (PST)

| Time | Event | Source |
|------|-------|--------|
| 17:34:42 | Both BGP sessions reset at once (cause unexplained; observation only) | EDGE-R1 log (01:34:42 UTC) |
| ≈17:35:51 | ISP-A shuts its customer-facing interface in error during maintenance (session hold timer 180 s → last keepalive) | Carrier (C8) + hold-timer arithmetic |
| 17:38:51 | BGP to ISP-A down (hold time expired); traffic fails over to ISP-B | EDGE-R1 log (01:38:51 UTC) |
| 17:40–17:41 | SolarWinds alerts: BGP peer Idle; traffic shift to ISP-B | Monitoring |
| 17:51 | Ticket opened, SEV-3; internet verified from the NOC | Work note |
| 18:18 | Failover to ISP-B confirmed; ISP-A peer unreachable across an up/up link | Work note |
| 18:25 | Carrier ticket opened with ISP-A (ISPA-INC-88213 at 18:28) | C2, C3 |
| 18:36 | Circuit ID and account provided to carrier | C7 |
| **18:37:28** | **ISP-A re-enables its interface; BGP to ISP-A Established** | EDGE-R1 log (02:37:28 UTC) |
| 18:43 | Verified: both sessions up, primary path via ISP-A, internet OK from the NOC | Work note |

**Key measures:** loss of redundancy 58 min 37 s (17:38:51 → 18:37:28); user impact none; time from alert to carrier ticket ≈45 min (≈8 min of that waiting on the circuit ID).

## Root cause
Carrier-side: ISP-A shut down its customer-facing interface toward IPL in error during unrelated maintenance on its PE router (per ISP-A, C8). Carrier RFO due within 2 business days.

## Resolution / escalation
_TBD_

---

[← Back to tickets](README.md) · [← Back to lab README](../README.md)
