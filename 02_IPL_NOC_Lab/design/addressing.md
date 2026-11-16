# Addressing Plan (IPAM) — IPL NOC Lab

## Ranges

| Range | Use |
|-------|-----|
| 10.0.0.0/16 | Core and HQ (OSPF area 0) |
| 10.1.0.0/16 | Reno (OSPF area 1) |
| 10.2.0.0/16 | Substation (OSPF area 2) |
| 10.255.255.0/24 | Router loopbacks / router IDs |
| 172.16.50.0/24 | DMZ |
| 198.51.100.0/24 | IPL public block (advertised by EDGE-R1) |
| 203.0.113.0/24 | ISP peering links |

Convention: the second octet is the site (0 = core/HQ, 1 = Reno, 2 = substation). WAN links are /30s in `10.<site>.0.x`; user LANs are `10.<site>.10.0/24` with the gateway on `.1` and the test host on `.10`.

## Subnets

| Subnet | Purpose | Routing |
|--------|---------|---------|
| 10.0.0.0/30 | FW1 inside1 ↔ CORE-R1 | OSPF area 0 |
| 10.0.0.4/30 | FW1 inside2 ↔ CORE-R2 | OSPF area 0 |
| 10.0.0.8/30 | CORE-R1 ↔ CORE-R2 | OSPF area 0 |
| 10.0.10.0/24 | HQ users, VLAN 10 | OSPF area 0 (passive) |
| 10.1.0.0/30 | CORE-R1 ↔ RENO-R1 | OSPF area 1 |
| 10.1.0.4/30 | CORE-R2 ↔ RENO-R1 | OSPF area 1 |
| 10.1.10.0/24 | Reno users | OSPF area 1 (passive) |
| 10.2.0.0/30 | CORE-R2 ↔ SUB-R1 | OSPF area 2 |
| 10.2.10.0/24 | SCADA hosts | OSPF area 2 (passive) |
| 172.16.50.0/24 | DMZ servers | OSPF area 0 (from FW1) |
| 198.51.100.0/29 | EDGE-R1 ↔ FW1 outside | Connected |
| 198.51.100.8/29 | Public NAT range (DMZ-SRV1 = .10) | Static on EDGE-R1 → FW1 |
| 203.0.113.0/30 | EDGE-R1 ↔ ISP-A | eBGP |
| 203.0.113.4/30 | EDGE-R1 ↔ ISP-B | eBGP |
| 203.0.113.8/30 | ISP-A ↔ INET | eBGP |
| 203.0.113.12/30 | ISP-B ↔ INET | eBGP |

## Interfaces

