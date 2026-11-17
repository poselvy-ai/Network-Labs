# Design Decisions — IPL NOC Lab

| Decision | Why |
|----------|-----|
| Multi-area OSPF (areas 0, 1, 2) | Gives ABRs, inter-area (O IA) and external (O*E2) routes to read during troubleshooting, the way a real multi-site network looks. |
| Reno dual-homed, substation single-homed | Reno shows OSPF failover and equal-cost paths; the substation shows what a single point of failure looks like. |
| ASAv as the firewall | CML has no Palo Alto image. The ASA covers the same Tier 1 questions: is traffic reaching the firewall, which zone, is it NATed, is a rule dropping it. `packet-tracer` plays the role of Palo Alto's `test security-policy-match`. |
| Firewall injects the default route into OSPF | Internal routers learn the way out dynamically; losing the default route is a realistic fault. |
| Two ISPs with eBGP, ISP-A preferred both ways | Local-pref (outbound) and AS-path prepend (inbound) give a path decision to verify and a failover to test. |
| EDGE-R1 advertises only 198.51.100.0/24 (prefix-list out) | Stops the edge from becoming transit between the two ISPs, a common real-world mistake. |
| IOL nodes as test hosts | Very low RAM per node; `ping`, `traceroute` and `telnet <ip> 80` are enough for Tier 1 testing. |
| Ubuntu for the DMZ web server (replaced IOL, C-20261116002) | The IOL HTTP service refused connections (TICKET-002) and doesn't behave like a real server. Ubuntu gives a real systemd service, host firewall and Linux admin workflow. Python's built-in web server is used because the lab has no internet to install nginx. |
| Fictional client name and documentation IP ranges | The lab is public; nothing in it maps to a real organization. |
