# Routing Tables Fundamentals

## What Is a Routing Table?

A **routing table** is a set of instructions that tells a device:

> **"If I need to send an IP packet to this destination, where should I send it?"**

The device checks the packet's destination IP address against its routing table, chooses the best matching route, and forwards the packet accordingly.

Routing tables can exist on:

* PCs
* Servers
* Virtual machines
* Routers
* Layer 3 switches
* Firewalls
* Linux/BSD network appliances

A routing table is therefore **not exclusively a router feature**.

---

# 1. Simple Example

Suppose a Windows 11 machine has:

```text
IP address:     10.10.10.50
Subnet mask:    255.255.255.0
```

It wants to communicate with:

```text
10.10.10.80
```

The destination belongs to:

```text
10.10.10.0/24
```

which is the client's local network.

Therefore, the client can communicate directly through its network interface.

Now suppose it wants to communicate with:

```text
8.8.8.8
```

That destination is not part of:

```text
10.10.10.0/24
```

The client therefore needs to determine:

> "Where should I send this packet?"

It checks its routing table.

---

# 2. Example Routing Table

A simplified routing table might look like:

```text
Destination        Gateway          Interface

10.10.10.0/24      On-link          eth0
0.0.0.0/0          10.10.10.1       eth0
```

This means:

```text
10.10.10.0/24
    -> Directly reachable through eth0

Everything else
    -> Send to 10.10.10.1
```

The second entry is the **default route**.

---

# 3. Default Route

The default route is commonly represented as:

```text
0.0.0.0/0
```

It means:

> **"If there is no more specific route for this destination, use this route."**

For a normal client:

```text
Client
10.10.10.50
     |
     | Default route
     v
10.10.10.1
     |
     v
Router
     |
     v
Internet
```

The address:

```text
10.10.10.1
```

is typically the client's **default gateway**.

---

# 4. Routing Table vs Default Gateway

These are related but different.

## Routing Table

The routing table contains the routes known by the device.

Example:

```text
10.10.10.0/24 -> Directly connected
10.20.20.0/24 -> 10.10.10.254
0.0.0.0/0     -> 10.10.10.1
```

## Default Gateway

The default gateway is the next-hop device used when no more specific route exists.

Example:

```text
Default gateway:
10.10.10.1
```

Therefore:

> **The default gateway is one part of the routing configuration. The routing table contains the complete set of routes.**

---

# 5. Your Computer Has a Routing Table

A common misconception is:

> "Only routers have routing tables."

This is incorrect.

Your:

* Debian host
* Windows 11 VM
* Windows Server 2022 VM
* Router
* Firewall

can all have routing tables.

For example, Linux:

```bash
ip route
```

Possible output:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link
```

Meaning:

```text
192.168.1.0/24
    -> Directly connected

Everything else
    -> 192.168.1.1
```

Windows:

```powershell
route print
```

or:

```powershell
Get-NetRoute
```

---

# 6. How a Routing Decision Works

Suppose a computer has:

```text
IP:
10.10.10.50/24
```

and wants to communicate with:

```text
10.20.20.50
```

Its routing table might contain:

```text
10.10.10.0/24 -> Direct
10.20.20.0/24 -> 10.10.10.254
0.0.0.0/0     -> 10.10.10.1
```

The destination is:

```text
10.20.20.50
```

The device finds:

```text
10.20.20.0/24
```

and therefore sends the packet to:

```text
10.10.10.254
```

It does **not** use the default route because a more specific route exists.

---

# 7. Longest Prefix Match

When multiple routes match a destination, the device generally chooses the **most specific route**.

Example:

```text
10.0.0.0/8       -> Router A
10.10.0.0/16     -> Router B
10.10.20.0/24    -> Router C
```

Destination:

```text
10.10.20.50
```

All three routes could match.

The most specific route is:

```text
10.10.20.0/24
```

Therefore, Router C is selected.

General principle:

```text
More specific route
        >
Less specific route
        >
Default route
```

This is called **longest prefix match**.

---

# 8. Where Do Routes Come From?

Routes can come from several sources.

## Directly Connected Routes

If a device has an interface configured as:

```text
10.10.10.1/24
```

it automatically knows:

```text
10.10.10.0/24
```

is directly connected.

```text
Interface
10.10.10.1/24
     |
     v
10.10.10.0/24
     |
     v
Directly connected
```

---

## Static Routes

An administrator can manually configure a route.

Example:

```text
Destination:
10.20.20.0/24

