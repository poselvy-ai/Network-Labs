# 2026-10-5 Baseline configurations
## Router
### HQRT1
```bash
HWSW3#show run
Building configuration...

Current configuration : 998 bytes
!
! Last configuration change at 22:57:19 UTC Mon Oct 5 2026
!
version 17.16
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname HWSW3
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
!
ip audit notify log
ip audit po max-events 100
ip cef
login on-success log
no ipv6 cef
!
memory free low-watermark processor 79497
!         
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
!
vlan internal allocation policy ascending
!
interface Ethernet0/0
!
interface Ethernet0/1
 description HQSW3 to HQSW1
!
interface Ethernet0/2
 description HQSW3 to HQSW2
!
interface Ethernet0/3
!
interface Vlan222
 ip address 172.16.10.253 255.255.255.0
 shutdown
!
ip forward-protocol nd
ip forward-protocol udp
!
ip http server
ip http secure-server
ip ssh bulk-mode 131072
!
no logging btrace
!
control-plane
!
line con 0
 logging synchronous
line aux 0
line vty 0 4
 login    
 transport input ssh
!
end
```
## Switches 
### HQSW1
```bash
HQSW1#show run
Building configuration...
Current configuration : 1360 bytes
!
! Last configuration change at 22:38:51 UTC Mon Oct 5 2026
!
version 17.16
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname HQSW1
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
!

ip audit notify log
ip audit po max-events 100
ip cef
login on-success log
no ipv6 cef
!

memory free low-watermark processor 79497
!         
spanning-tree mode rapid-pvst
spanning-tree extend system-id
spanning-tree vlan 10,20,30,222 priority 24576
!
vlan internal allocation policy ascending
!
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,222
 switchport mode trunk
!
interface Ethernet0/1
 description HQSW1 to HQRT1
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,222
 switchport mode trunk
!         
interface Ethernet0/2
 description SQSW1 to SQSW3
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,222
 switchport mode trunk
!
interface Ethernet0/3
!
interface Vlan222
 ip address 172.16.10.251 255.255.255.0
 shutdown
!
ip forward-protocol nd
ip forward-protocol udp
!
!
ip http server
ip http secure-server
ip ssh bulk-mode 131072
!
no logging btrace
!
control-plane
!
line con 0
 logging synchronous
line aux 0
line vty 0 4
 login
 transport input ssh
!
end
```
### HQSW2
```bash
HQSW2#show run
Building configuration...

Current configuration : 1103 bytes
!
! Last configuration change at 22:47:03 UTC Mon Oct 5 2026
!
version 17.16
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname HQSW2
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
!
ip audit notify log
ip audit po max-events 100
ip cef
login on-success log
no ipv6 cef
!
memory free low-watermark processor 79497
!         
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface Ethernet0/0
 description HQSW2 to HQSW1
!
interface Ethernet0/1
 description HQSW2 to HQSW3
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,222
 switchport mode trunk
!
interface Ethernet0/2
!
interface Ethernet0/3
!
interface Vlan222
 ip address 172.16.10.152 255.255.255.0
 shutdown
!
ip forward-protocol nd
ip forward-protocol udp
!
ip http server
ip http secure-server
ip ssh bulk-mode 131072
!
no logging btrace
!
!
control-plane
!
line con 0
 logging synchronous
line aux 0
line vty 0 4
 login
 transport input ssh
!
end
```
### HQSW3
```bash
HWSW3#show run
Building configuration...

Current configuration : 998 bytes
!
! Last configuration change at 22:57:19 UTC Mon Oct 5 2026
!
version 17.16
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname HWSW3
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
!
ip audit notify log
ip audit po max-events 100
ip cef
login on-success log
no ipv6 cef
!
!
memory free low-watermark processor 79497
!         
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
!
vlan internal allocation policy ascending
!
interface Ethernet0/0
!
interface Ethernet0/1
 description HQSW3 to HQSW1
!
interface Ethernet0/2
 description HQSW3 to HQSW2
!
interface Ethernet0/3
!
interface Vlan222
 ip address 172.16.10.253 255.255.255.0
 shutdown
!
ip forward-protocol nd
ip forward-protocol udp
!
ip http server
ip http secure-server
ip ssh bulk-mode 131072
!
no logging btrace
!
control-plane
!
line con 0
 logging synchronous
line aux 0
line vty 0 4
 login    
 transport input ssh
!
end
```
