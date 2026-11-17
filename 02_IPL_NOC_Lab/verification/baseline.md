# Baseline Verification — IPL NOC Lab

Run these after every fresh start. Paste the output under each check as evidence. A ticket is only meaningful against a known-good baseline.

| # | Device | Command | Expected | Result |
|---|--------|---------|----------|--------|
| 1 | CORE-R1, CORE-R2 | `show ip ospf neighbor` | All neighbors FULL | |
| 2 | CORE-R1 | `show ip route ospf` | `O*E2 0.0.0.0/0` via 10.0.0.1; O IA routes to 10.1.x and 10.2.x | |
| 3 | FW1 | `show ospf neighbor` | 10.255.255.1 and 10.255.255.2 FULL | |
| 4 | FW1 | `show route` | `S* 0.0.0.0 0.0.0.0 via 198.51.100.1` | |
| 5 | EDGE-R1 | `show ip bgp summary` | ISP-A and ISP-B up with prefixes received | |
| 6 | EDGE-R1 | `show ip bgp 8.8.8.8` | Best path via 203.0.113.1 (local-pref 200) | |
| 7 | HQ-PC1 | `ping 8.8.8.8` | Success (PAT on FW1) | |
| 8 | SCADA-PC1 | `telnet 172.16.50.10 80`, then `GET / HTTP/1.0` + Enter twice | `HTTP/1.0 200 OK` | |
| 9 | INET | `telnet 198.51.100.10 80 /source-interface Loopback0` | Connection opens | |
| 10 | DMZ-SRV1 | `systemctl status ipl-web` / `sudo ufw status verbose` | active (running); 80 allowed, 22 from 10.0.0.0/16 | |
| 11 | HQ-PC1 / SCADA-PC1 | `telnet 172.16.50.10 22` | HQ: SSH banner. SCADA: times out (ufw, by design) | |

Tip: `terminal length 0` before long `show` commands so the output copies cleanly.
