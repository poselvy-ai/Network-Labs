# Verification
## Layer 2 verification 
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

                                                   Hello  Max  Fwd
Vlan                         Bridge ID              Time  Age  Dly  Protocol
---------------- --------------------------------- -----  ---  ---  --------
VLAN0010          4106 ( 4096,  10) aabb.cc00.0b00    2    20   15  rstp        
VLAN0020          4116 ( 4096,  20) aabb.cc00.0b00    2    20   15  rstp        
VLAN0030          4126 ( 4096,  30) aabb.cc00.0b00    2    20   15  rstp        
VLAN0040          4136 ( 4096,  40) aabb.cc00.0b00    2    20   15  rstp        
VLAN0050          4146 ( 4096,  50) aabb.cc00.0b00    2    20   15  rstp        
VLAN0099          4195 ( 4096,  99) aabb.cc00.0b00    2    20   15  rstp       
```
