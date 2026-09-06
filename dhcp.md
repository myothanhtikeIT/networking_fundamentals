# DHCP Fundamentals
>DHCP basics :) 
## What Is DHCP?

**DHCP (Dynamic Host Configuration Protocol)** automatically provides network configuration to clients.


> DHCP gives computers IP addresses.
> **DHCP automatically provides clients with the network configuration they need to communicate on a network.**

A DHCP server can provide:

* IP address
* Subnet mask
* Default gateway
* DNS server
* DNS domain/suffix
* Other DHCP options
* Network boot information in some environments

---

# 1. DHCP vs Static IP

Without DHCP, an administrator could manually configure every machine:

```text
IP address:      10.10.10.50
Subnet mask:     255.255.255.0
Default gateway: 10.10.10.1
DNS server:      10.10.10.10
```

With DHCP, the client asks for its configuration automatically.

```text
Client
   |
   | "I need network configuration"
   v
DHCP Server
   |
   | "Here is your configuration"
   v
Client
```

DHCP reduces manual configuration and prevents many IP configuration mistakes.

---

# 2. DORA

The basic DHCP process is known as **DORA**:

```text
D = Discover
O = Offer
R = Request
A = Acknowledge
```

## Discover

The client broadcasts a DHCP Discover message:

```text
"Is there a DHCP server available?"
```

The client does not yet have a usable IP configuration.

## Offer

A DHCP server responds with an offer:

```text
"You can use 10.10.10.57."
```

The offer can also contain other network configuration.

## Request

The client requests the offered configuration:

```text
"I want 10.10.10.57."
```

## Acknowledge

The DHCP server confirms the assignment:

```text
"10.10.10.57 is yours for this lease period."
```

The client can now configure its network interface.

---

# 3. DHCP Lease

DHCP normally does not permanently assign an IP address.

Instead, it provides the address for a **lease period**.

Example:

```text
IP address: 10.10.10.57
Lease duration: 8 hours
```

The client attempts to renew the lease before it expires.

If a device disappears from the network, its address can eventually return to the available DHCP pool.

This allows a DHCP server to manage a pool of addresses efficiently.

---

# 4. DHCP Scope

A **DHCP scope** defines the network and address range that DHCP manages.

Example:

```text
Network:
10.10.10.0/24

DHCP range:
10.10.10.100 - 10.10.10.200
```

The DHCP server can dynamically assign addresses within that range.

Example:

```text
PC-01 -> 10.10.10.100
PC-02 -> 10.10.10.101
PC-03 -> 10.10.10.102
```

A scope can also contain configuration such as:

* Lease duration
* Exclusions
* Reservations
* DHCP options

---

# 5. DHCP Exclusions

An **exclusion** tells DHCP:

> Do not dynamically assign these addresses.

Example:

```text
DHCP scope:
10.10.10.1 - 10.10.10.254

Excluded:
10.10.10.1
10.10.10.10
10.10.10.11
10.10.10.12
```

Those addresses can then be manually assigned to infrastructure devices.

Example:

```text
10.10.10.1   Router
10.10.10.10  Domain Controller
10.10.10.11  File Server
10.10.10.12  NAS
```

---

# 6. DHCP Reservations

A **reservation** associates a particular device with a particular IP address.

Example:

```text
Device MAC:
AA:BB:CC:DD:EE:FF

Reserved IP:
10.10.10.50
```

Whenever that device requests DHCP, the DHCP server gives it:

```text
10.10.10.50
```

Reservations are useful for devices that need predictable addresses while still using DHCP.

Common examples:

* Printers
* Access points
* NAS devices
* Cameras
* IoT devices
* Specialized equipment

### Exclusion vs Reservation

```text
Exclusion:
"Nobody gets this IP automatically."

Reservation:
"This specific device gets this IP automatically."
```

---

# 7. DHCP Options

DHCP can provide much more than an IP address.

Common examples include:

| DHCP Option | Purpose         |
| ----------- | --------------- |
| Option 3    | Default gateway |
| Option 6    | DNS servers     |
| Option 15   | DNS domain name |

Example client configuration:

```text
IP address:
10.10.10.57

Subnet mask:
255.255.255.0

Default gateway:
10.10.10.1

DNS server:
10.10.10.10

DNS suffix:
corp.local
```

The important concept is:

> **DHCP distributes network configuration, not merely IP addresses.**

