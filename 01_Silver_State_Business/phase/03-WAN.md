WAN Build Phase

Phases are broken down into logical phases of the build for Silver State. This phase builds the WAN architecture that joins HQ and Branch into one routed network: an encrypted site-to-site tunnel over the ISP, and OSPF area 0 to exchange routes across it.

| # | Step| Status |
|---|------|--------|
| 1	| Verify the WAN underlay: HQRT1 ↔ BRRT1 public addresses reachable through ISP1	| Complete |
| 2	| Build Tunnel0 as plain GRE (no encryption) and prove the tunnel path	| Complete |
| 3	| intAdd IKEv2/IPsec protection to Tunnel0 (route-based VPN, VTI) | Complet |
| 4	| Build OSPF area 0 over Tunnel0; advertise LAN subnets, not the WAN /30s	| Complete |
| 5	| Re-test the Phase 2 "expected failures": cross-site services and management	| Complete |
| 6 |	WAN failure test: drop the tunnel, prove branch survives locally, prove recovery | Complete |
| 7	| Save configs, extract configs and export lab YAML, tag ssb-phase3	| Complete |

[BACK](../README.md)
