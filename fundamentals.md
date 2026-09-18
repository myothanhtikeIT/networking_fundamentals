# The Networking Fundamentals that need to Truly Own

## IP addressing and subnetting
when look at IP address like ```192.168.10.37/26```,  should be  immediately know:
```
Network:   192.168.10.0
Usable:    .1 - .62
Broadcast: .63
```
> And understand why, not just calculate it.
also understand:
- CIDR
- private vs public IPv4
- default gateway
- IPv6 basics
- subnetting/VLSM
- route summarization
---
## Ethernet and switching
> need to really understand: PC → Switch → Router → Internet
specifically:

- MAC addresses
- ARP
- Ethernet frames
- switching tables/MAC tables
- broadcast vs unicast vs multicast
- collision domain vs broadcast domain
- VLANs
- access ports
- trunk ports
- 802.1Q tagging
---
## Routing
You should understand:

"I have this destination IP. How does the router decide where to send it?"

That means:

- routing table
- connected routes
- static routes
- default route
- next hop
- longest-prefix match
- inter-VLAN routing
- dynamic routing concept
- OSPF fundamentals

For example:
```
10.20.10.0/24
       ↓
      VyOS
    ↙       ↘
VLAN 10    VLAN 20
```
You should be able to explain exactly what happens when 10.20.10.50 talks to 10.20.20.60.
---
## TCP/IP properly

Application
    ↓
TCP / UDP
    ↓
IP
    ↓
Ethernet / Wi-Fi

And know:

IP

Where is it going?

TCP

How do I reliably deliver this stream?

UDP

How do I deliver this with minimal transport overhead?

Port

Which service/application is this for?

Then learn:

- TCP handshake
- TCP/UDP ports
- sockets
- connection states
- retransmission
- sequence/acknowledgment numbers
- common ports
- encapsulation/decapsulation
---
## DNS
> make the process automatic:
chatgpt.com
        ↓
DNS lookup
        ↓
IP address
        ↓
TCP or QUIC/UDP connection
        ↓
HTTPS
        ↓
Internet

Understand:
- A records
- AAAA records
- CNAME
- MX
- DNS caching
- recursive vs authoritative DNS
- why DNS failure can look like "the Internet is broken"
---
## DHCP
Know the famous:

#### DORA

- Discover
- Offer
- Request
- Acknowledge
And understand what DHCP actually gives a host:
- IP address
- Subnet mask
- Default gateway
- DNS server
- Lease information

---
## NAT - network address translation
make sure this is solid:
**Private IP**
192.168.10.50
        ↓ NAT
Public IP
203.x.x.x

Understand:

- SNAT
- DNAT
- PAT
- masquerading
- why private RFC1918 addresses aren't directly routable across the public Internet

---
## Network security fundamentals
> you should understand:
- ACLs
- firewall rules
- stateful vs stateless filtering
- port security
- DHCP snooping
- Dynamic ARP Inspection
- SSH vs Telnet
- authentication/authorization/accounting
- segmentation
- VPN concepts
And importantly:
> A VLAN is not automatically a security boundary by itself.