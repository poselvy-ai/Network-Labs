[← Back to lab README](../README.md)

# Carrier Circuits and NOC Contacts (fictional)

Have this information ready before opening a carrier ticket. Carriers identify a service by **circuit ID** and **account number**, not by our device names.

| Carrier | Circuit ID | Account | Our side | Their side | Our AS / their AS | Carrier NOC | Escalation |
|---------|-----------|---------|----------|------------|-------------------|-------------|------------|
| ISP-A (primary) | **ISPA-ETH-0451-IPL** | IPL-77310 | EDGE-R1 E0/0, 203.0.113.2/30 | 203.0.113.1 | 65001 / 65100 | noc@isp-a.example · +1-555-0100 (24×7) | Duty manager after 2 h, no update |
| ISP-B (backup) | **ISPB-FIB-2210-IPL** | IPL-55120 | EDGE-R1 E0/1, 203.0.113.6/30 | 203.0.113.5 | 65001 / 65200 | noc@isp-b.example · +1-555-0200 (24×7) | Duty manager after 2 h, no update |

## A carrier ticket should include
- Our company, account number and **circuit ID**
- Both IP addresses on the link, and both AS numbers
- Start time in **local and UTC**
- What we tested on our side and the results (interface status, ping to their address, BGP state)
- Impact (service down / running on backup)
- A callback name and number, and the NOC ticket number

---

[← Back to lab README](../README.md)
