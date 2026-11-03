# Network-Labs

Hands-on network engineering labs built in **Cisco Modeling Labs (CML)**. Each lab starts from a business scenario, goes through design (addressing, diagrams, design decisions), then build and verification, with clean device configs and CLI evidence for every phase.

**Certifications:** CCNA

## Labs

| # | Lab | Skills demonstrated | Status |
|---|-----|---------------------|--------|
| 1 | [Silver State Business Solutions](./01_Silver_State_Business/README.md) | VLANs, 802.1Q trunking, Rapid PVST+, router-on-a-stick, DHCP, DNS, PAT, IKEv2 route-based VPN, OSPF | Complete |

## How each lab is organized

```
NN-lab-name/
├── README.md          Scenario, requirements, topology, status (start here)
├── design/            Addressing plan, design decisions, diagrams (.drawio + .png)
├── configs/           Final running-config per device, plus CML topology exports (.yaml) to rebuild the lab
├── phase/             Build steps and status for each phase
├── verification/      Screenshots / show-command output used as evidence
└── lessons-learned.md What broke, why, and how it was fixed
```
