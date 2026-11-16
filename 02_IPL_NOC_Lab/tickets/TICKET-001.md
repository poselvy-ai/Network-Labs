# TICKET-001 — SCADA-PC1 cannot reach DMZ web server (TCP/80)

| Field | Value |
|-------|-------|
| Priority | P3: one host, one service |
| Opened | 2026-11-16 14:38 |
| Reported by | Baseline verification, check #8 |
| Affected | SCADA-PC1 (10.2.10.10) → DMZ-SRV1 (172.16.50.10), TCP/80 |
| Status | Escalated to Network Engineering (2026-11-16 15:24) |

## Symptom

```
SCADA-PC1>telnet 172.16.50.10 80
Trying 172.16.50.10, 80 ...
% Connection timed out; remote host not responding
```

"Timed out" (not "refused") means nothing answered at all. Either the packets never arrive, the replies never come back, or something in the path drops them silently.

## Impact

Hosts behind both core routers cannot reach the DMZ (confirmed from SCADA-PC1, CORE-R2 and HQ-PC1). Inside → internet not yet tested.

## Expected path

| Hop | Device | In | Out | Expected decision |
|----:|--------|----|-----|-------------------|
| 1 | SCADA-PC1 | — | E0/0 | Default gateway 10.2.10.1 |
| 2 | SUB-R1 | E0/1 | E0/0 | O IA 172.16.50.0/24 via 10.2.0.1 |
| 3 | CORE-R2 | E0/3 | E0/0 | O 172.16.50.0/24 via 10.0.0.5 (FW1) |
| 4 | FW1 | Gi0/2 (inside2) | Gi0/3 (dmz) | Security 100 → 50 permitted, no NAT |
| 5 | DMZ-SRV1 | E0/0 | — | HTTP server listening on 80 |
| Return | DMZ-SRV1 → FW1 → CORE-R2 → SUB-R1 | | | Gateway 172.16.50.1; FW1 route 10.2.10.0/24 via 10.0.0.6 |

## Troubleshooting log

| Time | Device | Command | Result | Conclusion |
|------|--------|---------|--------|------------|
| 14:38 | SCADA-PC1 | `telnet 172.16.50.10 80` | Connection timed out | Symptom confirmed |
| 15:1x | SCADA-PC1 | `ping 10.2.10.1` | 5/5 | Host ↔ gateway OK (hop 1) |
| 15:1x | SCADA-PC1 | `traceroute 172.16.50.10` | Hop 1 10.2.10.1, hop 2 10.2.0.1, then `* * *` | Path OK to CORE-R2. Stars past CORE-R2 are expected (ASA doesn't show in traceroute by default), so not proof of a drop on their own |
| 15:1x | CORE-R2 | `show ip ospf neighbor` | CORE-R1, FW1 (10.255.255.254), RENO-R1, SUB-R1 all FULL | OSPF healthy, including to FW1 |
| 15:1x | CORE-R2 | `show ip route` | 172.16.50.0/24 present (OSPF) | Route to DMZ exists |
| 15:1x | CORE-R2 | `ping 172.16.50.10` | 0/5 | Fails one hop from FW1 |
| 15:1x | HQ-PC1 | `ping 172.16.50.10` | 0/5 | Fails from a second site via inside1 too |
| 15:1x | FW1 | `ping 172.16.50.10`, `10.2.10.10`, `10.255.255.2`, `10.255.255.4` | All 5/5 | FW1 reaches both sides itself |

Note: pinging 172.16.50.1 *from SCADA-PC1* fails by design. An ASA only answers pings on the interface the ping arrives on.

### Evidence still to attach (FW1)
- [ ] `packet-tracer input inside2 icmp 10.2.10.10 8 0 172.16.50.10 detail`
- [ ] `show asp drop`
- [ ] `capture CAP1 interface dmz match ip host 10.2.10.10 host 172.16.50.10` → ping from SCADA-PC1 → `show capture CAP1`
- [ ] Scope: `ping 8.8.8.8` from HQ-PC1 (does inside → outside work?)

## Root cause

_Pending engineering._ Fault isolated to traffic transiting FW1 between inside interfaces and the DMZ.

## Escalation note

**To:** Network Engineering / Firewall team
**Ticket:** TICKET-001, P3
**Summary:** Internal hosts cannot reach DMZ-SRV1 (172.16.50.10) through FW1. Confirmed from SCADA-PC1 (substation, via inside2), CORE-R2 (directly attached to inside2) and HQ-PC1 (HQ, via inside1).

**Ruled out:**
- Host and gateway: SCADA-PC1 → 10.2.10.1 OK.
- Internal routing: CORE-R2 has FULL OSPF adjacencies with CORE-R1, FW1, RENO-R1 and SUB-R1, and an OSPF route to 172.16.50.0/24. Traceroute reaches CORE-R2.
- FW1 reachability: FW1 itself pings DMZ-SRV1, SCADA-PC1 and the router loopbacks successfully, so the DMZ link, the inside links and DMZ-SRV1 are up.

**Isolated to:** traffic *through* FW1, inside1/inside2 (security 100) → dmz (security 50). Both inside interfaces affected, so not a single link or core router.

**Suspected areas (for engineering):** firewall policy / ICMP and TCP inspection / NAT handling for inside → dmz.

**Requested:** firewall review of inside → dmz flows. Packet-tracer, ASP drop counters and DMZ capture output attached below once collected.

## Lesson learned

_TBD_
