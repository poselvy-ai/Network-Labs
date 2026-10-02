# Lessons Learned. 
## Diagram
![SSB Diagram](./diagrams/SSB_Network_Diagram(V1).png)

## SVI Troubleshooting. 
During the building of of the network I was not able to build the sub interfaces off of Port FA0 on HQRT1 as it built as L2 port instead of an L3 port. 
While validating my configuration from a PC with the basic configuration for Corporate VLAN member and Sales Member I was not able to ping the *Default Gateway*. During my troubleshooting troubleshooting process I started from the HQRT1 moving to the PC I did.
1. I checked to ensure that the *routes* where properly showing connected and the *VLAN* were properly configured according to the design. I also ensured that the *Trunk* was able to pass the *VLAN* across.
2. I moved the HQSW1 and verified the port was in *access mode*, the *Trunk*, can pass the *VLAN*, and the port was assigned to the correct *VLAN*. I also, checked the **Interface Status** to check the connection of the wire which I did have to change out.
3. After troubleshooting Layer-1 & Layer-2, I went back to HQRT and verified using the *ARP* & *MAC-Address Table* command to see if HQRT1 was adding the MAC address of the PC to the to the table which it was.
4. After research I remember SVI do not act like a typical interface and you can directly ping them and aspect an echo, they are use to route traffic properly for the router.
5. I confirmed this by having the two PCs ping each other from different switch and they successfully replied. 
