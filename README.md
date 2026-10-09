# Network-Labs

Hands-on network engineering labs built in **Cisco Modeling Labs (CML)**. Each lab starts from a business scenario, goes through design (addressing, diagrams, design decisions), then build and verification, with clean device configs and CLI evidence for every phase.

**Certifications:** CCNA

## Labs

| # | Lab | Skills demonstrated | Status |
|---|-----|---------------------|--------|
| 1 | [Silver State Business Solutions](./01-silver-state-business/README.md) | VLANs, 802.1Q trunking, Rapid PVST+, router-on-a-stick, DHCP, DNS, PAT, IKEv2 route-based VPN, OSPF | In progress |

## How each lab is organized

```
NN-lab-name/
├── README.md          Scenario, requirements, topology, status (start here)
├── design/            Addressing plan, design decisions, diagrams (.drawio + .png)
├── configs/           Final running-config per device (one clean file each)
├── phases/            Build steps and verification for each phase
├── verification/      Screenshots / show-command output used as evidence
├── cml/               CML topology export (.yaml) — import it to rebuild the lab
└── lessons-learned.md What broke, why, and how it was fixed
```
