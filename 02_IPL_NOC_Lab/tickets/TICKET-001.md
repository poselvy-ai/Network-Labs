# TICKET-001 — DMZ-SRV1 unreachable from other subnets (reported as: SCADA-PC1 cannot reach DMZ web server)

| Field | Value |
|-------|-------|
| Priority | P3: one host, one service |
| Opened | 2026-11-16 14:38 |
| Reported by | Baseline verification, check #8 |
| Affected | SCADA-PC1 (10.2.10.10) → DMZ-SRV1 (172.16.50.10), TCP/80 |
| Status | **Resolved / Closed 2026-11-16 16:10.** Network fault fixed; HTTP service fault split to [TICKET-002](./TICKET-002.md) |

This ticket is written in two parts:
- **Part 1, Tier 1 NOC:** what a NOC Analyst 1 does: confirm, scope, check reachability and routing with read-only commands, then escalate with evidence.
- **Part 2, Engineer investigation (practice):** the deeper isolation done after the hand-off (packet-tracer, captures, debug, fix). In production this is Tier 2 / engineering work and needs the right access and change control.

---

# Part 1: Tier 1 NOC

## Symptom

```
SCADA-PC1>telnet 172.16.50.10 80
Trying 172.16.50.10, 80 ...
% Connection timed out; remote host not responding
```

"Timed out" (not "refused") means nothing answered at all. Either the packets never arrive, the replies never come back, or something in the path drops them silently.

## Expected path

| Hop | Device | In | Out | Expected decision |
|----:|--------|----|-----|-------------------|
| 1 | SCADA-PC1 | — | E0/0 | Default gateway 10.2.10.1 |
| 2 | SUB-R1 | E0/1 | E0/0 | O IA 172.16.50.0/24 via 10.2.0.1 |
| 3 | CORE-R2 | E0/3 | E0/0 | O 172.16.50.0/24 via 10.0.0.5 (FW1) |
| 4 | FW1 | Gi0/2 (inside2) | Gi0/3 (dmz) | Security 100 → 50 permitted, no NAT |
| 5 | DMZ-SRV1 | E0/0 | — | HTTP server listening on 80 |
| Return | DMZ-SRV1 → FW1 → CORE-R2 → SUB-R1 | | | Gateway 172.16.50.1; FW1 route 10.2.10.0/24 via 10.0.0.6 |

## Tier 1 troubleshooting log

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

## Escalation note (15:24)

**To:** Network Engineering / Firewall team
**Ticket:** TICKET-001, P3
**Summary:** Internal hosts cannot reach DMZ-SRV1 (172.16.50.10) through FW1. Confirmed from SCADA-PC1 (substation, via inside2), CORE-R2 (directly attached to inside2) and HQ-PC1 (HQ, via inside1).

**Ruled out:**
- Host and gateway: SCADA-PC1 → 10.2.10.1 OK.
- Internal routing: CORE-R2 has FULL OSPF adjacencies with CORE-R1, FW1, RENO-R1 and SUB-R1, and an OSPF route to 172.16.50.0/24. Traceroute reaches CORE-R2.
- FW1 reachability: FW1 itself pings DMZ-SRV1, SCADA-PC1 and the router loopbacks, so the DMZ link, the inside links and DMZ-SRV1 are up.

**Isolated to:** traffic *through* FW1, inside1/inside2 (security 100) → dmz (security 50). Both inside interfaces affected, so not a single link or core router.

**Suspected area:** firewall policy, inspection or NAT for inside → dmz.

**Requested:** engineering review of inside → dmz flows.

> **Tier 1 hand-off ends here.**

---

# Part 2: Engineer investigation (practice)

Work done after the escalation, as the engineer would. Captures and debugs on production devices need the right access and are usually run under a change or with care for device load.

## Engineer log

| Time | Device | Command | Result | Conclusion |
|------|--------|---------|--------|------------|
| 15:27 | FW1 | `packet-tracer input inside2 icmp 10.2.10.10 8 0 172.16.50.10 detail` | Action: allow. Route via dmz, no NAT, ICMP inspected, adjacency to DMZ-SRV1 MAC aabb.cc00.0700 | FW1 policy permits and forwards the request. Escalation's suspect (firewall policy) not supported |
| 15:27 | FW1 | `show asp drop` | acl-drop 25/10, l2_acl 205, interface-down 4 (never cleared) | Nothing tied to this flow; counters need clearing before a test |
| 15:30 | SCADA-PC1 | `ping 8.8.8.8` | 5/5 | Inside → outside through FW1 works; return traffic and ICMP inspection OK. Outage limited to the DMZ |
| 15:32 | FW1 | `clear asp drop`; `capture CAP1 interface dmz match ip host 10.2.10.10 host 172.16.50.10`; ping from SCADA-PC1 | 5 echo **requests** out the dmz interface, **0 replies**; asp drop: only l2_acl 6 | FW1 delivers the requests and drops nothing. DMZ-SRV1's replies never reach FW1 |
| 15:39 | FW1 | `show route` | 10.2.10.0/24 via 10.0.0.6 (inside2); 172.16.50.0/24 connected dmz | FW1's routing for both directions is correct |
| 15:39 | DMZ-SRV1 | `show ip route` / `show ip int brief` | Default gateway 172.16.50.1; E0/0 172.16.50.10 up/up | Gateway configured, link up |
| 15:39 | DMZ-SRV1 | `ping 172.16.50.1` | 5/5 | Server ↔ gateway OK |
| 15:39 | DMZ-SRV1 | `ping 10.2.10.10` | 0/5 | Expected by design: dmz (50) → inside (100) denied without an ACL. Not evidence by itself |
| 15:43 | DMZ-SRV1 | `show ip traffic \| include echo` | Rcvd 40 echo; Sent 40 echo reply, 168 unreachable | Server receives pings and generates replies (unreachables likely answers to traceroute probes), yet none arrive at FW1 |
| 15:46 | DMZ-SRV1 | `clear arp`; `debug ip icmp`; ping from SCADA-PC1 | Echo replies "sent" to 10.2.10.10 (counter 45 → 50) | Server builds the replies |
| 15:46 | DMZ-SRV1 | `show ip arp` | **10.2.10.10 Incomplete**; 172.16.50.1 resolved (5254.004f.2b6b) | Server ARPs for the remote host instead of using its gateway. Replies die on the server. **Root cause confirmed** |