Next hop:
10.10.10.254
```

Meaning:

> To reach 10.20.20.0/24, send traffic to 10.10.10.254.

Static routes are useful for:

* Small networks
* Simple architectures
* Specific network paths
* Special-purpose routes
* Default routes
* Troubleshooting

---

## Dynamic Routing

Large networks cannot realistically rely on administrators manually configuring every route.

Routers can learn routes dynamically using routing protocols such as:

* OSPF
* BGP
* IS-IS
* EIGRP

Conceptually:

```text
Router A
   |
   | Dynamic routing protocol
   |
Router B
```

Router B can tell Router A:

> "I know how to reach 10.50.0.0/16."

Router A can then learn and use that route.

Dynamic routing becomes increasingly important as networks grow.

---

# 9. Is There a Routing Server?

Not in the same sense as a DHCP server.

DHCP is normally provided by a **DHCP service**:

```text
DHCP Server
    |
    +-- DHCP service
```

Routing is normally a **function performed by a device or operating system**.

Examples:

```text
Router
    -> Routing

Layer 3 Switch
    -> Routing

Firewall
    -> Routing

Linux Server
    -> IP forwarding + routing

Windows Server
    -> Can perform routing functions
```

There is no universal "Routing Server" that every network uses.

Instead, the network architecture determines which device performs routing.

---

# 10. Routing Devices

A typical business network might look like:

```text
                    Internet
                       |
                       v
                Firewall / Router
                       |
                     Switch
                  /         \
                 /           \
            VLAN 10         VLAN 20
           Employees         Servers
          10.10.10.0/24    10.10.20.0/24
```

The firewall or Layer 3 switch may have routes such as:

```text
10.10.10.0/24 -> VLAN 10
10.10.20.0/24 -> VLAN 20
0.0.0.0/0     -> Internet
```

It uses those routes to forward traffic.

---

# 11. Routing Between VLANs

Routing becomes especially important when a network is divided into VLANs.

Example:

```text
VLAN 10
Employees
10.10.10.0/24

VLAN 20
Servers
10.10.20.0/24
```

An employee PC:

```text
10.10.10.50
```

wants to access:

```text
10.10.20.10
```

The destination is on another subnet.

The traffic therefore goes through a Layer 3 device:

```text
Employee PC
10.10.10.50
      |
      v
Default Gateway
10.10.10.1
      |
      v
Router / L3 Switch
      |
      v
10.10.20.10
Server
```

The routing table tells the Layer 3 device how to reach:

```text
10.10.20.0/24
```

---

# 12. DHCP and Routing Work Together

DHCP can tell a client:

```text
IP address:
10.10.10.50

Subnet mask:
255.255.255.0

Default gateway:
10.10.10.1

DNS server:
10.10.10.10
```

The client now knows:

```text
My network:
10.10.10.0/24

My gateway:
10.10.10.1

My DNS:
10.10.10.10
```

When it wants to reach:

```text
10.20.20.50
```

the client checks its routing table.

Because the destination is outside its local subnet, it sends the packet toward its default gateway:

```text
10.10.10.1
```

The router then checks **its own routing table** and forwards the packet toward the destination.

Conceptually:

```text
DHCP
  |
  | Provides network configuration
  v
Client
  |
  | Checks routing table
  v
Default Gateway
  |
  | Checks its routing table
  v
Destination
```

---

# 13. DHCP vs Routing

These are different jobs.

## DHCP

Answers:

> **"What network configuration should this client use?"**

DHCP can provide:

```text
IP address
Subnet mask
Default gateway
DNS server
DNS suffix
Other options
```

## Routing

Answers:

> **"Where should this packet go?"**

Example:

```text
Client
  |
  | Destination: 10.20.20.50
  v
Routing table
  |
  | Best route:
  | 10.20.20.0/24
  v
Next hop
```

Simple mental model:

```text
DHCP
    |
    +-- Teaches the client about its network

Routing
    |
    +-- Determines where traffic should be forwarded
```

---

# 14. Routing Troubleshooting

Suppose:

```text
Client:
10.10.10.50

Server:
10.20.20.50
```

The user says:

> "I cannot reach the server."

First inspect the client's network configuration:

```powershell
ipconfig
```

Then inspect its routing table:

```powershell
route print
```

or:

```powershell
Get-NetRoute
```

You might discover that there is no route toward:

```text
10.20.20.0/24
```

That gives you a strong clue:

> The client does not know how to reach that network.

Alternatively, the client might have:

```text
10.20.20.0/24 -> 10.10.10.254
```

but the router at:

```text
10.10.10.254
```

might not have a route toward the destination.

The investigation then moves to the router or Layer 3 device.

---

# 15. Routing Failure vs DNS Failure

Suppose:

```text
server.company.local
```

does not work.

First determine the server's IP:

```text
server.company.local
        |
        v
