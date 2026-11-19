[← Back to TICKET-002](TICKET-002.md) · [← Back to lab README](../README.md)

# TICKET-002 — Communications log

Every message linked to TICKET-002, in full and in order. Times are PST, as sent. Analyst messages are verbatim.

| # | Time | From → To | Subject / type |
|---|------|-----------|----------------|
| [C1](#c1) | 17:51 | Analyst → Ops Lead | EDGE-R1 BGP idle to ISP-A, over Utilized ISP-B - Investigating |
| [C2](#c2) | 18:25 | Analyst → ISP-A NOC | IPL - TICKET-002 - BGP IP Unreachable |
| [C3](#c3) | 18:31 | ISP-A NOC → Analyst | Re: carrier ticket ISPA-INC-88213 opened, information requested |
| [C4](#c4) | 18:31 | Ops Lead → Analyst | Update overdue |

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
**Time:** 2026-11-18 18:31 · **From:** ISP-A NOC (Tier 1) · **To:** IPL NOC
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
**Time:** 2026-11-18 18:31 · **From:** Ops Lead · **To:** Patrick S.

> Patrick, I haven't seen your 18:21 update. Send me where we stand, and include the carrier's ticket number. The circuit ID is in our carrier contacts doc.

---

[← Back to TICKET-002](TICKET-002.md) · [← Back to lab README](../README.md)
