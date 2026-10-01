# Silver State Business Solutions

Silver State Business Solutions (SSBS) is a growing professional-services company opening a new headquarters and connecting it to an existing small branch office.
The company currently has approximately 85 employees at headquarters and 20 employees at the branch. Management expects moderate growth over the next three years and does not want the network redesigned simply because another 20–30 employees are hired.
Your assignment is to design the initial enterprise network that will eventually become the production baseline for our NOC troubleshooting exercises.

## Headquarters requirements

HQ contains four business groups:
Department / Function	Current devices
Corporate / Administration	20
Engineering / Operations	30
Sales / Customer Service	25
IT / Network Management	10


These groups must be logically separated at Layer 2.

In addition, HQ requires a separate network for servers and infrastructure services. Initially this will contain approximately 10 devices, but capacity should exist for expansion.

Users in different departments must be able to communicate where permitted. Therefore, your design must provide inter-VLAN routing.

## Infrastructure services
HQ will host internal infrastructure services.

At minimum, the environment needs:

- DHCP — User workstations should receive their IPv4 configuration dynamically. Network infrastructure should generally use predictable addressing.
- DNS — An internal DNS server must provide name resolution for company resources.
- Application/Web Server — Deploy an internal server that users can reach by hostname. This gives us something useful to test at Layers 3–7.

## Internet connectivity
HQ has one ISP connection.

The ISP has provided the company with this WAN allocation:

**203.0.113.8/30**
ISP controls the first usable address.

Internal company addressing must use RFC1918 private IPv4 space.

Users must be able to access simulated Internet resources through the company Internet connection.


## Switching requirements
HQ requires at least two access switches.
- Employees connected to different switches may belong to the same department.
- Design therefore needs to support VLAN traffic between switches.
- Management is also concerned about accidental switching loops and wants the network capable of supporting redundant Layer 2 connections in the future.


## Branch office
The branch contains approximately 20 employees.
- For Lab 1, you only need to reserve addressing space and account for the branch in your architecture. 
- The branch must use a different IP subnet from HQ networks.

## Design constraints
This is a small-to-medium enterprise. Don't build a Fortune 500 network for an 85-person headquarters.
- Don't build a flat home network.
- Solution should be understandable by another network engineer who inherits it at 2:00 AM during an outage.
- Use meaningful hostnames and maintain consistent addressing conventions.
- You may use Cisco Packet Tracer devices of your choice, but if Packet Tracer doesn't support a feature exactly as real Cisco hardware would, document the limitation rather than designing around imaginary functionality.

## Success criteria
When the baseline is eventually complete, I should be able to connect a new workstation to the appropriate access port and have it:
PC → switch → VLAN → DHCP → default gateway → inter-VLAN routing → DNS → internal server → Internet
Every one of those steps will later become a potential incident.
