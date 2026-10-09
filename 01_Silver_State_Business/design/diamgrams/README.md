# Diagrams

Keep the editable source next to the exported image: `name.drawio` + `name.png`. No spaces or parentheses in filenames.

| File | Shows |
|------|-------|
| `ssb-physical.drawio/.png` | Every node and cable with **CML interface names** (E0/0...), trunk vs access, access VLAN per port, STP root/secondary and the blocked port |
| `ssb-logical.drawio/.png` | L3 view: each subnet as a cloud/segment with its gateway, the two /30 WAN links, Tunnel0 172.31.0.0/30, NAT boundaries (inside/outside), OSPF area 0, SRV1 services |

Tips that make diagrams look professional:
- One diagram, one purpose. Don't mix the L2 and L3 views.
- Put a small legend (line styles: trunk, access, VPN) and a title block (lab, version, date).
- Label the device's lab role, not a fictional model number, e.g. "HQRT1 — edge router (CML IOL)".
- Export at 2x scale so text stays sharp on GitHub.
