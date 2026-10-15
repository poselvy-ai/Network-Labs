# Verification Phase 2 Branch
## ISP 
### IP ROUTE
```bash
      8.0.0.0/32 is subnetted, 1 subnets
C        8.8.8.8 is directly connected, Loopback0
      203.0.113.0/24 is variably subnetted, 4 subnets, 2 masks
C        203.0.113.8/30 is directly connected, Ethernet0/0
L        203.0.113.9/32 is directly connected, Ethernet0/0
C        203.0.113.12/30 is directly connected, Ethernet0/1
L        203.0.113.13/32 is directly connected, Ethernet0/1
```

## BRRT1
### IP ROUTE
```bash
Gateway of last resort is 203.0.113.13 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 203.0.113.13
      10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C        10.20.10.0/24 is directly connected, Ethernet0/0.10
L        10.20.10.1/32 is directly connected, Ethernet0/0.10
C        10.20.20.0/24 is directly connected, Ethernet0/0.20
L        10.20.20.1/32 is directly connected, Ethernet0/0.20
C        10.20.30.0/24 is directly connected, Ethernet0/0.30
L        10.20.30.1/32 is directly connected, Ethernet0/0.30
C        10.20.99.0/24 is directly connected, Ethernet0/0.99
L        10.20.99.1/32 is directly connected, Ethernet0/0.99
      203.0.113.0/24 is variably subnetted, 2 subnets, 2 masks
C        203.0.113.12/30 is directly connected, Ethernet0/1
L        203.0.113.14/32 is directly connected, Ethernet0/1
```
### Interfaces
```bash
BRRT1# show ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES NVRAM  up                    up      
Ethernet0/0.10         10.20.10.1      YES manual up                    up      
Ethernet0/0.20         10.20.20.1      YES manual up                    up      
Ethernet0/0.30         10.20.30.1      YES manual up                    up      
Ethernet0/0.99         10.20.99.1      YES manual up                    up      
Ethernet0/1            203.0.113.14    YES manual up                    up      
Ethernet0/2            unassigned      YES NVRAM  administratively down down    
Ethernet0/3            unassigned      YES NVRAM  administratively down down
```
### NAT Access-list
```bash
BRRT1#show access-list
Standard IP access list NAT-BRANCH
    10 permit 10.20.0.0, wildcard bits 0.0.255.255
Extended IP access list BR-MGMT-ACL
    10 permit ip 10.10.40.0 0.0.0.255 10.20.99.0 0.0.0.255
    20 deny ip any 10.20.99.0 0.0.0.255 log
    30 permit ip any any
```
### NAT Translation 
```
BRRT1#show ip nat stat 
Total active translations: 1 (0 static, 1 dynamic; 1 extended)
Outside interfaces:
  Ethernet0/1
Inside interfaces: 
  Ethernet0/0.10, Ethernet0/0.20, Ethernet0/0.30, Ethernet0/0.99
Hits: 808  Misses: 0
CEF Translated packets: 803, CEF Punted packets: 0
 Reserved port setting disabled provisioned no
 Dynamic overload mapping configured: 1
Expired translations: 3
Dynamic mappings:
-- Inside Source
[Id: 1] access-list NAT-BRANCH interface Ethernet0/1 refcount 1
nat-limit statistics:
 max entry: max allowed 0, used 0, missed 0
```
### DHCP
#### DHCP Pools 
```bash
BRRT1#
BRRT1#show ip dhcp pool

Pool CORP :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 1
 Excluded addresses             : 20
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased/Excluded/Total
 10.20.10.22          10.20.10.1       - 10.20.10.254      1     / 20    / 254  

Pool SALES :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 1
 Excluded addresses             : 20
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased/Excluded/Total
 10.20.20.22          10.20.20.1       - 10.20.20.254      1     / 20    / 254  

Pool ENG :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 1
 Excluded addresses             : 20
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased/Excluded/Total
 10.20.30.22          10.20.30.1       - 10.20.30.254      1     / 20    / 254 
```
#### DHCP Lease
```bash
Bindings from all pools not associated with VRF:
IP address      Client-ID/              Lease expiration        Type       State      Interface
                Hardware address/
                User name
10.20.10.21     0152.5400.e1ff.48       Oct 15 2026 11:01 PM    Automatic  Active     Ethernet0/0.10
10.20.20.21     0152.5400.f19c.4f       Oct 15 2026 11:01 PM    Automatic  Active     Ethernet0/0.20
10.20.30.21     0152.5400.a68f.cf       Oct 15 2026 11:01 PM    Automatic  Active     Ethernet0/0.30
```


## BSW1
### Interfaces
```bash
BRSW1#show ip int br   
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  up                    up      
Ethernet0/1            unassigned      YES unset  up                    up      
Ethernet0/2            unassigned      YES unset  up                    up      
Ethernet0/3            unassigned      YES unset  up                    up      
Vlan99                 10.20.99.11     YES NVRAM  up                    up      
```
### VLANs
```bash
BRSW1#show vlan br  

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
10   CORP                             active    Et0/1
20   SALES                            active    Et0/2
30   ENG                              active    Et0/3
99   MGMT                             active    
999  NATIVE-UNUSED                    active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup
```
### Trunks
```bash
BRSW1#show int trunk

Port           Mode             Encapsulation  Status        Native vlan  
Et0/0          on               802.1q         trunking      999

Port           Vlans allowed on trunk
Et0/0          10,20,30,99

Port           Vlans allowed and active in management domain
Et0/0          10,20,30,99

Port           Vlans in spanning tree forwarding state and not pruned
Et0/0          10,20,30,99
```
### Spanning-tree 
```bash

BRSW1#show spanning-tree 

VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    4106
             Address     aabb.cc00.0200
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    4106   (priority 4096 sys-id-ext 10)
             Address     aabb.cc00.0200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p 
Et0/1               Desg FWD 100       128.2    P2p 



VLAN0020
  Spanning tree enabled protocol rstp
  Root ID    Priority    4116
          
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------

             Address     aabb.cc00.0200
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    4116   (priority 4096 sys-id-ext 20)
             Address     aabb.cc00.0200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p 
Et0/2               Desg FWD 100       128.3    P2p 



VLAN0030
  Spanning tree enabled protocol rstp
  Root ID    Priority    4126
             Address     aabb.cc00.0200
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

          
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------

  Bridge ID  Priority    4126   (priority 4096 sys-id-ext 30)
             Address     aabb.cc00.0200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p 
Et0/3               Desg FWD 100       128.4    P2p 



VLAN0099
  Spanning tree enabled protocol rstp
  Root ID    Priority    4195
             Address     aabb.cc00.0200
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    4195   (priority 4096 sys-id-ext 99)
             Address     aabb.cc00.0200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
          
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------


Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
```
## PC-BR-SALES
### DHCP Address 
```test
eth0      Link encap:Ethernet  HWaddr 52:54:00:F1:9C:4F  
          inet addr:10.20.20.21  Bcast:0.0.0.0  Mask:255.255.255.0
```
