# Verification
## Layer 2 verification 
### HQRT1



### HQSW1
```bash
HQSW1#show int trunk   

Port           Mode             Encapsulation  Status        Native vlan  
Et0/0          on               802.1q         trunking      999
Et0/1          on               802.1q         trunking      999
Et0/2          on               802.1q         trunking      999

Port           Vlans allowed on trunk
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99
Et0/2          10,20,30,40,50,99

Port           Vlans allowed and active in management domain
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99
Et0/2          10,20,30,40,50,99

Port           Vlans in spanning tree forwarding state and not pruned
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50
Et0/2          10,20,30,40,50,99
```
```bash
HQSW1#show int trunk   

Port           Mode             Encapsulation  Status        Native vlan  
Et0/0          on               802.1q         trunking      999
Et0/1          on               802.1q         trunking      999
Et0/2          on               802.1q         trunking      999

Port           Vlans allowed on trunk
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99
Et0/2          10,20,30,40,50,99

Port           Vlans allowed and active in management domain
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99
Et0/2          10,20,30,40,50,99

Port           Vlans in spanning tree forwarding state and not pruned
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50
Et0/2          10,20,30,40,50,99
```
```bash
HQSW1#show spanning-tree br

VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    4106
             Address     aabb.cc00.0b00
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    4106   (priority 4096 sys-id-ext 10)
             Address     aabb.cc00.0b00
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p 
Et0/1               Desg FWD 100       128.2    P2p 
Et0/2               Desg FWD 100       128.3    P2p       
```
### HWSW2
```bash
HQSW2#show int trunk

Port           Mode             Encapsulation  Status        Native vlan  
Et0/0          on               802.1q         trunking      999
Et0/1          on               802.1q         trunking      999

Port           Vlans allowed on trunk
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99

Port           Vlans allowed and active in management domain
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99

Port           Vlans in spanning tree forwarding state and not pruned
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50
```
```bash
HQSW2#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
10   CORP                             active    
20   SALES                            active    
30   ENG                              active    Et0/3
40   IT                               active    
50   SERVERS                          active    Et0/2
99   MGMT                             active    
999  NATIVE-UNUSED                    active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup
```
```bash
HQSW2#show spanning-tree 

VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    4106
             Address     aabb.cc00.0b00
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    8202   (priority 8192 sys-id-ext 10)
             Address     aabb.cc00.0d00
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p 
Et0/1               Desg FWD 100       128.2    P2p
```
### HQSW3
```bash
HQSW3#show int trunk

Port           Mode             Encapsulation  Status        Native vlan  
Et0/0          on               802.1q         trunking      999
Et0/1          on               802.1q         trunking      999

Port           Vlans allowed on trunk
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99

Port           Vlans allowed and active in management domain
Et0/0          10,20,30,40,50,99
Et0/1          10,20,30,40,50,99

Port           Vlans in spanning tree forwarding state and not pruned
Et0/0          10,20,30,40,50
Et0/1          99
```
```bash
HQSW3#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
10   CORP                             active    Et0/2
20   SALES                            active    Et0/3
30   ENG                              active    
40   IT                               active    
50   SERVERS                          active    
99   MGMT                             active    
999  NATIVE-UNUSED                    active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup
```
```bash
HQSW3#show spanning-tree  

VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    4106
             Address     aabb.cc00.0b00
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32778  (priority 32768 sys-id-ext 10)
             Address     aabb.cc00.0c00
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p 
Et0/1               Altn BLK 100       128.2    P2p 
Et0/2               Desg FWD 100       128.3    P2p
```
