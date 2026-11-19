[← Back to TICKET-002](TICKET-002.md) · [← Back to lab README](../README.md)

# TICKET-002 — Communications log

Every message linked to TICKET-002, in full and in order. Times are PST, as sent. Analyst messages are verbatim.

| # | Time | From → To | Subject / type |
|---|------|-----------|----------------|
| [C1](#c1) | 17:51 | Analyst → Ops Lead | EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Investigating |
| [C2](#c2) | 18:25 | Analyst → ISP-A NOC | IPL - TICKET-002 - BGP IP Unreachable |
| [C3](#c3) | 18:28 | ISP-A NOC → Analyst | Re: carrier ticket ISPA-INC-88213 opened, information requested |
| [C4](#c4) | 18:28 | Ops Lead → Analyst | Update overdue |
| [C5](#c5) | 18:30 | Analyst → Ops Lead | EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Working |
| [C6](#c6) | 18:32 | Ops Lead → Analyst | Circuit ID and account provided |
| [C7](#c7) | 18:36 | Analyst → ISP-A NOC | ISPA-INC-88213 (circuit ID, account, AS) |
| [C8](#c8) | 18:38 | ISP-A NOC → Analyst | Fault found on ISP-A side; restored, please confirm |
| [C9](#c9) | 18:45 | Ops Lead → Analyst | Traceroute from inside: by design |
| [C10](#c10) | 18:45 | Analyst → ISP-A NOC | ISPA-INC-88213: BGP confirmed up; keep open 72 h |
| [C11](#c11) | 18:50 | ISP-A NOC → Analyst | Ticket set to monitoring 72 h; RFO to follow |

---

### C1
**Time:** 2026-11-18 17:51 · **From:** Patrick S. · **To:** Ops Leads
**Subject:** EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Investigating.

> Hello Ops Leads
>
> We have received alerts via solar winds that BGP state between EDGE-R1 to ISP-A is now in idle state, causing the ISP-B BGP to be over utilized.
>
> Currently, I am able ping 8.8.8.8 from the HQ-PC1.
>
> As the impact is moderate, I am starting my investigation into the matter.
>
> Thank you.
>
> Patrick S.

### C2
**Time:** 2026-11-18 18:25 · **From:** IPL NOC · **To:** ISP-A NOC
**Subject:** IPL - TICKET-002 - BGP IP Unreachable

> Hello ISP-A NOC
>
> As of 17:40 PST on 11/18/26 we were no longer able to reach BGP IP address 203.0.113.1 (AS 65001) on your device. Please see logs below.
>
> ```
> EDGE-R1#show log | include BGP | neighbor 203.0.113.1
> *Nov 18 23:04:53.326: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Up
> *Nov 19 01:34:42.390: %BGP-3-NOTIFICATION: sent to neighbor 203.0.113.1 4/0 (hold time expired) 0 bytes
> *Nov 19 01:34:42.392: %BGP-5-NBR_RESET: Neighbor 203.0.113.5 reset (BGP Notification sent)
> *Nov 19 01:34:42.392: %BGP-5-NBR_RESET: Neighbor 203.0.113.1 reset (BGP Notification sent)
> *Nov 19 01:34:42.440: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Down BGP Notification sent
> *Nov 19 01:34:42.440: %BGP_SESSION-5-ADJCHANGE: neighbor 203.0.113.1 IPv4 Unicast topology base removed from session  BGP Notification sent
> *Nov 19 01:34:42.440: %BGP-5-ADJCHANGE: neighbor 203.0.113.5 Down BGP Notification sent
> *Nov 19 01:34:42.440: %BGP_SESSION-5-ADJCHANGE: neighbor 203.0.113.5 IPv4 Unicast topology base removed from session  BGP Notification sent
> *Nov 19 01:34:49.619: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Up
> *Nov 19 01:38:51.788: %BGP-3-NOTIFICATION: sent to neighbor 203.0.113.1 4/0 (hold time expired) 0 bytes
> *Nov 19 01:38:51.788: %BGP-5-NBR_RESET: Neighbor 203.0.113.1 reset (BGP Notification sent)
> *Nov 19 01:38:51.788: %BGP-5-ADJCHANGE: neighbor 203.0.113.1 Down BGP Notification sent
> *Nov 19 01:38:51.788: %BGP_SESSION-5-ADJCHANGE: neighbor 203.0.113.1 IPv4 Unicast topology base removed from session  BGP Notification sent
> ```
>
> Please investigate the issue as soon as possible.
>
> Thank you.
> IPL NOC

### C3
**Time:** 2026-11-18 18:28 · **From:** ISP-A NOC (Tier 1) · **To:** IPL NOC
**Subject:** RE: IPL - TICKET-002 - BGP IP Unreachable [ISPA-INC-88213]

> Hello IPL NOC,
>
> We've opened **ISPA-INC-88213**. To locate your service we need:
> 1. **Circuit ID** and account number. We can't search by IP alone.
> 2. Please confirm the AS numbers. Your note lists 203.0.113.1 as AS 65001, but our records show 65001 is **your** AS; ours is 65100.
> 3. The state of your interface facing us, and whether you can ping 203.0.113.1.
> 4. A callback name and number.
>
> Also, your log shows a reset of a session to 203.0.113.5. That's not our network; please confirm it's unrelated.
>
> Once we have the circuit ID we'll run tests from our side. Next update within 30 minutes of receiving it.
>
> ISP-A NOC

### C4
**Time:** 2026-11-18 18:28 · **From:** Ops Lead · **To:** Patrick S.

> Patrick, I haven't seen your 18:21 update. Send me where we stand, and include the carrier's ticket number. The circuit ID is in our carrier contacts doc.

### C5
**Time:** 2026-11-18 18:30 · **From:** Patrick S. · **To:** Ops Leads
**Subject:** EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Working.

> Hello Ops Leads.
>
> Sorry for the delay, I have discovered that I am unable to reach ISP-A port E0/0 (203.0.113.1/AS 65001) from EDGE-R1. I am currently work with carrier to resolve the issue.
>
> I do need your assistance as they requesting our Circuit ID and Account number to assist us.
>
> Carrier ticket: ISPA-INC-88213

### C6
**Time:** 2026-11-18 18:32 · **From:** Ops Lead · **To:** Patrick S.

> Thanks for the update and for asking. ISP-A circuit **ISPA-ETH-0451-IPL**, account **IPL-77310**. It's in the carrier contacts doc (design/carrier-contacts.md); bookmark it, you'll need it every carrier call. One correction before you reply to them: ISP-A is **AS 65100**, we're 65001. Send them the circuit ID, our interface status, the failed ping and a callback number, and copy me. Next update to me by 19:00.

### C7
**Time:** 2026-11-18 18:36 · **From:** IPL NOC · **To:** noc@isp-a.example
**Subject:** ISPA-INC-88213

> Hello ISPA NOC
>
> Circuit ID: ISPA-ETH-0451-IPL
> Account Number: IPL-77310
>
> Please check your records again we are showing your IP is 203.0.113.1 and AS is 651000, our corresponding AS is 65001 and IP 203.0.113.2.
>
> Thank you.
> IPL NOC

### C8
**Time:** 2026-11-18 18:38 · **From:** ISP-A NOC (Tier 2) · **To:** IPL NOC
**Subject:** RE: ISPA-INC-88213 — fault found, service restored, please confirm

> Hello IPL NOC,
>
> Thanks, circuit ISPA-ETH-0451-IPL located. (For our records: we're AS 65100; the 651000 in your note looks like a typo.)
>
> **Finding:** our customer-facing interface toward you (203.0.113.1) was **administratively shut down** at approximately 17:35 PST during unrelated maintenance on our PE router. An engineer applied a change to the wrong port. Our change team is reviewing it.
>
> **Action:** interface re-enabled at **18:37 PST**. We see ARP for 203.0.113.2 again.
>
> Please confirm your BGP session to 203.0.113.1 is Established and you're receiving routes. We'll keep ISPA-INC-88213 open until you confirm, and send an RFO (reason for outage) within 2 business days.
>
> ISP-A NOC · +1-555-0100

### C9
**Time:** 2026-11-18 18:45 · **From:** Ops Lead · **To:** Patrick S.

> Good question to ask instead of chasing it. Yes, by design: FW1 doesn't show up in a traceroute and doesn't pass the outside routers' time-exceeded replies back in, so from inside you'll only ever see hop 1. To see the ISP path, run the trace from EDGE-R1 with `source 198.51.100.1`. Nice work on the verification. Confirm with ISP-A, send me the 19:00 update with your recommendation.

### C10
**Time:** 2026-11-18 18:45 · **From:** IPL NOC · **To:** noc@isp-a.example
**Subject:** ISPA-INC-88213

> Hello ISP-A NOC
>
> We have confirmed BPG is up.
>
> Please leave the ticket open for 72 hours then close.
>
> Thank you.

### C11
**Time:** 2026-11-18 18:50 · **From:** ISP-A NOC · **To:** IPL NOC
**Subject:** RE: ISPA-INC-88213 — monitoring

> Thanks for confirming. ISPA-INC-88213 is now in **Monitoring** for 72 hours and will close automatically on 2026-11-21 18:50 PST unless the circuit alarms again. The RFO for the maintenance error will be sent within 2 business days to the contact on your account. Reply to this ticket if you see any further issues.
>
> ISP-A NOC · +1-555-0100

---

[← Back to TICKET-002](TICKET-002.md) · [← Back to lab README](../README.md)
