
# Design Decisions

Each decision records what was chosen, what else was considered, and why. This is the page an interviewer reads to see *how* you think, not just what you typed.

---

## DD-01 — Addressing: one /16 per site, VLAN ID = third octet

**Decision:** HQ `10.10.0.0/16`, Branch `10.20.0.0/16`, VLAN *N* uses `10.site.N.0/24`.

**Alternatives:** The original plan mixed `10.10.x.0` user subnets with `172.16.10.0/24` for IT, and VLAN 10 mapped to `10.10.20.0`.

**Why:** Mismatched VLAN IDs and subnets slow down troubleshooting ("VLAN 10 is .20?"). A /16 per site means each site is one summary route across the VPN, and VLAN IDs can be reused at every site with the same meaning.

---

## DD-02 — Separate Management (99) and Servers (50) VLANs

**Decision:** Switch management SVIs live in VLAN 99; servers live in VLAN 50. IT staff laptops stay in VLAN 40.

**Alternatives:** Original plan put IT users, servers and switch management all in VLAN 222.

**Why:** Management planes and servers should not share a broadcast domain with user laptops. Separate VLANs let an ACL (Phase 4) say "only VLAN 40 can SSH to VLAN 99" and "all users can reach DNS/web on VLAN 50, nothing else."

---

## DD-03 — DHCP on the site routers

**Decision:** HQRT1 and BRRT1 run the IOS DHCP server for their own sites.

**Alternatives considered:**
1. Dedicated DHCP server at HQ with `ip helper-address` relays (original design).
2. Central HQ DHCP server serving the branch across the VPN.

**Why:** At ~105 users and two sites, router/firewall DHCP is what small and mid-size businesses commonly run, and it saves a CML node. Branch DHCP *must* be local: if the VPN drops, branch users would otherwise lose the ability to get or renew an address. (DHCP relay works fine across routed links; the reason to keep it local is WAN survivability, not DHCP's broadcast nature.)

**Trade-off / production note:** Larger enterprises, especially Active Directory shops, usually run DHCP on Windows or IPAM servers (Infoblox, etc.) and relay to them with `ip helper-address`. This lab can demonstrate that later by moving HQ DHCP to SRV1 (see Phase 4 ideas).

---

## DD-04 — Consolidate three servers into SRV1

**Decision:** One Ubuntu server (SRV1) provides DNS (dnsmasq) and the internal web app (nginx). DHCP moved to routers (DD-03).

**Alternatives:** Separate DHCP, DNS and App servers.

**Why:** CML is limited to 20 nodes; three servers would leave little room for the branch, test hosts and later phases. Role separation is still shown by VLAN placement, and a second server can be added in the headroom if needed.

---

## DD-05 — Rapid PVST+ with explicit priorities

**Decision:** Rapid PVST+ on all switches. HQSW1 priority 4096 (root), HQSW2 8192 (secondary), HQSW3 default. VTP transparent.

**Why:** HQSW1 holds the router uplink, so it should be root for every VLAN; HQSW2 takes over if HQSW1 fails. Explicit priorities are deterministic (the `root primary` macro calculates once and never re-adjusts). The expected blocked port is HQSW3 E0/1 toward HQSW2. VTP transparent keeps VLANs in each switch's running-config (so they appear in the saved configs in this repo) and avoids VTP revision-number accidents.

---

## DD-06 — Route-based VPN (IKEv2 + IPsec VTI) with OSPF over the tunnel

**Decision:** `Tunnel0` on each router in `tunnel mode ipsec ipv4`, OSPF area 0 across the tunnel, static default route to the ISP.

**Alternatives:** Policy-based crypto map with a matching crypto ACL; GRE over IPsec.

**Why:** With a VTI the tunnel is a real interface: routing decides what is encrypted, OSPF learns the remote site automatically, and traffic to the remote site leaves through Tunnel0 rather than the NAT outside interface, so no NAT-exemption ACL is needed. Only the LAN and tunnel networks go into OSPF; the WAN /30s stay out to avoid recursive routing.

---

## DD-07 — Hardening baseline

Every device: SSH v2 only with local users, `enable secret`, `no ip http server`, `no ip domain lookup`, unused native VLAN 999, `switchport nonegotiate` on trunks, PortFast + BPDU Guard on access ports.

## DD-07 - Single-Node for internet

The internet in simulation with connect to one single node (ISP1). In production each site will connect to it own provider.

---

## Accepted risks

The customer accepts the following single points of failure:

- One edge router per site and one ISP circuit per site.
- One link between HQRT1 and HQSW1; losing it or HQSW1 isolates HQ from routing.

Note: the **switch layer at HQ is redundant**. The HQSW1–HQSW2–HQSW3 triangle gives every switch two paths, with STP blocking one link until it is needed.

---

## Phase 4 ideas (stretch)

- Inter-VLAN policy with ACLs or IOS Zone-Based Firewall (e.g., Sales cannot reach Engineering; only IT reaches MGMT).
- Move HQ DHCP to SRV1 and use `ip helper-address` relays.
- Second ISP link with IP SLA tracking for failover.
- Syslog + NTP on SRV1.
