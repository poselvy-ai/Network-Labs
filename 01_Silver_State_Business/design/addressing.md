
# Addressing Plan (IPAM)

## Site summaries

| Site | Supernet | Notes |
|------|----------|-------|
| HQ | 10.10.0.0/16 | One summary route describes all of HQ |
| Branch | 10.20.0.0/16 | One summary route describes all of the branch |
| VPN transit | 172.31.0.0/30 | Tunnel0 between HQRT1 and BRRT1 |
| HQ WAN | 203.0.113.8/30 | ISP1 .9 — HQRT1 .10 |
| Branch WAN | 203.0.113.12/30 | ISP1 .13 — BRRT1 .14 |

Convention: **VLAN ID = third octet.** VLAN 30 is always `10.x.30.0/24`, at either site.

## VLANs

| VLAN | Name | HQ subnet | Branch subnet | Gateway | Addressing |
|-----:|------|-----------|---------------|---------|------------|
| 10 | CORP | 10.10.10.0/24 | 10.20.10.0/24 | .1 | DHCP .21–.254 |
| 20 | SALES | 10.10.20.0/24 | 10.20.20.0/24 | .1 | DHCP .21–.254 |
| 30 | ENG | 10.10.30.0/24 | 10.20.30.0/24 | .1 | DHCP .21–.254 |
| 40 | IT | 10.10.40.0/24 | — | .1 | DHCP .21–.254 |
| 50 | SERVERS | 10.10.50.0/24 | — | .1 | Static only |
| 99 | MGMT | 10.10.99.0/24 | 10.20.99.0/24 | .1 | Static only |
| 999 | NATIVE-UNUSED | — | — | — | Trunk native VLAN, carries no traffic |

`.2–.20` in every DHCP scope is excluded and reserved for static devices (printers, etc.).

A /24 per department gives ~230 DHCP addresses each, several times the current headcount, so the 3-year growth requirement is met without re-addressing.

## Static assignments

| Device | Interface | Address | VLAN |
|--------|-----------|---------|------|
| HQRT1 | E0/0 | 203.0.113.10/30 | WAN |
| HQRT1 | E0/1.10 / .20 / .30 / .40 / .50 / .99 | 10.10.x.1/24 | gateways |
| HQRT1 | Tunnel0 | 172.31.0.1/30 | VPN |
| HQSW1 | Vlan99 | 10.10.99.11/24 | 99 |
| HQSW2 | Vlan99 | 10.10.99.12/24 | 99 |
| HQSW3 | Vlan99 | 10.10.99.13/24 | 99 |
| SRV1 | ens2 | 10.10.50.10/24 | 50 |
| BRRT1 | E0/0 | 203.0.113.14/30 | WAN |
| BRRT1 | E0/1.10 / .20 / .30 / .99 | 10.20.x.1/24 | gateways |
| BRRT1 | Tunnel0 | 172.31.0.2/30 | VPN |
| BRSW1 | Vlan99 | 10.20.99.11/24 | 99 |
| ISP1 | Lo0 | 8.8.8.8/32 | simulated internet |

## DNS names (zone `ssbs.internal`)

| Name | Address |
|------|---------|
| srv1.ssbs.internal | 10.10.50.10 |
| app.ssbs.internal | 10.10.50.10 |
| hqrt1.ssbs.internal | 10.10.99.1 |
| hqsw1 / hqsw2 / hqsw3.ssbs.internal | 10.10.99.11 / .12 / .13 |
| brrt1.ssbs.internal | 10.20.99.1 |
| brsw1.ssbs.internal | 10.20.99.11 |

`.internal` is the TLD ICANN reserved (2024) for private networks. Avoid `.local`, which collides with mDNS.

## Physical connections (CML)

| A side | Port | B side | Port | Type |
|--------|------|--------|------|------|
| ISP1 | E0/0 | HQRT1 | E0/0 | Routed /30 |
| ISP1 | E0/1 | BRRT1 | E0/0 | Routed /30 |
| HQRT1 | E0/1 | HQSW1 | E0/0 | Trunk |
| HQSW1 | E0/1 | HQSW2 | E0/0 | Trunk |
| HQSW1 | E0/2 | HQSW3 | E0/0 | Trunk |
| HQSW2 | E0/1 | HQSW3 | E0/1 | Trunk (expected STP alternate/blocking on HQSW3) |
| HQSW1 | E0/3 | PC-IT | eth0 | Access 40 |
| HQSW2 | E0/2 | SRV1 | ens2 | Access 50 |
| HQSW2 | E0/3 | PC-ENG | eth0 | Access 30 |
| HQSW3 | E0/2 | PC-CORP | eth0 | Access 10 |
| HQSW3 | E0/3 | PC-SALES | eth0 | Access 20 |
| BRRT1 | E0/1 | BRSW1 | E0/0 | Trunk |
| BRSW1 | E0/1 | PC-BR-CORP | eth0 | Access 10 |
| BRSW1 | E0/2 | PC-BR-SALES | eth0 | Access 20 |
| BRSW1 | E0/3 | PC-BR-ENG | eth0 | Access 30 |
