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
> CIDR - Classless Inter-Domain Routing. /24, /26, etc. tell us how many bits belong to the network portion of an IP address.

> Private vs Public IPv4 - Private: Used inside local networks, not directly routable on the Internet. Ranges: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 Public: Globally routable Internet addresses.

> Default Gateway - The router a host sends traffic to when the destination is outside its local subnet.

> IPv6 Basics - 128-bit addressing instead of IPv4's 32-bit addresses. Uses hexadecimal, for example 2001:db8::1. No broadcast; uses multicast instead.

> Subnetting / VLSM - Subnetting: Dividing one network into smaller networks. VLSM: Using different subnet sizes within the same overall address space.

> Route Summarization - Combining multiple smaller routes into one larger route to reduce routing-table size.

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

> MAC Address - A Layer 2 hardware address used to identify a network interface on the local network.
> ARP - Address Resolution Protocol. Maps an IPv4 address to a MAC address on the local network.

> Ethernet Frame The Layer 2 data unit. Contains source MAC, destination MAC, payload, and error-checking information.

Switching Table / MAC Table -  A switch's table mapping MAC addresses to switch ports.

>Broadcast vs Unicast vs Multicast
> Broadcast: One sender to everyone on the local broadcast domain.
>Unicast: One sender to one destination.
> Multicast: One sender to a specific group of receivers.

> Collision Domain vs Broadcast Domain
> Collision domain: Area where devices could compete for the same Ethernet medium.
> Broadcast domain: Area that receives a Layer 2 broadcast.

> VLAN - Virtual LAN. Logically separates a switch into multiple Layer 2 broadcast domains.

> Access Port - A switch port normally carrying traffic for one VLAN to an end device.
> Trunk Port - A switch port carrying traffic for multiple VLANs, usually between switches, routers, or APs.

> 802.1Q Tagging - Adds a VLAN tag to an Ethernet frame so switches know which VLAN the frame belongs to.
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

> Routing Table - A router's list of known destination networks and how to reach them.

> Connected Route - A route automatically created when a router interface is configured with an IP address and is up.

> Static Route - A route manually configured by an administrator.

> Default Route - The route used when no more specific route exists. Usually written as 0.0.0.0/0.

> Next Hop - The next router or device to which a packet should be forwarded.

> Longest-Prefix Match - The router chooses the most specific matching route.
```
Example:
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
Traffic to 10.10.10.50 uses /24.
```
> Inter-VLAN Routing - Routing traffic between different VLANs because different VLANs are separate Layer 2 networks.

> Dynamic Routing - Routers automatically learn and exchange routes using routing protocols.

> OSPF Fundamentals - Open Shortest Path First. A link-state dynamic routing protocol that calculates paths using network topology and cost.

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

> TCP Handshake - TCP connection establishment:
```
SYN → 
← SYN-ACK
ACK →
TCP / UDP Ports
```
Numbers used to identify applications or services.

Examples:

TCP 22   SSH
TCP 80   HTTP
TCP 443  HTTPS
UDP 53   DNS
> Socket - An endpoint of network communication, typically represented by an IP address and port.

Example:

192.168.1.50:50000
Connection States

TCP keeps track of the state of a connection, such as LISTEN, SYN-SENT, ESTABLISHED, and TIME-WAIT.

Retransmission

TCP resends data when it determines that the data was not successfully delivered.

Sequence / Acknowledgment Numbers

TCP uses sequence numbers to track data order and acknowledgment numbers to confirm received data.

```
Common Ports
20/21   FTP
22      SSH
23      Telnet
25      SMTP
53      DNS
67/68   DHCP
80      HTTP
110     POP3
143     IMAP
443     HTTPS
3389    RDP
```

> Encapsulation / Decapsulation
> Encapsulation: Each network layer adds its own header to data before transmission.
> Decapsulation: The receiving system removes those headers as data moves up the stack.

Application data
↓
TCP/UDP
↓
IP
↓
Ethernet
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

> A Record - Maps a hostname to an IPv4 address. example.com → 93.184.216.34
> AAAA Record - Maps a hostname to an IPv6 address.

> CNAME - Alias that points one hostname to another hostname.

> MX - Specifies which mail servers handle email for a domain.

> DNS Caching - Stores previous DNS answers temporarily so future lookups can be faster.