You do not need to memorize every DHCP option number.

---

# 8. DHCP and DNS

DHCP and DNS frequently work together.

For example:

```text
Windows DHCP
      |
      v
Client receives:
      |
      +-- IP address
      +-- Default gateway
      +-- DNS server
      +-- DNS suffix
```

In a Microsoft environment:

```text
Windows Server
├── Active Directory Domain Services
├── DNS
└── DHCP
```

A Windows client might receive:

```text
IP:
10.10.10.57

DNS:
10.10.10.10

DNS suffix:
corp.local
```

It can then resolve names such as:

```text
dc01.corp.local
```

to an IP address.

### Important distinction

Active Directory fundamentally depends on **DNS**.

DHCP is useful and commonly integrated with AD environments, but AD does not require DHCP.

A domain-joined computer can use a static IP address.

---

# 9. DHCP and Active Directory

In a Microsoft-heavy environment, DHCP can be part of a centralized infrastructure:

```text
                 Windows Server
                      |
        +-------------+-------------+
        |             |             |
       AD DS         DNS           DHCP
        |             |             |
        +-------------+-------------+
                      |
                 Windows Clients
```

DHCP provides network configuration.

DNS provides name resolution.

Active Directory provides identity, authentication, computer management, Group Policy, and other directory services.

These services complement each other but perform different jobs.

---

# 10. Where Can DHCP Run?

DHCP does not have to run on Windows Server.

Common DHCP providers include:

```text
Home router
Firewall
Network appliance
Windows Server
Linux server
Dedicated network infrastructure
```

The important question is:

> **Which system is responsible for providing DHCP to this network or VLAN?**

---

# 11. Router DHCP vs Windows DHCP

## Router/Firewall DHCP

Common in small networks.

```text
Internet
   |
Firewall / Router
   |
   +-- DHCP
   |
Clients
```

Advantages:

* Simple
* Cheap
* Easy to configure
* No additional server required

Perfectly appropriate for many small businesses.

## Windows Server DHCP

Common in Microsoft-heavy business environments.

```text
Firewall
   |
Switch
   |
Windows Server
├── AD DS
├── DNS
└── DHCP
   |
Windows Clients
```

Advantages can include:

* Centralized administration
* Integration with Microsoft infrastructure
* Multiple scopes
* Reservations
* DHCP policies
* Detailed configuration
* DHCP failover
* DNS integration
* Better suitability for larger environments

Windows DHCP is not inherently better than router DHCP.

The appropriate choice depends on the organization's architecture and requirements.

---

# 12. Multiple DHCP Scopes

Organizations can have multiple networks or VLANs.

Example:

```text
VLAN 10
Employees
10.10.10.0/24

VLAN 20
Servers
10.10.20.0/24

VLAN 30
Guest Wi-Fi
10.10.30.0/24

VLAN 40
Cameras
10.10.40.0/24
```

A DHCP server can manage separate scopes for these networks.

```text
                 DHCP Server
                      |
          +-----------+-----------+
          |           |           |
       Scope 10    Scope 20    Scope 30
          |           |           |
       VLAN 10     VLAN 20     VLAN 30
```

This becomes particularly useful in enterprise networks.

---

# 13. DHCP Relay

DHCP clients initially use broadcasts to discover DHCP servers.

Routers normally do not forward broadcasts between different networks.

Example:

```text
Client Network
10.10.20.0/24
      |
      v
L3 Switch / Router
      |
      v
Server Network
10.10.10.0/24
      |
      v
DHCP Server
10.10.10.10
```

The DHCP server is on a different subnet.

A **DHCP relay** allows the router or Layer 3 switch to forward DHCP requests to the DHCP server.

Conceptually:

```text
Client
   |
   | DHCP Broadcast
   v
L3 Switch / Router
   |
   | DHCP Relay
   v
DHCP Server
```

DHCP relay is an important concept when working with:

* VLANs
* Layer 3 switching
* Enterprise networks
* Centralized DHCP servers

---

# 14. DHCP Failure

Suppose the DHCP server goes offline:

```text
DHCP Server
     X
   OFFLINE
```

Existing clients may continue working temporarily because they already have valid leases.

New clients may fail to obtain an IP address.

Existing clients can eventually experience problems when their leases expire and they cannot renew them.

Therefore, DHCP can become an important availability dependency in larger environments.

