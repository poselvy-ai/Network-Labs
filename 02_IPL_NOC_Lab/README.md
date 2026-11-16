# Ironwood Power & Light (IPL) — NOC Troubleshooting Lab

> Ironwood Power & Light is a **fictional** electric utility. All names, addresses and AS numbers are lab-only (RFC 1918, RFC 5737 documentation ranges and private ASNs).

## Scenario

IPL runs a regional network: an HQ data center, a branch office in Reno, and a substation that carries SCADA traffic. A firewall separates the internal network from a dual-homed internet edge, and a DMZ hosts a public web server.

I work this lab as a **Tier 1 NOC analyst**. The network is built in a known-good state, then faults are introduced. Each fault comes in as a ticket: a symptom with no hint about the cause. I isolate it with show commands, ping and traceroute, then either fix it (Tier 1 scope) or write an escalation note with the evidence.

## Skills demonstrated

- Layer-by-layer isolation: physical, Layer 2, IP, routing, firewall policy, application
- Multi-area OSPF: neighbor states, ABRs, inter-area (O IA) and external (O*E2) routes
- eBGP: neighbor states, best-path selection (local preference, AS-path prepend)
- Firewall triage: zones and security levels, NAT/PAT, ACLs, `packet-tracer` (ASAv as a stand-in for Palo Alto)
- Ticket documentation and escalation notes

## Topology

Editable diagram: [`design/diagrams/IPL_NOC_Topology.drawio`](./design/diagrams/IPL_NOC_Topology.drawio)

```mermaid
graph TD
  INET["INET<br/>AS 65300 · 8.8.8.8 / 1.1.1.1"]
  ISPA["ISP-A (primary)<br/>AS 65100"]
  ISPB["ISP-B (backup)<br/>AS 65200"]
  EDGE["EDGE-R1<br/>AS 65001 · eBGP"]
  FW["FW1 (ASAv)<br/>Palo Alto stand-in"]
  DMZ["DMZ-SRV1<br/>172.16.50.10 · web"]
  C1["CORE-R1<br/>OSPF area 0/1"]
  C2["CORE-R2<br/>OSPF area 0/1/2"]
  SW["HQ-SW1<br/>VLAN 10"]
  RENO["RENO-R1<br/>OSPF area 1"]
  SUB["SUB-R1<br/>OSPF area 2"]
  PC1["HQ-PC1<br/>10.0.10.10"]
  PC2["RENO-PC1<br/>10.1.10.10"]
  PC3["SCADA-PC1<br/>10.2.10.10"]
  INET --- ISPA
  INET --- ISPB
  ISPA --- EDGE
  ISPB --- EDGE
  EDGE --- FW
  FW --- DMZ
  FW --- C1
  FW --- C2
  C1 --- C2
  C1 --- SW
  C1 --- RENO
  C2 --- RENO
  C2 --- SUB
  SW --- PC1
  RENO --- PC2
  SUB --- PC3
```

## Design summary

- **Internal routing:** OSPF process 1. Area 0 = core, firewall and HQ users; area 1 = Reno (dual-homed to both cores); area 2 = substation (single-homed to CORE-R2).
- **Firewall (FW1):** inside1/inside2 (security 100) to the cores, dmz (50), outside (0). PAT for 10.0.0.0/8; static NAT for the DMZ web server to `198.51.100.10`. Injects the default route into OSPF.
- **Internet edge (EDGE-R1, AS 65001):** eBGP to two ISPs. ISP-A is preferred outbound (local-pref 200) and inbound (AS-path prepend toward ISP-B). Advertises only `198.51.100.0/24`.
- **Hosts:** the PCs and the DMZ server are IOL nodes with `no ip routing` and a default gateway; DMZ-SRV1 runs `ip http server` so TCP/80 can be tested with `telnet`.

Full addressing: [`design/addressing.md`](./design/addressing.md) · Design choices: [`design/decisions.md`](./design/decisions.md)

## CML node budget (20-node limit)

| Area | Nodes | Count |
|------|-------|------:|
| Internet | INET, ISP-A, ISP-B | 3 |
| Edge / security | EDGE-R1, FW1, DMZ-SRV1 | 3 |
| Core / sites | CORE-R1, CORE-R2, RENO-R1, SUB-R1, HQ-SW1 | 5 |
| Test hosts | HQ-PC1, RENO-PC1, SCADA-PC1 | 3 |
| **Total** | | **14** |

## Build and run

1. CML 2.9 with the main refplat ISO (IOL, IOL-L2, ASAv). Host VM: 8 vCPU, 20 GB RAM.
2. Import [`configs/IPL-NOC-Baseline.yaml`](./configs/IPL-NOC-Baseline.yaml) and start the lab. FW1 (ASAv) takes 3–5 minutes to boot.
3. FW1 asks you to set an enable password the first time you type `enable`. Then `write memory`.
4. Run the baseline checks in [`verification/baseline.md`](./verification/baseline.md) before working any ticket.

Per-device baseline configs are in [`configs/`](./configs).

## Phases

| Phase | Scope | Status |
|-------|-------|--------|
| 1 | OSPF core, firewall, BGP edge: baseline build | Built, baseline in progress |
| 2 | Fault tickets (OSPF, BGP, firewall, Layer 2) | In progress |
| 3 | AWS extension: IPsec VPN + BGP to a VPC (CloudFormation) | Planned |

## Tickets

Workflow and template: [`tickets/README.md`](./tickets/README.md)

| Ticket | Summary | Status |
|--------|---------|--------|
| [TICKET-001](./tickets/TICKET-001.md) | SCADA-PC1 cannot reach DMZ web server (TCP/80) | Escalated (Tier 1) · engineer practice in progress |

## Lessons learned

[`lessons-learned.md`](./lessons-learned.md)
