# TICKET-001 — SCADA-PC1 cannot reach DMZ web server (TCP/80)

| Field | Value |
|-------|-------|
| Priority | P3: one host, one service |
| Opened | 2026-11-16 14:38 |
| Reported by | Baseline verification, check #8 |
| Affected | SCADA-PC1 (10.2.10.10) → DMZ-SRV1 (172.16.50.10), TCP/80 |
| Status | Open |

## Symptom

```
SCADA-PC1>telnet 172.16.50.10 80
Trying 172.16.50.10, 80 ...
% Connection timed out; remote host not responding
```

"Timed out" (not "refused") means nothing answered at all. Either the packets never arrive, the replies never come back, or something in the path drops them silently.

## Impact

The substation host can't reach the DMZ web service. Other flows not yet tested.

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
| | SCADA-PC1 | `ping 10.2.10.1` | | |
| | SCADA-PC1 | `ping 172.16.50.10` | | |
| | SCADA-PC1 | `traceroute 172.16.50.10` | | |
| | SUB-R1 | `show ip route 172.16.50.0` | | |
| | CORE-R2 | `show ip route 172.16.50.0` | | |
| | FW1 | `show route 172.16.50.0` / `show route 10.2.10.0` | | |
| | FW1 | `packet-tracer input inside2 tcp 10.2.10.10 12345 172.16.50.10 80` | | |
| | DMZ-SRV1 | `show ip http server status` | | |
| | DMZ-SRV1 | `ping 172.16.50.1` | | |

Note: pinging 172.16.50.1 *from SCADA-PC1* fails by design. An ASA only answers pings on the interface the ping arrives on.

## Root cause

_TBD_

## Resolution / escalation note

_TBD_

## Lesson learned

_TBD_
