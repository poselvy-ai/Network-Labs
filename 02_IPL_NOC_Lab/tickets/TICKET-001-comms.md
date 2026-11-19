[← Back to TICKET-001](TICKET-001.md) · [← Back to lab README](../README.md)

# TICKET-001 — Communications log

Every message linked to TICKET-001, in full and in order, as a ticketing system would attach it to the incident's activity stream. Times are PST, as sent. Analyst messages are verbatim.

| # | Time | From → To | Subject / type |
|---|------|-----------|----------------|
| [C1](#c1) | 15:29 | Analyst → Substation 1 | Initial notification |
| [C2](#c2) | 15:29 | Analyst → Ops Lead | Initial notification |
| [C3](#c3) | 15:29 | Ops Lead → Analyst | Severity raised to SEV-2, IC assigned |
| [C4](#c4) | 15:44 | Analyst → Substation 1 | TICKET-001: SCADA workstation connectivity, investigating |
| [C5](#c5) | 15:44 | Substation 1 → Analyst | Reply: telemetry restored |
| [C6](#c6) | 15:45 | Ops Lead → Analyst | Query: logs and change check |
| [C7](#c7) | 15:45 | Analyst → Ops Lead | Reply: restore first |
| [C8](#c8) | 15:50 | Analyst → Ops Lead | TICKET-001 opened: SEV-2 (P2), Substation 1 SCADA workstation down |
| [C9](#c9) | 15:50 | Ops Lead → Analyst | Status set: Resolved, monitoring; RCA required |
| [C10](#c10) | 16:43 | Analyst → Ops Lead | TICKET-001: SCADA workstation connectivity, restoration and closure |
| [C11](#c11) | 16:49 | Ops Lead → Analyst | RCA review; escalated to Security |

---

### C1
**Time:** 2026-11-18 15:29 · **From:** Patrick S., NOC Analyst 1 · **To:** Substation 1 Operator · **Type:** Initial notification

> Hello Substation,
>
> We acknowledge that your have lost telemetry to your work station and we are currently investigation the issue.
>
> We get back to shortly with an update.
>
> Thank you.
>
> Patrick S.
> NOC Analysis 1.

### C2
**Time:** 2026-11-18 15:29 · **From:** Patrick S. · **To:** Ops Leads · **Type:** Initial notification

> Hello Ops Leads,
>
> Substation 1 is not able to see telemetry at their works station. I have open of a P3 Investigation TICKET-001 to start my investigation and work with the appropriate teams.
>
> I will follow up shortly with an update.
>
> Thank you.
>
> Patrick S.

### C3
**Time:** 2026-11-18 15:29 · **From:** Ops Lead · **To:** Patrick S. · **Type:** Reply

> Thanks, Patrick. Good, fast acknowledgment to the site. I'm raising this to SEV-2 (P2) and taking Incident Commander. At a utility, a SCADA outage is never routine. Find out whether the control center also lost this substation's telemetry. If they did, we're SEV-1 and I'm paging leadership. Updates every 15 minutes. Start troubleshooting now.

### C4
**Time:** 2026-11-18 15:44 · **From:** Patrick S. · **To:** Substation 1 Operator
**Subject:** TICKET-001: SCADA workstation connectivity, investigating

> We have found that a port was shut down preventing the SCADA-PC1 not to be reached on the network. The NOC has turned up the port restoring the communication path.
>
> Please verify you are now seeing telemetry on SCADA-PC1.
>
> Thank you.

### C5
**Time:** 2026-11-18 15:44 · **From:** Substation 1 Operator · **To:** Patrick S. · **Type:** Reply

> Yes, telemetry is back. We see all feeders as of 15:44. Thanks.

### C6
**Time:** 2026-11-18 15:45 · **From:** Ops Lead · **To:** Patrick S. · **Type:** Query

> Patrick, did you check the logs and the change calendar before bringing that port back up? An administratively down port means someone typed `shutdown`. If that was an engineer working in the substation, we may have just re-energized a link in the middle of their work. If nobody approved it, then someone made an unauthorized change on our SCADA network, and that's a potential security incident. I also haven't had your 15:45 update.

### C7
**Time:** 2026-11-18 15:45 · **From:** Patrick S. · **To:** Ops Lead · **Type:** Reply

> I am not their yet. As we are at Sev-2 the priority is to restore service first.

*IC note: accepted. ITIL restore-first. For manual changes on OT networks, a 30-second log/change check before reversing is good practice.*

### C8
**Time:** 2026-11-18 15:50 · **From:** Patrick S. · **To:** Ops Lead
**Subject:** TICKET-001 opened: SEV-2 (P2), Substation 1 SCADA workstation down

> I have found that on SUB-R1 port E0/1 (10.2.10.1/24) was administratively downed, and have amined up the port. I have confirmed with the customer the telemetry has been restored to SCADA-PC1.
>
> I am deescalating the issue to SEV-4, while I prep the RCA and finishing gathering logs.
>
> Thank you.
>
> Patrick S. NOC Analysis.

### C9
**Time:** 2026-11-18 15:50 · **From:** Ops Lead · **To:** Patrick S. · **Type:** Reply

> Good work getting the substation back fast. Confirmed restored. One correction: as Incident Commander, I'll make the severity call. I'm marking the incident Resolved, monitoring. The severity of record stays SEV-2, which is why the RCA is required. Please get me the logs, and open the escalation to Security on who shut that port. RCA draft by tomorrow 15:00.

### C10
**Time:** 2026-11-18 16:43 · **From:** Patrick S. · **To:** Ops Lead
**Subject:** TICKET-001: SCADA workstation connectivity, restoration and closure.
**Attachment:** [TICKET-001-RCA-analyst-draft.txt](./TICKET-001-RCA-analyst-draft.txt)

> After restoration of service, I was able to find the SUB-R1 port E0/1 was admined down on "*Nov 18 23:19:50.389: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down". Unfortunately, I am unable to proceed further as the console does not need "User Authentication" to log in. Also, the "*" indicates that the time is set to the device time not NTP time. I am not sure if these are by designed or not and will leave escalation in your hands.
>
> I have filled out the initial part of the RCA, for you to go over and fill in the rest.
>
> Thank you.
>
> Patrick S.

### C11
**Time:** 2026-11-18 16:49 · **From:** Ops Lead · **To:** Patrick S. · **Type:** Reply
**Attachment:** [TICKET-001-RCA.md](./TICKET-001-RCA.md)

> Thanks, Patrick. Solid draft, and you got it in the same day. You're right to hand me the NTP and authentication questions instead of acting on them. I've escalated to Security to review who had console access at 15:19:49 PST. I've completed the RCA. It stays open with a preliminary root cause until Security reports back (review on 11/20). Read my changes so you know what I corrected.

---

[← Back to TICKET-001](TICKET-001.md) · [← Back to lab README](../README.md)
