Secure Small Office Network (V1)

A small office network designed, built, secured and then attacked in Cisco Packet Tracer, as part of my move into network engineering and my study towards the CCNA.

Topology

The brief

Staff, a finance team and visitors share one building. The network enforces five rules:

-Guests on the guest WIFI get the internet and nothing else
-Finance can reach the server and the internet, but nobody else can reach Finance
-Staff can reach the server, the printer and the internet
-Network devices can only be managed over SSH from the Management VLAN
-A stranger plugging into a spare port, or bringing their own DHCP server, gets nowhere

Every part of the configuration exists to enforce one of those rules.

Somethings I learned/had to troubleshoot

Native VLAN mismatch while building trunks. After configuring SW1's side of the trunk to SW2, CDP reported a native VLAN mismatch and spanning tree blocked the port for VLAN 999. This was expected until SW2 was configured to match, and it showed why mismatches matter: they can leak traffic between VLANs.

SW2 unreachable for management. Pings to 192.168.99.12 failed. show vlan brief showed I'd never created the VLANs on SW2, because VLANs are local to each switch. VLANs 10, 20 and 30 existed only because IOS creates them automatically when a port is assigned. After creating them, show ip interface brief revealed a second fault: no Vlan99 interface at all. Two faults, one symptom.

No internet access. show ip nat translations was empty. R1's outside interface had 203.0.114.2 instead of 203.0.113.2, so the default route pointed at an unreachable network. One digit. Lesson: check interface addresses first.

Server, management and trunk all went down at once. I pasted SW2's unused port command onto SW1. The port range happened to include the server port, the management port and the trunk to SW2. show interfaces status showed them disabled in VLAN 998. Fixing it meant restoring each port's VLAN and rebuilding the trunk, not just no shutdown, because the range command had converted the trunk into an access port. Lesson: always check the hostname in the prompt before pasting.

Roadmap

This is the first in a series where the same network grows over time:

Version 1: secure small office (this repo)
Version 2: add a stateful firewall at the edge
Version 3: a second branch office linked by site to site VPN and OSPF
Motorsport network: a race team network linking the pit wall back to the factory