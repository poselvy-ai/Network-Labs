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
