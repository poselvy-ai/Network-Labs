[← Back to tickets](README.md) · [← Back to lab README](../README.md)

# TICKET-002 — EDGE-R1 BGP session to ISP-A down (Established → Idle)

| Field | Value |
|-------|-------|
| Severity | **SEV-3 (P3)**: impact Low × urgency Medium |
| Opened | 2026-11-18 17:40 PST |
| Reported by | SolarWinds alerts (17:40, 17:41) |
| Affected | Internet edge: EDGE-R1 ↔ ISP-A (203.0.113.0/30, AS 65100) |
| Analyst | Patrick Selvy, NOC Analyst 1 |
| Status | **In progress** |

> Times are **PST (UTC−8)**. **Work notes** carry the time the analyst entered them. **Device logs** are UTC and are the record of equipment events.

## Symptom

**17:40 PST, SolarWinds:**
> BGP peer state change: EDGE-R1 → neighbor 203.0.113.1 (ISP-A, AS 65100). Established → Idle.

**17:41 PST, SolarWinds:**
> Interface traffic shift: EDGE-R1 Ethernet0/1 (to ISP-B) inbound traffic up 100% over baseline. Ethernet0/0 (to ISP-A) traffic dropped to 0.

No calls from users or sites.

## Impact

No user impact observed: internet reachable from the NOC. Internet edge running on the backup ISP only (**redundancy lost**).

## Severity history

| Time (PST) | SEV | Set by | Reason |
|------------|-----|--------|--------|
| 17:51 | SEV-3 (P3) | Analyst | Impact Low × Urgency Medium |

## Communications

Full emails and replies: **[TICKET-002 communications log](./TICKET-002-comms.md)**

| # | Time | From → To | Subject / type |
|---|------|-----------|----------------|
| [C1](./TICKET-002-comms.md#c1) | 17:51 | Analyst → Ops Lead | EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Investigating |

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

## Troubleshooting log

| Time (PST) | Device | Command | Result | Conclusion |
|------------|--------|---------|--------|------------|
| 17:51 | HQ-PC1 (NOC) | `ping 8.8.8.8` | 5/5, 6/17/33 ms | Internet reachable from the NOC |
| 17:51 | HQ-PC1 (NOC) | `ping 172.16.50.10` | 5/5 | DMZ web server reachable |
| 17:57 | HQ-PC1 (NOC) | `traceroute 8.8.8.8` | Hop 1 10.0.10.1 (CORE-R1), then `* * *` to hop 30 | Expected: FW1 (ASA) hides itself and doesn't pass ICMP time-exceeded from outside routers, so a trace from inside can't show the ISP path. Not a fault |
| 17:57 | HQ-PC1 (NOC) | `ping 8.8.8.8` | 5/5, 4/12/29 ms | Internet (AS 65300) still reachable |

## Root cause
_TBD_

## Resolution / escalation
_TBD_

---

[← Back to tickets](README.md) · [← Back to lab README](../README.md)