| Device | Interface | IP / CIDR | Connects to | Remote interface | Routing |
|--------|-----------|-----------|-------------|------------------|---------|
| CORE-R1 | Lo0 | 10.255.255.1/32 | — | — | OSPF area 0 |
| CORE-R1 | E0/0 | 10.0.0.2/30 | FW1 | Gi0/1 (inside1) | OSPF area 0 |
| CORE-R1 | E0/1 | 10.0.0.9/30 | CORE-R2 | E0/1 | OSPF area 0 |
| CORE-R1 | E0/2 | 10.1.0.1/30 | RENO-R1 | E0/0 | OSPF area 1 |
| CORE-R1 | E0/3 | 10.0.10.1/24 | HQ-SW1 | E0/0 | OSPF area 0 (passive) |
| CORE-R2 | Lo0 | 10.255.255.2/32 | — | — | OSPF area 0 |
| CORE-R2 | E0/0 | 10.0.0.6/30 | FW1 | Gi0/2 (inside2) | OSPF area 0 |
| CORE-R2 | E0/1 | 10.0.0.10/30 | CORE-R1 | E0/1 | OSPF area 0 |
| CORE-R2 | E0/2 | 10.1.0.5/30 | RENO-R1 | E0/1 | OSPF area 1 |
| CORE-R2 | E0/3 | 10.2.0.1/30 | SUB-R1 | E0/0 | OSPF area 2 |
| RENO-R1 | Lo0 | 10.255.255.3/32 | — | — | OSPF area 1 |
| RENO-R1 | E0/0 | 10.1.0.2/30 | CORE-R1 | E0/2 | OSPF area 1 |
| RENO-R1 | E0/1 | 10.1.0.6/30 | CORE-R2 | E0/2 | OSPF area 1 |
| RENO-R1 | E0/2 | 10.1.10.1/24 | RENO-PC1 | E0/0 | OSPF area 1 (passive) |
| SUB-R1 | Lo0 | 10.255.255.4/32 | — | — | OSPF area 2 |
| SUB-R1 | E0/0 | 10.2.0.2/30 | CORE-R2 | E0/3 | OSPF area 2 |
| SUB-R1 | E0/1 | 10.2.10.1/24 | SCADA-PC1 | E0/0 | OSPF area 2 (passive) |
| FW1 | Gi0/0 (outside, sec 0) | 198.51.100.2/29 | EDGE-R1 | E0/2 | Static default → 198.51.100.1 |
| FW1 | Gi0/1 (inside1, sec 100) | 10.0.0.1/30 | CORE-R1 | E0/0 | OSPF area 0 |
| FW1 | Gi0/2 (inside2, sec 100) | 10.0.0.5/30 | CORE-R2 | E0/0 | OSPF area 0 |
| FW1 | Gi0/3 (dmz, sec 50) | 172.16.50.1/24 | DMZ-SRV1 | E0/0 | OSPF area 0 |
| EDGE-R1 | E0/0 | 203.0.113.2/30 | ISP-A | E0/0 | eBGP AS 65100 |
| EDGE-R1 | E0/1 | 203.0.113.6/30 | ISP-B | E0/0 | eBGP AS 65200 |
| EDGE-R1 | E0/2 | 198.51.100.1/29 | FW1 | Gi0/0 (outside) | Static 198.51.100.8/29 → FW1 |
| ISP-A | Lo0 | 100.64.1.1/32 | — | — | BGP AS 65100 |
| ISP-A | E0/0 | 203.0.113.1/30 | EDGE-R1 | E0/0 | eBGP AS 65001 |
| ISP-A | E0/1 | 203.0.113.9/30 | INET | E0/0 | eBGP AS 65300 |
| ISP-B | Lo0 | 100.64.2.1/32 | — | — | BGP AS 65200 |
| ISP-B | E0/0 | 203.0.113.5/30 | EDGE-R1 | E0/1 | eBGP AS 65001 |
| ISP-B | E0/1 | 203.0.113.13/30 | INET | E0/1 | eBGP AS 65300 |
| INET | Lo0 | 8.8.8.8/32 | — | — | BGP AS 65300 |
| INET | Lo1 | 1.1.1.1/32 | — | — | BGP AS 65300 |
| INET | E0/0 | 203.0.113.10/30 | ISP-A | E0/1 | eBGP AS 65100 |
| INET | E0/1 | 203.0.113.14/30 | ISP-B | E0/1 | eBGP AS 65200 |
| HQ-SW1 | E0/0 | access VLAN 10 | CORE-R1 | E0/3 | Layer 2 |
| HQ-SW1 | E0/1 | access VLAN 10 | HQ-PC1 | E0/0 | Layer 2 |
| HQ-SW1 | Vlan10 | 10.0.10.2/24 | — | — | Default gateway 10.0.10.1 |
| HQ-PC1 | E0/0 | 10.0.10.10/24 | HQ-SW1 | E0/1 | Default gateway 10.0.10.1 |
| RENO-PC1 | E0/0 | 10.1.10.10/24 | RENO-R1 | E0/2 | Default gateway 10.1.10.1 |
| SCADA-PC1 | E0/0 | 10.2.10.10/24 | SUB-R1 | E0/1 | Default gateway 10.2.10.1 |
| DMZ-SRV1 | E0/0 | 172.16.50.10/24 | FW1 | Gi0/3 (dmz) | Default gateway 172.16.50.1 |

## Router IDs and AS numbers

| Device | Router ID | Protocol |
|--------|-----------|----------|
| CORE-R1 | 10.255.255.1 | OSPF 1 |
| CORE-R2 | 10.255.255.2 | OSPF 1 |
| RENO-R1 | 10.255.255.3 | OSPF 1 |
| SUB-R1 | 10.255.255.4 | OSPF 1 |
| FW1 | 10.255.255.254 | OSPF 1 |
| EDGE-R1 | 198.51.100.1 | BGP AS 65001 |
| ISP-A | 100.64.1.1 | BGP AS 65100 |
| ISP-B | 100.64.2.1 | BGP AS 65200 |
| INET | 8.8.8.8 | BGP AS 65300 |
