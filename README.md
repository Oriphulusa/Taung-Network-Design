# Taung Heritage Site Visitor Centre — Network Design

A segmented, secure network design for a tourism visitor centre — built around 
VLAN-based departmental isolation, scalable subnetting, internal DNS, and 
stateful access control policy.

![Logical Topology](diagrams/logical-topology.png)

---

## The Problem

Taung Skull Heritage Site Visitor Centre needed a single network that could 
serve both internal staff systems and public visitor WiFi, without exposing 
sensitive data (finance, admin, security systems) to visitor devices. The 
network also had to absorb a seasonal doubling of staff numbers for three 
months a year without requiring a redesign, and needed a working internal DNS 
service so staff could reach systems by hostname rather than raw IP address.

---

## What I Built

- **7 VLANs** separating departments (Admin, Finance, Visitor Services, IT, 
  Security, Servers, Guest WiFi), each on its own subnet carved from a 
  172.30.36.0/23 address block
- **Generous subnet sizing** per department so the seasonal staff surge fits 
  without re-addressing
- **Central server VLAN** hosting DNS, file, and ticketing application servers, 
  reachable only by authorised staff VLANs
- **Stateful access control** — servers respond only to requests staff devices 
  initiate; they never contact staff devices unsolicited
- **Fully isolated guest WiFi** — visitors get internet access only, with zero 
  reachability into staff or server networks
- **IT support VLAN** with full inter-VLAN reach for troubleshooting, while 
  every other department is restricted to what it needs

![Physical Topology](diagrams/physical-topology.png)

The physical design centres the server room in the building to minimise cable 
runs, with the one exception being the outdoor WiFi access point (~70m run — 
within Cat6 tolerance, with a PoE-switch upgrade path identified for later).

---

## Skills Demonstrated

- Network segmentation and VLAN design
- Subnetting and CIDR-based IP address planning
- Access control policy design (stateful, least-privilege)
- Internal DNS architecture
- Structured technical documentation and diagramming

---

## Status

Design phase complete. Next: Cisco Packet Tracer implementation, DNS 
configuration and testing, ACL enforcement, and full connectivity testing.

---

*Academic context: CMPG 325 — Computer Networks, North-West University. 
Oree Mavhunga, 46030395.*