This is one reason organizations may implement DHCP redundancy or failover.

---

# 15. DHCP Failover

A larger environment can use multiple DHCP servers.

Conceptually:

```text
             DHCP Clients
                  |
          +-------+-------+
          |               |
      DHCP Server 1    DHCP Server 2
```

If one DHCP server fails, another can continue providing DHCP service.

This improves availability.

The exact implementation depends on the DHCP platform and network architecture.

---

# 16. DHCP and Network Boot

DHCP can also participate in **PXE/network boot** environments.

A simplified process:

```text
New computer
     |
     | DHCP
     v
DHCP infrastructure
     |
     | Network boot information
     v
PXE infrastructure
     |
     v
OS deployment
```

This can allow organizations to deploy operating systems to large numbers of computers without manually installing each machine.

PXE is a later topic, but DHCP's involvement is worth knowing.

---

# 17. DHCP Does Not Do Everything

It is important not to confuse DHCP with other network services.

| Service      | Main responsibility                          |
| ------------ | -------------------------------------------- |
| DHCP         | Provides network configuration               |
| DNS          | Resolves names to IP addresses               |
| Router       | Moves traffic between networks               |
| Firewall     | Controls network traffic                     |
| AD DS        | Identity, authentication, directory services |
| Group Policy | Centralized Windows configuration/policy     |

These services commonly work together:

```text
                    Client
                      |
             +--------+--------+
             |        |        |
            DHCP     DNS      AD DS
             |        |        |
       Network      Name     Identity
      config     resolution  and policy
```

---

# 18. DHCP Security

DHCP is infrastructure and can be attacked or misconfigured.

One important example is a **rogue DHCP server**.

Imagine the legitimate DHCP server says:

```text
DNS server:
10.10.10.10
```

But an unauthorized device starts answering DHCP requests:

```text
DNS server:
10.99.99.99
```

Clients could receive malicious or incorrect network configuration.

Enterprise networks therefore use technologies and controls such as:

* DHCP snooping
* Switch security
* Network segmentation
* Access control
* Monitoring
* Proper DHCP authorization

This becomes particularly relevant to networking and cybersecurity.

---

# 19. The DHCP Mental Model

Do not think:

```text
DHCP = IP address dispenser
```

Think:

```text
                 DHCP
                   |
        +----------+----------+
        |          |          |
       IP       Gateway      DNS
        |          |          |
        +----------+----------+
                   |
                 Client
```

DHCP automatically provides the network configuration a client needs.

---

# 20. What a System Engineer Should Know

For practical System Engineering work, understand these concepts well:

```text
[x] What DHCP does
[x] DORA
[x] DHCP leases
[x] DHCP scopes
[x] DHCP address pools
[x] DHCP exclusions
[x] DHCP reservations
[x] DHCP options
[x] Default gateway
[x] DNS configuration through DHCP
[x] DHCP and DNS relationship
[x] Multiple DHCP scopes
[x] DHCP relay
[x] DHCP and VLANs
[x] Router DHCP vs Server DHCP
[x] DHCP failure behavior
[x] DHCP redundancy/failover
[x] Rogue DHCP servers
```

Later, learn:

```text
[ ] DHCP snooping
[ ] PXE/network boot
[ ] DHCP policies
[ ] DHCP security
[ ] DHCPv6
[ ] Detailed DHCP packet analysis
```

You do not need to memorize every DHCP option or become a DHCP protocol archaeologist.

---

# 21. Lab Exercises

For a Windows Server 2022 lab, useful exercises are:

1. Install the DHCP Server role.
2. Create a DHCP scope.
3. Configure a DHCP address pool.
4. Configure exclusions.
5. Configure a reservation.
6. Configure DNS and default gateway options.
7. Connect a Windows 11 client.
8. Verify the DHCP lease.
9. Release and renew the client's lease.
10. Stop the DHCP Server service and observe what happens.
11. Create a second scope.
12. Build a second VLAN.
13. Configure DHCP relay.
14. Break DHCP configuration deliberately.
15. Troubleshoot why a client cannot obtain an IP address.
16. Investigate DHCP leases and server logs.

The goal is not merely to know what DHCP stands for.

The goal is to be able to look at a network and answer:

> **"Who is providing DHCP, what scope is this client using, what configuration did it receive, and what happens if DHCP disappears?"**
That is practical DHCP knowledge.