10.20.20.50
```

Then test the IP directly.

If:

```text
ping 10.20.20.50
```

fails, investigate:

* Routing
* Firewall
* VLAN
* Network connectivity
* ACL
* Server availability

If:

```text
ping 10.20.20.50
```

works but:

```text
ping server.company.local
```

fails, investigate:

* DNS
* DNS configuration
* DNS records
* DNS server connectivity

This distinction is extremely useful in practical troubleshooting.

---

# 16. Routing and OSI Layers

Routing primarily operates at **Layer 3, the Network layer**, in the OSI model.

You do not need to memorize the OSI model just to understand routing.

The useful troubleshooting model is:

```text
Physical connectivity
        |
        v
Local network connectivity
        |
        v
IP connectivity and routing
        |
        v
TCP/UDP connectivity
        |
        v
Application
```

The purpose of layers is not to make you memorize seven additional things.

The purpose is to help answer:

> **"At what point does communication stop working?"**

---

# 17. Practical System Engineer Troubleshooting Model

A System Engineer can combine **service-oriented thinking** with **layer-oriented thinking**.

Start with the practical question:

```text
"What is broken?"
```

Then isolate it:

```text
Does the machine have a valid IP?
        |
        +-- No
        |    -> DHCP / network configuration
        |
        v
Can it communicate with the local network?
        |
        +-- No
        |    -> NIC / Ethernet / Wi-Fi / VLAN / ARP / switch
        |
        v
Can it reach the gateway?
        |
        +-- No
        |    -> Local network / VLAN / gateway
        |
        v
Can it reach the destination IP?
        |
        +-- No
        |    -> Routing / firewall / ACL / VLAN / server
        |
        v
Can it reach the required TCP/UDP port?
        |
        +-- No
        |    -> Firewall / service / TCP / UDP
        |
        v
Does hostname resolution work?
        |
        +-- No
        |    -> DNS
        |
        v
Does the application work?
        |
        +-- No
             -> Application / authentication / configuration
```

This combines the two troubleshooting styles:

```text
Service-oriented thinking
    |
    +-- DHCP
    +-- DNS
    +-- AD
    +-- HTTP
    +-- SMB
    +-- etc.

Layer-oriented thinking
    |
    +-- Physical
    +-- Local network
    +-- IP / routing
    +-- TCP / UDP
    +-- Application
```

You do not need to choose one approach.

Use both.

---

# 18. Useful Commands

## Linux

Display routing table:

```bash
ip route
```

Display routes:

```bash
ip route show
```

Display IP configuration:

```bash
ip addr
```

Test connectivity:

```bash
ping 10.10.10.1
```

Trace the network path:

```bash
traceroute 8.8.8.8
```

or:

```bash
tracepath 8.8.8.8
```

---

## Windows

Display IP configuration:

```powershell
ipconfig
```

Display routing table:

```powershell
route print
```

or:

```powershell
Get-NetRoute
```

Test connectivity:

```powershell
ping 10.10.10.1
```

Test a TCP port:

```powershell
Test-NetConnection 10.20.20.10 -Port 443
```

Trace the network path:

```powershell
tracert 8.8.8.8
```

---

# 19. What a System Engineer Should Know

## Core Knowledge

```text
[x] What a routing table is
[x] Why hosts have routing tables
[x] Why routers have routing tables
[x] Destination networks
[x] Next hop
[x] Default route
[x] Default gateway
[x] Directly connected routes
[x] Static routes
[x] Dynamic routes
[x] Longest prefix match
[x] Routing between subnets
[x] Routing between VLANs
[x] DHCP vs routing
[x] How to inspect routing tables
[x] How routing problems appear during troubleshooting
```

## Learn Later

```text
[ ] Static routing configuration
[ ] OSPF
[ ] BGP
[ ] Route metrics
[ ] Administrative distance
[ ] Policy-based routing
[ ] NAT
[ ] IPv6 routing
[ ] Advanced routing protocols
```

You do not need to become a routing protocol expert yet.

---

# 20. Core Mental Model

Keep this model in your head:

```text
                         DHCP
                           |
                           | Provides network configuration
                           v
                        Client
                           |
                           | "I need to reach 10.20.20.50"
                           v
                    Client Routing Table
                           |
                           | "Use this route"
                           v
                    Default Gateway
                           |
                           v
                    Router / L3 Switch
                           |
                           | Checks its own
                           | routing table
                           v
                       Destination
```

The simplest definition to remember is:

> **A routing table tells a device where to forward IP traffic based on the destination IP address.**

The practical troubleshooting question is:

> **"Does this device know where to send traffic to the destination?"**

If it does not, you are likely dealing with a **routing problem**.
