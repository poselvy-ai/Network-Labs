# Verification Phase 2 Branch
## BRRT1

## BBSW1
### Interfaces
```bash
BRSW1#show ip int br   
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  up                    up      
Ethernet0/1            unassigned      YES unset  up                    up      
Ethernet0/2            unassigned      YES unset  up                    up      
Ethernet0/3            unassigned      YES unset  up                    up      
Vlan99                 10.20.99.11     YES manual administratively down down    
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
