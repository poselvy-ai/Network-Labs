# Lessons Learned

Record problems as you hit them. Format: **Symptom → Cause → Fix → What I'd do differently.**

## Packet Tracer → CML migration
- **Symptom:** Configs captured from the CLI contained `--More--` prompts, syslog messages, and failed commands.
  **Cause:** Copying `show run` output straight from the terminal.
  **Fix:** `terminal length 0` before `show run`, or use CML's *Extract configurations* and copy from the node's Config tab.
- **Symptom:** Device configs included long `crypto pki certificate` blocks.
  **Cause:** `ip http secure-server` auto-generates a self-signed certificate.
  **Fix:** `no ip http server` / `no ip http secure-server` (unneeded in the lab), then `no crypto pki trustpoint TP-self-signed-...`.
- **Symptom:** STP root changed after a reload / config looked "lost".
  **Cause:** Changes made but not saved with `copy running-config startup-config` before stopping the node.
  **Fix:** Save on every device before stopping the lab; extract configs in CML.
- Packet Tracer interface names (Gi0/0, Fa0/1) don't exist on CML IOL nodes (Ethernet0/0...). Diagrams now use the CML names.


**Symptom**: VLAN 99 STP topology differed from VLANs 10–50. HQSW3 saw HQSW2 as root for VLAN 99, E0/0 stuck Desg BLK, and HQSW1 E0/2 / HQSW3 E0/0 were both designated.
**Evidence**: HQSW1 sent 1753 BPDUs; HQSW3 received 760. 148 forwarding transitions on HQSW3 E0/0. Input errors increasing on HQSW1 E0/2.
**Cause**: Faulty HQSW1 E0/2 ↔ HQSW3 E0/0 link dropping frames. Lost BPDUs caused RSTP to repeatedly reconverge VLAN 99.
**Fix**: Bouncing the ports didn’t help. Deleting and recreating the link in CML fixed it.
**Lesson**: Verify STP per VLAN, not just one. BPDU sent/received counters reveal one-way loss. In production, UDLD and Loop Guard protect against this failure.
