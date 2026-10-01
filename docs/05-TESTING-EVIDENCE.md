# Testing Evidence

This document records the connectivity and security tests performed on the 
completed Packet Tracer implementation, confirming the design decisions made 
in Milestone 1 actually function as intended.

---

## 1. VLAN Configuration Verification

The switch VLAN table confirms all seven VLANs are active with the correct 
ports assigned, matching the design in the IP Addressing Plan.

![VLAN Brief](../evidence/switch-vlan-brief.png)

| VLAN | Name | Ports |
|---|---|---|
| 10 | Admin | Fa0/6 |
| 20 | Finance | Fa0/8 |
| 30 | Visitor | Fa0/10 |
| 40 | IT | Fa0/5 |
| 50 | Security | Fa0/7 |
| 60 | Servers | Fa0/2, Fa0/3, Fa0/4 |
| 70 | Guest | Fa0/9, Fa0/11 |

---

## 2. Inter-VLAN Routing

A device on the Admin VLAN (172.30.36.10) successfully reaches a device on 
the Finance VLAN (172.30.36.74), confirming the router is correctly routing 
traffic between VLAN sub-interfaces (router-on-a-stick configuration).

![Admin to Finance Ping](../evidence/admin-ping-finance.png)

Result: 4/4 packets received, 0% loss.

---

## 3. DNS Resolution (Assigned Networking Challenge)

The internal DNS server (172.30.37.130) was configured with three A records 
under the `taung.local` domain. A staff PC (Admin VLAN) successfully resolved 
all three hostnames using `nslookup`:

![DNS Resolution Test](../evidence/admin-nslookup-records.png)

| Hostname | Resolved Address |
|---|---|
| ticketing-app.taung.local | 172.30.37.132 |
| file-server.taung.local | 172.30.37.131 |
| dns-server.taung.local | 172.30.37.130 |

This confirms the DNS service is correctly configured and reachable by 
authorised staff VLANs, satisfying the assigned networking challenge.

---

## 4. Guest WiFi Isolation (Access Control)

The client's security requirement states that Guest WiFi must not be able to 
reach any internal staff system or server. An extended ACL was applied inbound 
on the Guest VLAN's router sub-interface (GigabitEthernet0/0.70), denying all 
traffic from the Guest subnet (172.30.37.192/26) toward the rest of the 
172.30.36.0/23 address block.

**Before the ACL was applied**, a ping from Guest WiFi to the DNS server 
succeeded (4/4 packets, 0% loss) — confirming the network was functional but 
not yet secured.

**After the ACL was applied**, the same ping was blocked:

![Guest Ping Blocked](../evidence/guest-ping-blocked.png)

Result: 4/4 packets lost, "Destination host unreachable" returned by the 
Guest gateway (172.30.37.193) — confirming the router itself is rejecting the 
traffic at the point of entry, rather than the request simply failing to find 
a route.

The router's access-list counters confirm the rule is actively matching 
blocked traffic, not merely present in the configuration:

![ACL Match Counters](../evidence/router-acl-matches.png)
---

## 5. IT Support Full Access

The client's requirement that IT Support retain full troubleshooting access 
to every VLAN was verified by testing from a PC on the IT VLAN against both a 
staff VLAN and the Servers VLAN:

![IT Full Access Test](../evidence/it-full-access.png)

| Destination | Result |
|---|---|
| Finance VLAN (172.30.36.74) | 4/4 success |
| Servers VLAN — DNS server (172.30.37.130) | 4/4 success |

No ACL is applied to the IT VLAN's sub-interface, so it retains unrestricted 
access by design, confirming this requirement is met without needing an 
explicit permit rule.

---

## Summary

| Requirement | Test | Result |
|---|---|---|
| Inter-VLAN connectivity | Admin → Finance ping | Pass |
| DNS challenge | nslookup × 3 records | Pass |
| Guest isolation | Guest → Servers ping (after ACL) | Pass (blocked) |
| IT full access | IT → Finance, IT → Servers ping |  Pass |
| VLAN configuration | show vlan brief |  Pass |

All core requirements from the Milestone 1 design have been implemented and 
verified in the working Packet Tracer file.
