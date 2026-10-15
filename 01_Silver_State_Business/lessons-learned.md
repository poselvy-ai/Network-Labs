# Lessons Learned — Silver State Business Solutions

Format for each entry: **Symptom → Evidence → Cause → Fix → Lesson.**
Entries are grouped by build area, roughly in the order they were hit during Phase 1 (HQ).

---

## 1. Packet Tracer → CML migration

### 1.1 Captured configs were unusable
- **Symptom:** Saved configs contained `--More--` prompts, syslog messages, failed `copy` attempts, and long `crypto pki certificate` blocks.
- **Cause:** `show run` copied straight from the terminal; `ip http secure-server` auto-generates a self-signed certificate.
- **Fix:** `terminal length 0` before `show run`; `no ip http server` / `no ip http secure-server`; use CML *Extract configurations*.
- **Lesson:** A config file in the repo is evidence. Capture it cleanly or it undermines the work.

### 1.2 Settings "disappeared" after a reload
- **Symptom:** STP root priority on HQSW1 was missing after the lab restarted.
- **Cause:** Changes were never saved before stopping the node.
- **Fix:** `copy running-config startup-config` on every device before stopping the lab.
- **Lesson:** Save at every checkpoint, not just at the end.

### 1.3 Adding a NIC to SRV1 required a wipe
- **Symptom:** CML would not add a second interface to the running Ubuntu node; the node had to be wiped.
- **Cause:** VM nodes need their interface slots defined before boot.
- **Fix:** Wiped SRV1 and re-applied its network configuration.
- **Lesson:** Give server nodes their extra NIC slots **before** configuring them. Put persistent settings (hostname, static IP) in the node's CML Config tab (cloud-init) so a wipe rebuilds them automatically.

---

## 2. Layer 2 — Spanning Tree

### 2.1 VLAN 99 built a different STP topology than VLANs 10–50
- **Symptom:** For VLANs 10–50, HQSW3 E0/1 was `Altn BLK` as designed. For VLAN 99, HQSW3 believed **HQSW2** was root, E0/0 was stuck `Desg BLK`, and both ends of the HQSW1–HQSW3 link claimed Designated.
- **Evidence:**
  - HQSW1 E0/2 sent **1753** BPDUs; HQSW3 E0/0 received only **760**.
  - HQSW3 E0/0: **148** transitions to forwarding (flapping).
  - Input errors increasing on HQSW1 E0/2.
- **Cause:** Faulty HQSW1 E0/2 ↔ HQSW3 E0/0 link dropping frames one-way. Lost BPDUs caused RSTP to repeatedly reconverge.
- **Fix:** Bouncing the ports did not help. Deleting and recreating the link in CML fixed it.
- **Lesson:** Verify STP **per VLAN**, not just one. BPDU sent/received counters expose one-way loss. Two "Designated" ends on one link means BPDUs are not crossing it. In production, **UDLD** and **Loop Guard** protect against this failure.

### 2.2 PortFast not applied
- **Symptom:** Access ports showed Type `P2p` instead of `P2p Edge`.
- **Fix:** `spanning-tree portfast` (or `portfast edge`) and `spanning-tree bpduguard enable` on access ports.
- **Lesson:** `show spanning-tree` confirms hardening; don't assume a command took.

---

## 3. Device management

### 3.1 SSH key command flagged as hidden
- **Symptom:** `%PARSER-5-HIDDEN: 'crypto key generate rsa' is a hidden command`.
- **Cause:** IOS XE 17.x deprecates the short form.
- **Fix:** `crypto key generate rsa general-keys modulus 2048`.
- **Lesson:** Key generation requires `hostname` and `ip domain name` first; SSH also needs a local user and `login local` on the VTY lines.

### 3.2 Switch management depends on a default gateway
- **Lesson:** With `no ip routing`, a switch is a host on VLAN 99 and needs `ip default-gateway 10.10.99.1` to answer anything outside its subnet. Same-subnet pings do not prove it; test with `ping <switch> source Ethernet0/1.40` from HQRT1.

---

## 4. Routing, DHCP, and PAT

### 4.1 Default route configured but "Gateway of last resort is not set"
- **Symptom:** `ip route 0.0.0.0 0.0.0.0 203.0.113.9` present in the config but not in the routing table.
- **Cause:** E0/0 IP/mask misconfigured (a /29 mask, 255.255.255.248, used instead of /30, 255.255.255.252), so the next hop was not in a connected subnet.
- **Fix:** Corrected the mask on E0/0.
- **Lesson:** IOS only installs a static route when its **next hop is reachable**. Check with `show ip route <next-hop>`. Mask math: 256 − block size (/30 → block 4 → .252).

### 4.2 DHCP exclusions for static-only VLANs
- **Decision:** VLANs 50 and 99 are fully excluded from DHCP.
- **Lesson:** IOS does **not** warn when a pool is created over an excluded range. The exclusion prevents **leases**, not configuration. Describe controls by what they actually do.

