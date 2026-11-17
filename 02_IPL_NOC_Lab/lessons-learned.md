# Lessons Learned — IPL NOC Lab

Format for each entry: **Symptom → Evidence → Cause → Fix → Lesson.**

---

## 1. Lab setup

### 1.1 ASAv would not accept a blank enable password
- **Symptom:** First `enable` on FW1 asked for a password to be set; a blank one was not accepted.
- **Cause:** Current ASA software requires an enable password to be set the first time.
- **Fix:** Set a lab password, then `write memory`.
- **Lesson:** Failed or blank attempts don't lock an ASA out unless a lockout is configured; in CML the worst case is Stop → Wipe the node.

### 1.2 CML disk usage at 76% after importing the main refplat ISO
- **Symptom:** Dashboard showed 76% disk used on an 80 GB VM.
- **Cause:** Since CML 2.7, every reference image is copied to the local disk.
- **Fix:** Skipped the supplemental ISO; wipe finished labs.
- **Lesson:** Size the CML disk for the images you plan to import, not just the labs.

---

## 2. Tickets

### 2.1 V1-TICKET-001: DMZ server unreachable from other subnets ([archived](./tickets/archive/V1-TICKET-001.md))
- **Symptom:** Internal hosts and the internet could not reach DMZ-SRV1; FW1 itself could.
- **Evidence:** packet-tracer allowed; FW1 capture showed echo requests out to the DMZ and no replies; DMZ-SRV1 `show ip arp` had `10.2.10.10 Incomplete`.
- **Cause:** DMZ-SRV1 in host mode (`no ip routing` + `ip default-gateway`) ARPed for remote hosts; the ASA does not proxy-ARP for them.
- **Fix:** `ip routing` + `ip route 0.0.0.0 0.0.0.0 172.16.50.1` on DMZ-SRV1.
- **Lesson:** Proxy ARP on IOS routers hides host misconfigurations; a firewall as gateway exposes them. Check the ARP table, not just gateway pings.

### 2.2 V1-TICKET-002: DMZ "web server" refused HTTP ([archived](./tickets/archive/V1-TICKET-002.md))
- **Symptom:** TCP/80 refused from every source, including the server itself; port 80 showed LISTEN.
- **Cause:** An IOL router's built-in HTTP service is not a production web server.
- **Fix:** Rebuilt the lab (v2) with an Ubuntu web server in the DMZ.
- **Lesson:** Lab stand-ins should behave like what they represent, or the tickets teach the wrong things.
