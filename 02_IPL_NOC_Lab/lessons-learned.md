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