### 4.3
 - **Symptom**: Ip 10.20.0.1 could not reach IP 8.8.8.8
 - **Cause**: Failed to activate the NAT *BRRT1(config)# ip nat inside source list NAT-BRANCH interface Ethernet0/1 overload*
 - **Fix**: Implemented the *BRRT1(config)# ip nat inside source list NAT-BRANCH interface Ethernet0/1 overload*
 - **Lesson**: When the NAT rule is not in place the *inside*,*outside*,and *ACL* that are needed for NAT/PAT will not work, as the binding does not take place.

---

## 5. SRV1 (Ubuntu) build

### 5.1 Getting packages onto an isolated node
- **Symptom:** dnsmasq and nginx not installed; CML lab has no internet (8.8.8.8 is ISP1's loopback).
- **Fix:** Temporary External Connector on ens3 with a separate netplan file (`dhcp4: true`, `route-metric: 50`), install, then delete the file, the link, and the connector.
- **Lessons:**
  - The connector came up in **System Bridge** mode (192.168.0.x), putting SRV1 on the home LAN. NAT mode isolates it better. Remove it immediately after use either way.
  - With two default routes, Linux uses the **lowest metric**. A netplan static route with no metric is metric 0 and wins. `ip route get <dest>` shows the route actually used.
  - `dpkg -l` uses a lowercase **L**, not the digit 1.
  - In nano, save with **Ctrl+O → Enter**; do not choose Mac/DOS format.

---

## 6. ACLs

### 6.1 Return traffic is filtered by an outbound ACL
- **Symptom:** SRV1's pings and DNS forwarding failed with the server ACL applied.
- **Cause:** An outbound ACL on E0/1.50 filters **everything entering VLAN 50**, including replies to connections SRV1 starts (echo-reply, DNS answers from 8.8.8.8).
- **Fix:** Permit the replies explicitly (`permit icmp any host 10.10.50.10`, `permit udp host 8.8.8.8 eq domain host 10.10.50.10`).
- **Lesson:** For an outbound ACL, ask "what is the destination as the packet **leaves** this interface?"

### 6.2 Router-originated traffic WAS filtered
- **Evidence:** ACL log showed `denied icmp 10.10.50.1 -> 10.10.50.10` (the router's own pings and replies).
- **Lesson:** Classic IOS exempts router-generated traffic from outbound ACLs, but **this IOS XE 17.16 image did not**. Trust the ACL log over the rule of thumb.

### 6.3 Typo'd ACL names silently created new ACLs
- **Symptom:** Fixes "didn't work."
- **Evidence:** `show access-lists` listed `SRVR-ACL`, `SRVER-ACL`, `SVER-ACL`, `MTGMT-ACL`, and an ACL literally named `no`.
- **Cause:** IOS creates a new ACL for any unrecognized name; edits went to ACLs not applied to the interface. The ACL named `no` came from typing `ip access-list extended no ...` instead of `no ip access-list extended ...`.
- **Fix:** Removed the strays; rebuilt under one exact name.
- **Lesson:** After every ACL change, run `show run | include access-group` and `show access-lists` together and confirm the names match.

### 6.4 Implicit deny vs. an empty ACL
- **Observation:** A one-line ACL blocked everything except that line; an applied ACL with **no entries** permitted everything.
- **Lesson:** Every ACL ends in an invisible implicit deny with no counter and no log. Use an explicit `deny ... log` so drops are visible.

### 6.5 IOL egress ACL misparses ARP as IP (platform quirk)
- **Symptom:** With `1000 deny ip any 10.10.50.0 0.0.0.255 log` on SRVR-ACL, SRV1 could not reach its gateway ("Destination Host Unreachable", `ip neigh` = `INCOMPLETE`), yet **line 1000 showed 0 matches**. Replacing line 1000 with `permit ip any any log` restored traffic.
- **Evidence:** ACL log entry `permitted 187 10.16.10.10 -> 50.1.82.84`. Decoded against an ARP reply from HQRT1 to SRV1:

  | ARP bytes | Contents | Misread as IP field |
  |---|---|---|
  | byte 9 | `bb` (HQRT1 MAC `aabb.cc00…`) | Protocol 187 |
  | bytes 12–15 | end of HQRT1 MAC + `0a 0a` | Source 10.16.10.10 |
  | bytes 16–19 | `32 01` (end of 10.10.50.1) + `52 54` (start of SRV1 MAC) | Destination 50.1.82.84 |

- **Cause:** The emulated router evaluated outgoing **ARP replies** against the IP ACL. The misread "packet" matched no permit and was not in 10.10.50.0/24, so it hit the **implicit deny** (no counter, no log). ARP replies never reached SRV1.
- **Fix:** Added a final line after the explicit deny, with a remark, on SRVR-ACL and on the MGMT ACL:
  ```
  1010 remark IOL quirk: egress ACL misparses ARP as IP; permit non-subnet destinations
  1010 permit ip any any
  ```
  The policy is unchanged: traffic to the protected subnet is still denied and logged by the explicit deny.
- **Lessons:**
  - Isolate one variable at a time. Toggling only line 1000 between permit and deny proved the ACL was involved even though its counters said otherwise.
  - Zero matches on an explicit deny plus broken traffic points to the **implicit deny**.
  - Decode "impossible" log entries; they often reveal the mechanism.
  - Real hardware does not filter ARP with IP ACLs. This workaround would not be needed on IOSv or Catalyst 8000v.
 