## Hypothesis (confirmed 15:46)

DMZ-SRV1 runs as a host (`no ip routing`). For off-subnet destinations it appears to ARP for the remote host directly instead of using its default gateway. On the other host subnets, the IOS router answers that ARP (proxy ARP is on by default), which hides the problem. The ASA does not proxy-ARP for arbitrary addresses, so DMZ-SRV1's replies have no next hop.

**Confirming test:**
- [x] DMZ-SRV1: `clear ip arp` → ping from SCADA-PC1 → echo reply counter +5 and `show ip arp` shows 10.2.10.10 *Incomplete*
- [ ] SCADA-PC1: `show ip arp` (8.8.8.8 resolved to SUB-R1's MAC = proxy ARP)

## Root cause

DMZ-SRV1 was built as a host with `no ip routing` + `ip default-gateway 172.16.50.1`. In that mode it ARPed for off-subnet destinations (10.2.10.10) instead of forwarding to its gateway. FW1 (ASA) does not proxy-ARP for arbitrary addresses, so the ARP never resolved and every reply to another subnet was dropped on the server. Inside → DMZ and internet → DMZ were both affected.

Contributing factor: the other lab hosts use the same host-mode config but appeared healthy because their IOS gateways answer proxy ARP by default, which masks the problem.

## Resolution

Configured DMZ-SRV1 with a real default route instead of host mode:

```
conf t
 ip routing
 ip route 0.0.0.0 0.0.0.0 172.16.50.1
 no ip default-gateway
end
write memory
```

Baseline config in [`../configs/DMZ-SRV1.txt`](../configs/DMZ-SRV1.txt) and the CML import file updated so a rebuild doesn't reintroduce the fault.

**Verification:**
- [x] DMZ-SRV1 default route applied (15:51)
- [x] SCADA-PC1 `ping 172.16.50.10`: success (15:51)
- [x] SCADA-PC1 `telnet 172.16.50.10 80`: now **refused** instead of timed out. The connection reaches the server, so the network fault is fixed; the server's HTTP service rejects it → split to [TICKET-002](./TICKET-002.md)
- [x] HQ-PC1 `ping 172.16.50.10`: success (15:51)
- [ ] INET `telnet 198.51.100.10 80 /source-interface Loopback0`: moved to TICKET-002 (depends on the HTTP service)

**Follow-up (problem record):** HQ-PC1, RENO-PC1 and SCADA-PC1 depend on proxy ARP for the same reason. If proxy ARP is ever disabled on their gateways (a common hardening step), they will fail the same way. Recommend the same default-route fix.

## Lessons learned

1. **A capture beats a theory.** Requests out, no replies back moved the investigation off the firewall in one test.
2. **"The device can ping its gateway" doesn't prove it uses its gateway.** Check `show ip arp` for *Incomplete* entries to off-subnet addresses.
3. **Proxy ARP can hide misconfigurations.** Things that "work" only because a router answers ARP for other hosts break as soon as the gateway changes (here, an ASA).
4. **Tier 1 escalation was correct even though its suspect was wrong.** The evidence narrowed the problem to the firewall boundary; engineering found the exact cause on the far side of it.
5. **Check both ends of the path.** Tier 1 worked from the source toward the firewall; the fault was on the destination. Checking the destination's gateway *and* its ARP/neighbor table early (`show ip route` + `show ip arp` on Cisco; `ipconfig /all` + `arp -a` on Windows; `ip route` + `ip neigh` on Linux) would have found it sooner.
6. **A router-as-host needs a gateway of last resort.** With `ip routing` on, a static default route (`S* 0.0.0.0/0`) is what sends return traffic to the gateway; host-mode `ip default-gateway` looked correct but wasn't used.

## Closure

Closed 2026-11-16 16:10. The network fault (DMZ-SRV1 could not return traffic to other subnets) is resolved: ICMP from SCADA-PC1 and HQ-PC1 succeeds, and TCP/80 now gets a **refusal** instead of a timeout, which proves the connection reaches the server. The remaining HTTP service failure is a separate fault with a different owner (server team) and is tracked in [TICKET-002](./TICKET-002.md).
