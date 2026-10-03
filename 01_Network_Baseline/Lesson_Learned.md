# Lessons Learned. 
## Diagram
![SSB Diagram](./diagrams/SSB_Network_Diagram(V1).png)

## Lessons Learned — 10/1/26

1. The router platform determines the configuration method. We initially approached HQRT1 as traditional router-on-a-stick, but the Packet Tracer C819's FastEthernet ports are integrated Layer-2 switchports. We therefore used SVIs (Vlan10, Vlan20, Vlan30, Vlan222) for inter-VLAN routing rather than routed subinterfaces on Fa0.

2. Don't trust Packet Tracer's visual indicators alone. We had a Corporate PC connection that looked green in the topology, but the switch reported the interface as down/down (disabled). Reconnecting the PC/cable to a working port restored Layer-1 connectivity. The CLI should be the authoritative verification source.

3. Troubleshoot systematically through the layers. We worked through physical connectivity, access-port/VLAN membership, trunks, STP, ARP, routing, and finally end-to-end testing instead of randomly changing configuration.

4. ARP and the MAC/CAM table answer different questions. We corrected the terminology during troubleshooting: ARP maps IP → MAC, while the switch MAC/CAM table maps MAC → switchport/VLAN. Seeing the PCs' IP addresses associated with MAC addresses on HQRT1 was ARP evidence.

5. End-to-end testing proved inter-VLAN routing even though direct SVI pings behaved strangely. Hosts in different /24 networks successfully communicated through HQRT1. We deliberately did not document “SVIs cannot be pinged,” because that would be technically incorrect; the direct-SVI ICMP behavior remained an unresolved Packet Tracer/C819 peculiarity.

6. STP blocking a redundant link doesn't mean the physical link is down. The redundant switch path was operational, but RSTP placed one path in an alternate/discarding state to prevent a Layer-2 loop. HQSW1 was confirmed as the intended STP root.

7. The design has an accepted single point of failure. The switch triangle provides Layer-2 path redundancy, but the single HQSW1 → HQRT1 connection means there is no gateway/WAN redundancy. We explicitly accepted that risk for this lab.

## Lessons Learned — 10/2/26

1. Successful local communication does not validate the default gateway
HQDHCP (172.16.10.200) could communicate with devices inside VLAN 222, but initially could not communicate with hosts in VLANs 10, 20, or 30.
The cause was a typo in its configured gateway:
Incorrect: 172.162.10.1
Correct:   172.16.10.1

Because devices on 172.16.10.0/24 communicate directly at Layer 2, the incorrect gateway did not prevent HQDHCP from reaching other VLAN 222 devices. The problem appeared only when traffic needed to leave the local subnet.
Lesson: When troubleshooting cross-subnet connectivity, verify the endpoint's actual IP address, mask, and default gateway. Successful same-subnet communication does not prove the default gateway is correct.

2. Verify that the service is running before troubleshooting the network
The VLAN 10 DHCP Discover successfully traveled:
Client → HQSW1 → HQRT1 → HQDHCP

We spent time investigating the return path before discovering that the DHCP service on HQDHCP was OFF.
Once the service was enabled, the client successfully obtained a lease.
Lesson: A correctly configured network cannot compensate for an application/service that isn't running. Troubleshooting should include checking both network reachability and service state.
A good future checklist is:
Is the device reachable?
        ↓
Is the service enabled/running?
        ↓
Is the service configured correctly?
        ↓
Then investigate deeper protocol behavior

3. Don't change the network to compensate for a bad endpoint
The original IT workstation on VLAN 222 behaved incorrectly and appeared to have an addressing problem. Rather than changing VLAN 222, HQRT1, the DHCP server, or adding a helper address, we substituted a known-good PC.
The replacement PC successfully used DHCP on VLAN 222.
Lesson: When one endpoint behaves differently from other devices, substitute a known-good endpoint before modifying working network infrastructure. This helps determine whether the problem follows the host or stays with the network.

4. ip helper-address is only required when DHCP must cross a routed boundary
VLANs 10, 20, and 30 require:
ip helper-address 172.16.10.200

because their DHCP Discover messages are broadcasts and HQDHCP resides in VLAN 222.
VLAN 222 does not require a helper because its clients and HQDHCP share the same broadcast domain.
Lesson: DHCP relay solves the problem of getting a client's broadcast to a DHCP server on another subnet. It is not required when the client and server reside in the same VLAN.

5. Test services in layers
When validating HQ_APP_01, we tested it both ways:
172.16.10.202

and:
app1.ssb.local

Both succeeded.
That distinction matters. If access by IP succeeds but access by hostname fails, the underlying network path is probably operational and DNS becomes the primary troubleshooting target.
Lesson: Test IP connectivity before name resolution. This isolates Layer 3/application reachability from DNS problems.

6. DNS names and device hostnames serve different purposes
We established:
Device hostname: HQ_APP_01
IP:              172.16.10.202
FQDN:            app1.ssb.local
DNS server:      172.16.10.201

HQ_APP_01 identifies the infrastructure device, while app1.ssb.local provides users/applications with a DNS name for reaching the service.

7. Follow evidence instead of immediately changing configurations
Probably the biggest lesson from today's troubleshooting was methodological.

Several possible causes came up: ACLs, VLAN configuration, STP, routing, DHCP relay, switch forwarding, Packet Tracer behavior, and the server itself. We kept narrowing the problem with routing-table checks, ARP, source-specific pings, ipconfig, Simulation Mode, and known-good endpoint substitution.

Some of our hypotheses turned out to be wrong—and that's normal troubleshooting.
The important process was:
**Observe → form hypothesis → test → accept/reject hypothesis → narrow scope → change configuration only after identifying the cause.**

That is the lesson I'd emphasize most in the GitHub write-up because it demonstrates troubleshooting methodology, not merely Cisco command knowledge.
We also have enough material now that I would make today's DHCP/default-gateway incident its own subsection in Lesson_Learned.md, rather than burying it in a generic DHCP paragraph.
Observe → form hypothesis → test → accept/reject hypothesis → narrow scope → change configuration only after identifying the cause.
That is the lesson I'd emphasize most in the GitHub write-up because it demonstrates troubleshooting methodology, not merely Cisco command knowledge.
We also have enough material now that I would make today's DHCP/default-gateway incident its own subsection in Lesson_Learned.md, rather than burying it in a generic DHCP paragraph.
