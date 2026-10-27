



# Two-Site Enterprise Network (Cisco Packet Tracer)

A two-site company network built in Cisco Packet Tracer. The headquarters (HQ) uses VLANs on a single router interface (router-on-a-stick), the branch office connects over OSPF, and both sites reach a simulated ISP through PAT. A Guest VLAN is restricted with an extended ACL, and the routers are managed over SSH.


## Features

- **VLAN segmentation** at HQ: Staff (10), Guest (20), Servers (30), with trunk links between switches
- **Inter-VLAN routing** using router-on-a-stick (`Gig0/0/0.10`, `.20`, `.30`)
- **OSPF (area 0)** between HQ (Router1) and the branch (Router2), with a default route advertised to the branch
- **DHCP** server with one pool per network, reached from other networks through `ip helper-address` (DHCP relay)
- **DNS** server (`www.company.local`) and a web server
- **PAT (NAT overload)** to a simulated ISP over a serial link
- **Extended ACL** (`GUEST_IN`) that limits the Guest VLAN to DHCP, DNS, the web server and the internet
- **SSH** (version 2) on the routers, Telnet disabled

## Addressing

| Network | Subnet | Gateway |
|---|---|---|
| VLAN 10 Staff | 192.168.10.0/24 | 192.168.10.1 |
| VLAN 20 Guest | 192.168.20.0/24 | 192.168.20.1 |
| VLAN 30 Servers | 192.168.30.0/24 | 192.168.30.1 |
| Branch | 192.168.40.0/24 | 192.168.40.1 |
| Router1 - Router2 link | 10.0.0.0/30 | 10.0.0.1 / 10.0.0.2 |
| Router1 - ISP serial link | 203.0.113.0/30 | 203.0.113.2 / 203.0.113.1 |

| Server | Address |
|---|---|
| DHCP server | 192.168.30.10 |
| DNS server | 192.168.30.11 |
| Web server | 192.168.30.20 |
| ISP test address (loopback) | 8.8.8.8 |

## Devices

| Device | Role |
|---|---|
| Router1 (PT8200) | HQ gateway, DHCP relay, OSPF, PAT, ACL, SSH |
| Router2 (PT8200) | Branch gateway, DHCP relay, OSPF, SSH |
| Router3 / ISP (2911) | Simulated internet |
| Switch1 | HQ core switch, trunks to the router and the access switches |
| Switch2-4 | HQ access switches for VLAN 10, 20 and 30 |
| Switch5 | Branch switch |

## How to run

1. Open the `.pkt` file in Cisco Packet Tracer.
2. On the PCs set the IP configuration to **DHCP** (switch to Static and back to DHCP to refresh).

## How to test

| Test | Expected result |
|---|---|
| PC0 (Staff): `ping 8.8.8.8` | Replies |
| PC4 (Branch): `ping 8.8.8.8` | Replies |
| Any PC: `nslookup www.company.local` | Returns 192.168.30.20 |
| Any PC: browser to `http://www.company.local` | Web page opens |
| PC2 (Guest): `ping 192.168.10.11` | Blocked by the ACL |
| PC2 (Guest): `ping 192.168.40.11` | Blocked by the ACL |
| PC2 (Guest): web page and DNS | Allowed |
| Router1: `show ip nat translations` | Private addresses translated to 203.0.113.2 |
| Router1: `show access-lists` | Match counters increase |
| PC0: `ssh -l admin 192.168.10.1` | SSH login works |
| PC2 (Guest): `ssh -l admin 192.168.20.1` | Blocked |

## Skills shown

VLANs and trunking, inter-VLAN routing, OSPF, DHCP relay, DNS, NAT/PAT, extended ACLs, SSH, serial links, troubleshooting with `show` commands.

## Notes

- Lab credentials: user `admin`, password `123`. These are for the lab only.
- Add the screenshots (topology, DHCP, DNS and web, NAT translations, ACL counters, blocked ping, SSH login) to the `screenshots/` folder.

