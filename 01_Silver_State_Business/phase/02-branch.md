# Branch Build Phase

Phases are broken down into logical phases of the build for Silver State's Branch site. The branch must operate independently of HQ: local DHCP and its own internet path (DD-03).

| # | Step | Status |
|---|------|--------|
| 1 | Cable infrastructure according to the Physical Diagram and the *Physical Connection* table in addressing | Completed |
| 2 | BRSW1: VLANs, trunk, STP, management SVI, hardening | Complete |
| 3 | BRRT1: subinterfaces (E0/1.10–.99) and DHCP pools | Complete |
| 4 | ISP1 E0/1, then PAT and the default route on BRRT1 | Complete |
| 5 | BRRT1: Management ACL (HQ IT only, with IOL ARP workaround) | Complete |
| 6 | Test PCs: DHCP, inter-VLAN, internet |Complete |
| 7 | Save configs, extract configs and export lab YAML, tag `ssb-phase2` | Complete |

[BACK](../README.md)
