# Verification
## Layer 2 verification 
### HQRT1
```bash
HQRT1#show ip int bri
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES TFTP   administratively down down    
Ethernet0/1            unassigned      YES TFTP   up                    up      
Ethernet0/1.10         10.10.10.1      YES manual up                    up      
Ethernet0/1.20         10.10.20.1      YES manual up                    up      
Ethernet0/1.30         10.10.30.1      YES manual up                    up      
Ethernet0/1.40         10.10.40.1      YES manual up                    up      
Ethernet0/1.50         10.10.50.1      YES manual up                    up      
Ethernet0/1.99         10.10.99.1      YES manual up                    up      
Ethernet0/2            unassigned      YES TFTP   administratively down down    
Ethernet0/3            unassigned      YES TFTP   administratively down down 
```
``` bash
HQRT1#show ip int bri
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES TFTP   administratively down down    
Ethernet0/1            unassigned      YES TFTP   up                    up      
Ethernet0/1.10         10.10.10.1      YES manual up                    up      
Ethernet0/1.20         10.10.20.1      YES manual up                    up      
Ethernet0/1.30         10.10.30.1      YES manual up                    up      
Ethernet0/1.40         10.10.40.1      YES manual up                    up      
Ethernet0/1.50         10.10.50.1      YES manual up                    up      
Ethernet0/1.99         10.10.99.1      YES manual up                    up      
Ethernet0/2            unassigned      YES TFTP   administratively down down    
Ethernet0/3            unassigned      YES TFTP   administratively down down
```
```bash
HQRT1#show ip int bri
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES TFTP   administratively down down    
Ethernet0/1            unassigned      YES TFTP   up                    up      
Ethernet0/1.10         10.10.10.1      YES manual up                    up      
Ethernet0/1.20         10.10.20.1      YES manual up                    up      
Ethernet0/1.30         10.10.30.1      YES manual up                    up      
Ethernet0/1.40         10.10.40.1      YES manual up                    up      
Ethernet0/1.50         10.10.50.1      YES manual up                    up      
Ethernet0/1.99         10.10.99.1      YES manual up                    up      
Ethernet0/2            unassigned      YES TFTP   administratively down down    
Ethernet0/3            unassigned      YES TFTP   administratively down down
```
```bash
HQRT1(config)#do show access-list 
Extended IP access list MTGMT-ACL
    10 permit ip 10.10.40.0 0.0.0.255 10.10.99.0 0.0.0.255
    20 deny ip any 10.10.99.0 0.0.0.255
Extended IP access list SRVR-ACL
    10 permit udp any host 10.10.50.10 eq domain
    20 permit tcp any host 10.10.50.10 eq domain
    30 permit tcp any host 10.10.50.10 eq 443
    40 permit udp host 8.8.8.8 eq domain host 10.10.50.10
    50 deny ip any 10.10.50.0 0.0.0.255 log
```
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
### HQRT1 is issuing DHCP
```bash
HQRT1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address      Client-ID/              Lease expiration        Type       State      Interface
                Hardware address/
                User name
10.10.10.21     0152.5400.edbe.2a       Oct 14 2026 01:46 AM    Automatic  Active     Ethernet0/1.10
10.10.20.21     0152.5400.5bab.93       Oct 14 2026 01:46 AM    Automatic  Active     Ethernet0/1.20
10.10.30.21     0152.5400.2646.d9       Oct 14 2026 01:46 AM    Automatic  Active     Ethernet0/1.30
10.10.40.21     0152.5400.4bfd.dc       Oct 14 2026 01:41 AM    Automatic  Active     Ethernet0/1.40
HQRT1#
```
