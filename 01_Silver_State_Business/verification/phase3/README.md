# WAN Build Validation
## VPN Tunnel 
### HQRT1
#### Tunnel10
```bash
HQRT1#show int tunnel10
Tunnel10 is up, line protocol is up 
  Hardware is Tunnel
  Internet address is 172.31.0.1/30
  MTU 17878 bytes, BW 100 Kbit/sec, DLY 50000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation TUNNEL, loopback not set
  Keepalive not set
  Tunnel linestate evaluation up
  Tunnel source 203.0.113.10 (Ethernet0/0), destination 203.0.113.14
   Tunnel Subblocks:
      src-track:
         Tunnel10 source tracking subblock associated with Ethernet0/0
          Set of tunnels with source Ethernet0/0, 1 member (includes iterators), on interface <OK>
  Tunnel protocol/transport IPSEC/IP
  Tunnel TTL 255
  Tunnel transport MTU 1438 bytes
  Tunnel transmit bandwidth 8000 (kbps)
  Tunnel receive bandwidth 8000 (kbps)
  Tunnel protection via IPSec (profile "SSB-IPSEC-PROF")
  Tunnel state info: 
    State change to up   : Oct 16 2026 23:15:33
    State change to down : Oct 16 2026 23:41:23
    Last state info      : down - linestate protection reg down
  Last input 01:10:49, output 01:10:49, output hang never
  Last clearing of "show interface" counters 01:22:17
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/0 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     15 packets input, 1740 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     15 packets output, 1740 bytes, 0 underruns
     Output 0 broadcasts (0 IP multicasts)
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 output buffer failures, 0 output buffers swapped out
 ```

#### IPSEC Tunnel
```bash
HQRT1#show crypto ikev2 sa
 IPv4 Crypto IKEv2  SA 

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
1         203.0.113.10/500      203.0.113.14/500      none/none            READY  
      Encr: AES-CBC, keysize: 256, PRF: SHA256, Hash: SHA256, DH Grp:14, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/1187 sec
      CE id: 1034, Session-id: 1
      Local spi: C3D93A6A42ACE2F4       Remote spi: 2FED647870D4A78F

 IPv6 Crypto IKEv2  SA 
 ```
### BRRT1
#### Tunnel10
```bash
BRRT1#show int tunnel10
Tunnel10 is up, line protocol is up 
  Hardware is Tunnel
  Internet address is 172.31.0.2/30
  MTU 17878 bytes, BW 100 Kbit/sec, DLY 50000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation TUNNEL, loopback not set
  Keepalive not set
  Tunnel linestate evaluation up
  Tunnel source 203.0.113.14 (Ethernet0/1), destination 203.0.113.10
   Tunnel Subblocks:
      src-track:
         Tunnel10 source tracking subblock associated with Ethernet0/1
          Set of tunnels with source Ethernet0/1, 1 member (includes iterators), on interface <OK>
  Tunnel protocol/transport IPSEC/IP
  Tunnel TTL 255
  Tunnel transport MTU 1438 bytes
  Tunnel transmit bandwidth 8000 (kbps)
  Tunnel receive bandwidth 8000 (kbps)
  Tunnel protection via IPSec (profile "SSB-IPSEC-PROF")
  Tunnel state info: 
    State change to up   : Oct 16 2026 23:21:42
    State change to down : Oct 16 2026 23:56:38
    Last state info      : down - linestate protection reg down
  Last input 01:12:28, output 01:12:28, output hang never
  Last clearing of "show interface" counters 01:21:37
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/0 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     15 packets input, 1740 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     15 packets output, 1740 bytes, 0 underruns
     Output 0 broadcasts (0 IP multicasts)
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 output buffer failures, 0 output buffers swapped out
```
#### IPSEC 
```bash
BRRT1#show crypto ikev2 sa
 IPv4 Crypto IKEv2  SA 

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
2         203.0.113.14/500      203.0.113.10/500      none/none            READY  
      Encr: AES-CBC, keysize: 256, PRF: SHA256, Hash: SHA256, DH Grp:14, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/1415 sec
      CE id: 1034, Session-id: 32
      Local spi: 2FED647870D4A78F       Remote spi: C3D93A6A42ACE2F4
```

## OSPF 
### HQRT1
#### Neighbor 
```
HQRT1#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
10.20.255.1       0   FULL/  -        00:00:31    172.31.0.2      Tunnel10
```
### Interface sourced by OSPF
```bash
HQRT1#show ip route ospf     

Gateway of last resort is 203.0.113.9 to network 0.0.0.0

      10.0.0.0/8 is variably subnetted, 16 subnets, 2 masks
O        10.20.10.0/24 [110/1010] via 172.31.0.2, 00:02:03, Tunnel10
O        10.20.20.0/24 [110/1010] via 172.31.0.2, 00:02:03, Tunnel10
O        10.20.30.0/24 [110/1010] via 172.31.0.2, 00:02:03, Tunnel10
O        10.20.99.0/24 [110/1010] via 172.31.0.2, 00:02:03, Tunnel10
```
#### OSPF Route
```bash
HQRT1#show ip route ospf

Gateway of last resort is 203.0.113.9 to network 0.0.0.0
```
## BRRT1
#### Neighbor 
```bash
BRRT1#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
10.10.255.1       0   FULL/  -        00:00:30    172.31.0.1      Tunnel10
```
### Interface sourced by OSPF
```bash
BRRT1#show ip ospf int br
Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
Et0/0.99     1     0               10.20.99.1/24      10    WAIT  0/0
Et0/0.30     1     0               10.20.30.1/24      10    WAIT  0/0
Et0/0.20     1     0               10.20.20.1/24      10    WAIT  0/0
Et0/0.10     1     0               10.20.10.1/24      10    WAIT  0/0
Tu10         1     0               172.31.0.2/30      1000  P2P   1/1
```
#### OSPF Route
```bash
BRRT1#show ip route ospf

Gateway of last resort is 203.0.113.13 to network 0.0.0.0

      10.0.0.0/8 is variably subnetted, 14 subnets, 2 masks
O        10.10.10.0/24 [110/1010] via 172.31.0.1, 00:23:11, Tunnel10
O        10.10.20.0/24 [110/1010] via 172.31.0.1, 00:23:11, Tunnel10
O        10.10.30.0/24 [110/1010] via 172.31.0.1, 00:23:11, Tunnel10
O        10.10.40.0/24 [110/1010] via 172.31.0.1, 00:23:11, Tunnel10
O        10.10.50.0/24 [110/1010] via 172.31.0.1, 00:23:11, Tunnel10
O        10.10.99.0/24 [110/1010] via 172.31.0.1, 00:23:11, Tunnel10
```
[BACK](../README.md)


