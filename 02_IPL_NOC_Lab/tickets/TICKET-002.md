# TICKET-002 — DMZ-SRV1 HTTP service refuses connections (TCP/80)

| Field | Value |
|-------|-------|
| Priority | P3: one service, one server |
| Opened | 2026-11-16 16:10 |
| Reported by | Verification of [TICKET-001](./TICKET-001.md) |
| Affected | DMZ-SRV1 (172.16.50.10 / public 198.51.100.10), HTTP TCP/80 |
| Assigned | Server team |
| Status | Open, escalated to server team |

## Symptom

```
SCADA-PC1>telnet 172.16.50.10 80
Trying 172.16.50.10, 80 ...
% Connection refused by remote host
```

Same result from CORE-R2 and from DMZ-SRV1 itself.

"Refused" (not "timed out") means the TCP connection reached the server and the server rejected it. The network path is not the problem.

## Already ruled out (from TICKET-001)

- Routing and firewall: ICMP from SCADA-PC1 and HQ-PC1 to 172.16.50.10 succeeds; FW1 packet-tracer allows inside → dmz.
- Server return path: fixed in TICKET-001 (default route via 172.16.50.1).

## Log

| Time | Device | Command | Result | Conclusion |
|------|--------|---------|--------|------------|
| 16:03 | DMZ-SRV1 | `show running-config \| include http` | `ip http server`, `ip http secure-server` | HTTP configured |
| 16:03 | DMZ-SRV1 | `show ip http server status \| include status\|port` | HTTP enabled, port 80; HTTPS enabled, port 443 | Service enabled |
| 16:03 | DMZ-SRV1 | `show tcp brief all` | `0.0.0.0.80`, `::.80`, `0.0.0.0.443` LISTEN; `0.0.0.0.22` LISTEN with no TCB | Port 80 registered as listening |
| 16:03 | DMZ-SRV1 | `telnet 172.16.50.10 80` (local) | Connection refused | Fails even locally (some IOS builds refuse self-connections, so not conclusive alone) |
| 16:07 | SCADA-PC1, CORE-R2 | `telnet 172.16.50.10 80` | Connection refused | Rejected by the server from remote hosts |
| 16:10 | SCADA-PC1 | `telnet 172.16.50.10 22` | Connection refused | Expected; see next line |
| 16:11 | DMZ-SRV1 | `show ip ssh` | `SSH Disabled - version 2.0`, no RSA/EC keys | **Confirmed:** port 22 refused because SSH isn't running (no keys). Unrelated to HTTP |

Note: VTY 0 4 is `transport input ssh`, so port 23 is refused by design. Unrelated to this ticket; no change requested.

## Escalation note (16:07)

> **To:** Server Team · **Host:** DMZ-SRV1 172.16.50.10
>
> **Issue:** HTTP (TCP/80) on DMZ-SRV1 refuses connections from SCADA-PC1, CORE-R2 and the server itself (`% Connection refused by remote host`).
>
> **Network ruled out:** ICMP to 172.16.50.10 succeeds from SCADA-PC1 and HQ-PC1. "Refused" confirms the connection reaches the server.
>
> **Server state:** `ip http server` enabled; `show tcp brief all` shows `0.0.0.0.80 LISTEN`.
>
> **Request:** Investigate why the HTTP service rejects connections.

## Working hypothesis (engineer)

DMZ-SRV1 is an IOL router standing in for a web server. Its built-in HTTP service may not accept connections the way a real web server does in this image. **Unconfirmed.** Options:
1. Investigate the IOL HTTP service further (`show ip http server all`, `debug ip http all` while connecting).
2. Replace the stand-in with a real Linux web server node (Ubuntu/Alpine with a web server) so the DMZ behaves like production.

## Root cause

_TBD_

## Resolution

_TBD_ · Verification: SCADA-PC1 `telnet 172.16.50.10 80` opens; INET `telnet 198.51.100.10 80 /source-interface Loopback0` opens.
