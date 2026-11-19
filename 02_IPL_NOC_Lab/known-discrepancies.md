[← Back to lab README](README.md)

# Known Discrepancies (NOC shift notes)

Differences between labels/documentation and how the network is actually used. Read at the start of each shift. Each item is reported to the documentation owner and stays here until an engineering update fixes it.

| # | Item | Labeled as | Actually | Reported to | Status |
|---|------|------------|----------|-------------|--------|
| 1 | HQ-PC1 (10.0.10.10) | HQ user workstation (CML label `HQ-PC1`, description `LAN`) | **NOC workstation**: "test from the NOC" is run from here | Network Engineering (documentation) | Open: relabel in next lab release |


## Expected behavior (not faults)

| Behavior | Why |
|----------|-----|
| `traceroute` from inside hosts (e.g. HQ-PC1) shows hop 1, then `* * *` | FW1 (ASA) doesn't appear in traces and doesn't pass outside routers' time-exceeded replies inbound. To see the ISP path, trace from EDGE-R1 with `source 198.51.100.1` |
| First ping after a quiet period shows `.!!!!` | First packet waits for ARP |

---

[← Back to lab README](README.md)