> Recursive vs Authoritative DNS
> Recursive DNS: Finds the answer on behalf of the client.
> Authoritative DNS: Holds the official DNS records for a domain.
> Why DNS Failure Can Look Like "The Internet Is Broken"
> You may still have network connectivity, but if DNS cannot resolve names, users cannot easily turn names like google.com into IP addresses.
---
## DHCP
Know the famous:

#### DORA
- Discover
- Offer
- Request
- Acknowledge
> and understand what DHCP actually gives a host:
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

> SNAT - Source Network Address Translation. Changes the source IP of outgoing traffic.

> DNAT - Destination Network Address Translation. Changes the destination IP of incoming traffic.

> PAT - Port Address Translation. Allows many private hosts to share one public IP by using different port numbers.

> Masquerading - A form of source NAT where the router uses its own outgoing interface IP as the translated source address. Common when the public IP may change.

> Why Private RFC1918 Addresses Aren't Directly Routable on the Public Internet
> Private addresses are reserved for internal networks and are not globally routable. NAT is commonly used to translate them to public addresses for Internet access.
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

> ACLs - Access Control Lists. Rules that allow or deny traffic based on things such as IP addresses and protocols.

> Firewall Rules - Rules that control which network connections are permitted or blocked.

> Stateful vs Stateless Filtering
> Stateful: Tracks connection state and can allow return traffic automatically.
> Stateless: Evaluates packets individually without tracking the connection.

> Port Security - A switch feature that limits which MAC addresses can use a port.

> DHCP Snooping - A switch security feature that blocks unauthorized DHCP servers and builds a trusted DHCP-binding table.

> Dynamic ARP Inspection - Checks ARP traffic against trusted DHCP information to help prevent ARP spoofing.

> SSH vs Telnet - SSH: Encrypted remote administration.
> Telnet: Unencrypted remote administration. SSH should normally be used.

> Authentication / Authorization / Accounting
>Authentication: Who are you?
>Authorization: What are you allowed to do?
>Accounting: What did you do?

> Segmentation - Separating a network into different zones or networks to reduce unnecessary traffic and limit access.

> VPN Concepts - A Virtual Private Network creates an encrypted tunnel across an untrusted network, commonly the Internet.
---
## Wireless
Understand:

- SSID
- AP
- channels
- 2.4/5/6 GHz
- channel interference
- WPA2/WPA3
- roaming
- basic controller/cloud-managed AP concepts

> SSID - The name of a Wi-Fi network that users see.

> AP - Access Point. Provides wireless connectivity and bridges Wi-Fi clients to the wired network.

> Channels - Specific radio frequencies used by Wi-Fi devices to communicate.

> 2.4 / 5 / 6 GHz - Wi-Fi frequency bands.

> 2.4 GHz: Longer range, more interference, fewer channels.
> 5 GHz: Faster, more channels, shorter range.
> 6 GHz: More spectrum and less congestion, supported only by newer Wi-Fi standards/devices.

> Channel Interference - When overlapping or competing wireless transmissions reduce performance.

> WPA2 / WPA3 - Wi-Fi security standards used to authenticate clients and protect wireless traffic. WPA3 is the newer standard.

> Roaming - A wireless client moves from one AP to another while maintaining network connectivity.

> Controller / Cloud-Managed AP - A system that centrally manages multiple APs, including configuration, monitoring, firmware, and wireless policies.
---
## Troubleshooting methodology
Don't just memorize commands.
Learn to think:
> Where exactly does communication break?
```
PC
 ↓
NIC?
 ↓
Ethernet?
 ↓
Switch?
 ↓
VLAN?
 ↓
Gateway?
 ↓
Routing?
 ↓
NAT?
 ↓
DNS?
 ↓
Internet?
```
Then use tools to prove each layer:

```
Linux           Windows
ip addr         ipconfig                 
ip route        route print
ping            ping
arp             tracert
traceroute      nslookup
nslookup        arp -a
dig             netstat
ss              Test-NetConnection
tcpdump
nmap
```
## there's one final thing
brain should be able to narrate the entire fucking journey:
"Windows needs DNS first. DNS gets the destination IP. The workstation checks whether the destination is local. It isn't, so it sends the Ethernet frame to its default gateway. The switch forwards it through the correct VLAN. VyOS receives it, checks its routing table, performs NAT, forwards it toward the ISP, and the return traffic comes back through the NAT state back to the workstation."
---









