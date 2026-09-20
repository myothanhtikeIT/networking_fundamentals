# Networking Troubleshooting & Everyday Protocol Cheatsheet

> Practical networking notes for Linux, Windows/PowerShell, and small
> enterprise labs.
>
> Focus: the stuff you will actually use while troubleshooting servers,
> clients, routers, DNS, AD, SSH, SMB, and connectivity.

------------------------------------------------------------------------

## 1. The Mental Model

Think about networking as layers of responsibility.

``` text
Application
    SSH / HTTP(S) / DNS / SMB / DHCP / RDP
        |
Transport
    TCP / UDP
        |
Network
    IPv4 / IPv6 / ICMP
        |
Link
    Ethernet / Wi-Fi
```

A command is usually an application/tool that uses one or more protocols
underneath.

Examples:

``` text
ping 10.20.10.100
    -> ICMP -> IP -> Ethernet/Wi-Fi

ssh user@10.20.10.100
    -> SSH -> TCP/22 -> IP -> Ethernet/Wi-Fi

https://example.com
    -> HTTPS -> TCP/443 -> IP -> Ethernet/Wi-Fi

\\CC-FILE01\Public
    -> SMB -> TCP/445 -> IP -> Ethernet/Wi-Fi
```

### Important distinction

-   **Protocol** = rules/language used by networked systems.
-   **Port** = logical endpoint for TCP/UDP traffic.
-   **Command/tool** = program that uses a protocol.
-   **IP address** = identifies a network interface/host at the IP
    layer.

------------------------------------------------------------------------

# 2. ICMP

**ICMP = Internet Control Message Protocol**

Used for network diagnostics and control/error messages.

The most familiar example is `ping` command

``` bash
ping 10.20.10.100
```

Conceptually:

``` text
Debian
  |
  | ICMP Echo Request
  v
Win11
  |
  | ICMP Echo Reply
  v
Debian
```

ICMP does **not** use TCP/UDP ports.

so:

``` text
ping 10.20.10.100
```

and:

``` bash
nc -zv 10.20.10.100 22
```

test different things.

A host can block ICMP while still accepting TCP connections.

------------------------------------------------------------------------

# 3. TCP

**TCP = Transmission Control Protocol**

TCP provides a reliable, connection-oriented transport.

It uses **ports**.

Examples:

``` text
TCP/22    SSH
TCP/80    HTTP
TCP/443   HTTPS
TCP/445   SMB
TCP/3389  RDP
```

A TCP connection roughly begins with a three-way handshake:

``` text
Client -> SYN
Server -> SYN-ACK
Client -> ACK
```

You normally do not interact with TCP directly. Applications use it.

Examples:

``` bash
ssh user@10.20.10.10
curl https://example.com
smbclient //10.20.10.10/Public
```

All of those may use TCP underneath.

------------------------------------------------------------------------

# 4. UDP

**UDP = User Datagram Protocol**

UDP is connectionless and has lower protocol overhead than TCP.

It also uses ports.

Common examples:

``` text
UDP/53     DNS
UDP/67-68  DHCP
UDP/123    NTP
UDP/161    SNMP
```

UDP does not provide TCP-style connection establishment or delivery
guarantees.

Applications choose TCP or UDP depending on their requirements.

------------------------------------------------------------------------

# 5. SMB

**SMB = Server Message Block**

SMB is heavily used in Windows environments for:

-   Network file shares
-   Shared folders
-   Printer sharing
-   Windows resource access
-   Some Windows administration/infrastructure operations

Modern SMB commonly uses:

``` text
TCP/445
```

Windows example:

``` text
\\CC-FILE01\Public
```

Windows CMD:

``` cmd
net use Z: \\CC-FILE01\Public
```

Linux:

``` bash
smbclient -L //10.20.10.10 -U Administrator
```

Connect to a share:

``` bash
smbclient //10.20.10.10/Public -U batman
```

Basic mental model:

``` text
SMB
 |
TCP/445
 |
IP
 |
Ethernet/Wi-Fi
```

------------------------------------------------------------------------

# 6. Essential Everyday Ports

## Core Infrastructure

    Port Protocol   Name / Service         Typical Use
  ------ ---------- ---------------------- ------------------------------------
      20 TCP        FTP Data               FTP data channel
      21 TCP        FTP                    File Transfer Protocol
      22 TCP        SSH                    Secure remote shell / SCP / SFTP
      23 TCP        Telnet                 Legacy remote terminal
      25 TCP        SMTP                   Mail transfer
      53 TCP/UDP    DNS                    Name resolution / DNS operations
      67 UDP        DHCP Server            DHCP server side
      68 UDP        DHCP Client            DHCP client side
      80 TCP        HTTP                   Web traffic
      88 TCP/UDP    Kerberos               Active Directory authentication
     110 TCP        POP3                   Email retrieval
     123 UDP        NTP                    Time synchronization
     135 TCP        RPC Endpoint Mapper    Windows RPC
     137 UDP        NetBIOS Name           Legacy Windows name service
     138 UDP        NetBIOS Datagram       Legacy Windows networking
     139 TCP        NetBIOS Session        Legacy SMB
     143 TCP        IMAP                   Email retrieval
     161 UDP        SNMP                   Network/device monitoring
     162 UDP        SNMP Trap              SNMP notifications
     389 TCP/UDP    LDAP                   Directory access
     443 TCP        HTTPS                  Secure web traffic
     445 TCP        SMB                    Modern Windows file sharing
     464 TCP/UDP    Kerberos Password      Kerberos password changes
     500 UDP        IKE                    IPsec key exchange
     514 UDP        Syslog                 Logging
     636 TCP        LDAPS                  LDAP over TLS
    1433 TCP        Microsoft SQL Server   SQL Server
    3389 TCP/UDP    RDP                    Windows Remote Desktop
    5985 TCP        WinRM HTTP             Windows Remote Management
    5986 TCP        WinRM HTTPS            Windows Remote Management over TLS

## Active Directory / Windows Ports Worth Knowing

``` text
53      DNS
88      Kerberos
135     RPC Endpoint Mapper
389     LDAP
445     SMB
464     Kerberos password changes
636     LDAPS
3268    Global Catalog
3269    Global Catalog over SSL/TLS
3389    RDP
5985    WinRM HTTP
5986    WinRM HTTPS
```

For an AD troubleshooting lab, these are especially important.

------------------------------------------------------------------------

# 7. DNS

**DNS = Domain Name System**

DNS translates names into IP addresses and provides other records used
by applications and infrastructure.

Example:

``` bash
nslookup google.com
```

Your ChocolateCompany lab:

``` bash
nslookup corp.chocolatecompany.local
nslookup CC-DC01.corp.chocolatecompany.local
```

AD relies heavily on DNS.

Important DNS record types:

  Record   Purpose
  -------- ------------------------------------
  A        Name -\> IPv4
  AAAA     Name -\> IPv6
  CNAME    Alias
  MX       Mail server
  NS       Authoritative name server
  PTR      IP -\> name
  SRV      Service discovery
  TXT      Arbitrary text / verification data

AD uses SRV records heavily.

Example:

``` bash
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.chocolatecompany.local
```

------------------------------------------------------------------------

# 8. DHCP

**DHCP = Dynamic Host Configuration Protocol**

Used to automatically provide network configuration.

Typical information supplied:

``` text
IP address
Subnet mask
Default gateway
DNS server
Lease information
```

Typical flow:

``` text
DHCP Discover
      |
DHCP Offer
      |
DHCP Request
      |
DHCP ACK
```

Common ports:

``` text
UDP/67  Server
UDP/68  Client
```

------------------------------------------------------------------------

# 9. NTP

**NTP = Network Time Protocol**

Used for clock synchronization.

Common port:

``` text
UDP/123
```

Time is particularly important in Active Directory.

Kerberos authentication is sensitive to clock differences.

Useful Linux command:

``` bash
timedatectl
```

Useful Windows command:

``` powershell
w32tm /query /status
```

------------------------------------------------------------------------

# 10. Linux Networking Commands

## View IP configuration

Modern Linux:

``` bash
ip addr
```

Short form:

``` bash
ip a
```

View routes:

``` bash
ip route
```

View interfaces:

``` bash
ip link
```

View neighbors/ARP-like information:

``` bash
ip neigh
```

------------------------------------------------------------------------

## Connectivity

Ping:

``` bash
ping 10.20.10.10
```

Limiting the number of packets:

``` bash
ping -c 4 10.20.10.10
```

Trace route:

``` bash
traceroute 10.20.10.10
```

If `traceroute` isn't installed:

``` bash
tracepath 10.20.10.10
```

------------------------------------------------------------------------

## Test a TCP port

Netcat:

``` bash
nc -zv 10.20.10.10 22
```

Meaning:

``` text
nc   = netcat
-z   = zero-I/O / just test the connection
-v   = verbose
IP   = destination
22   = TCP port
```

Examples:

``` bash
nc -zv 10.20.10.10 22
nc -zv 10.20.10.10 53
nc -zv 10.20.10.10 445
nc -zv 10.20.10.10 3389
```

------------------------------------------------------------------------

## DNS troubleshooting

``` bash
nslookup google.com
```

``` bash
nslookup corp.chocolatecompany.local
```

``` bash
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.chocolatecompany.local
```

If installed:

``` bash
dig google.com
```

``` bash
dig @10.20.10.10 corp.chocolatecompany.local
```

------------------------------------------------------------------------

## DNS cache

On systems using systemd-resolved:

``` bash
resolvectl status
```

Query DNS:

``` bash
resolvectl query google.com
```

------------------------------------------------------------------------

## Routing

``` bash
ip route
```

Find the route used to reach a destination:

``` bash
ip route get 10.20.10.100
```

This is extremely useful when troubleshooting multiple
networks/VLANs/routes.

------------------------------------------------------------------------

## ARP / neighbor discovery

``` bash
ip neigh
```

Example:

``` text
10.20.10.1 dev eth0 lladdr xx:xx:xx:xx:xx:xx REACHABLE
```

------------------------------------------------------------------------

## Socket / listening-port inspection

Modern Linux:

``` bash
ss -tulpn
```

Useful variants:

``` bash
ss -tln
ss -uln
ss -tn
```

Meaning:

``` text
-t = TCP
-u = UDP
-l = listening
-p = process
-n = don't resolve names
```

------------------------------------------------------------------------

## Packet capture

`tcpdump` is one of the most useful troubleshooting tools.

Capture an interface:

``` bash
sudo tcpdump -i eth0
```

Capture TCP/22:

``` bash
sudo tcpdump -i eth0 tcp port 22
```

Capture ICMP:

``` bash
sudo tcpdump -i eth0 icmp
```

Capture traffic to/from a host:

``` bash
sudo tcpdump -i eth0 host 10.20.10.100
```

------------------------------------------------------------------------

# 11. Windows / PowerShell Networking Commands

## IP configuration

Classic:

``` powershell
ipconfig
```

Detailed:

``` powershell
ipconfig /all
```

PowerShell-native:

``` powershell
Get-NetIPConfiguration
```

IP addresses:

``` powershell
Get-NetIPAddress
```

Network adapters:

``` powershell
Get-NetAdapter
```

------------------------------------------------------------------------

## Connectivity

``` powershell
ping 10.20.10.10
```

PowerShell can also use:

``` powershell
Test-Connection 10.20.10.10
```

------------------------------------------------------------------------

## Test a TCP port

The Windows equivalent of:

``` bash
nc -zv 10.20.10.10 22
```

is:

``` powershell
Test-NetConnection 10.20.10.10 -Port 22
```

Examples:

``` powershell
Test-NetConnection 10.20.10.10 -Port 22
Test-NetConnection 10.20.10.10 -Port 53
Test-NetConnection 10.20.10.10 -Port 445
Test-NetConnection 10.20.10.10 -Port 3389
```

Look for:

``` text
TcpTestSucceeded : True
```

------------------------------------------------------------------------

## Routing

``` powershell
Get-NetRoute
```

IPv4 routes:

``` powershell
Get-NetRoute -AddressFamily IPv4
```

Classic:

``` cmd
route print
```

------------------------------------------------------------------------

## ARP / neighbors

``` powershell
Get-NetNeighbor
```

Classic:

``` cmd
arp -a
```

------------------------------------------------------------------------

## DNS

``` powershell
Resolve-DnsName google.com
```

Specific DNS server:

``` powershell
Resolve-DnsName google.com -Server 10.20.10.10
```

Classic:

``` cmd
nslookup google.com
```

------------------------------------------------------------------------

## DNS client configuration

``` powershell
Get-DnsClientServerAddress
```

------------------------------------------------------------------------

## Listening TCP ports

``` powershell
Get-NetTCPConnection -State Listen
```

Specific port:

``` powershell
Get-NetTCPConnection -LocalPort 22
```

Listening UDP:

``` powershell
Get-NetUDPEndpoint
```

------------------------------------------------------------------------

## Windows Firewall

List firewall profiles:

``` powershell
Get-NetFirewallProfile
```

List rules:

``` powershell
Get-NetFirewallRule
```

Find SSH rules:

``` powershell
Get-NetFirewallRule -Name *ssh*
```

------------------------------------------------------------------------

## Services

Check SSH:

``` powershell
Get-Service sshd
```

Check DNS:

``` powershell
Get-Service DNS
```

Check DHCP:

``` powershell
Get-Service DHCPServer
```

Start a service:

``` powershell
Start-Service sshd
```

Stop:

``` powershell
Stop-Service sshd
```

Set automatic startup:

``` powershell
Set-Service sshd -StartupType Automatic
```

------------------------------------------------------------------------

# 12. SSH

SSH normally uses:

``` text
TCP/22
```

From Linux:

``` bash
ssh user@10.20.10.10
```

Specify a port:

``` bash
ssh -p 22 user@10.20.10.10
```

Verbose troubleshooting:

``` bash
ssh -v user@10.20.10.10
```

More verbose:

``` bash
ssh -vv user@10.20.10.10
```

Very verbose:

``` bash
ssh -vvv user@10.20.10.10
```

Copy files with SCP:

``` bash
scp file.txt user@10.20.10.10:/tmp/
```

------------------------------------------------------------------------

# 13. Common Windows SSH Setup

Install OpenSSH Server:

``` powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

Start:

``` powershell
Start-Service sshd
```

Automatic startup:

``` powershell
Set-Service -Name sshd -StartupType Automatic
```

Verify:

``` powershell
Get-Service sshd
```

Verify listening:

``` powershell
Get-NetTCPConnection -LocalPort 22 -State Listen
```

From Linux:

``` bash
nc -zv 10.20.10.100 22
```

Then:

``` bash
ssh user@10.20.10.100
```

------------------------------------------------------------------------

# 14. Active Directory Networking Essentials

For AD, core services are:

``` text
DNS
Kerberos
LDAP
SMB
RPC
Global Catalog
NTP / time synchronization
```

Useful ports:

``` text
53      DNS
88      Kerberos
135     RPC Endpoint Mapper
389     LDAP
445     SMB
464     Kerberos password changes
636     LDAPS
3268    Global Catalog
3269    Global Catalog SSL/TLS
```

AD DNS checks:

``` powershell
Resolve-DnsName corp.chocolatecompany.local
```

``` powershell
Resolve-DnsName CC-DC01.corp.chocolatecompany.local
```

``` powershell
Resolve-DnsName _ldap._tcp.dc._msdcs.corp.chocolatecompany.local -Type SRV
```

Linux:

``` bash
nslookup corp.chocolatecompany.local
```

``` bash
nslookup CC-DC01.corp.chocolatecompany.local
```

``` bash
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.chocolatecompany.local
```

------------------------------------------------------------------------

# 15. A Practical Troubleshooting Sequence

When something cannot connect, **do not immediately start changing
configuration**.

Work from the bottom up.

## Step 1: Check the IP configuration

Linux:

``` bash
ip a
ip route
```

Windows:

``` powershell
ipconfig /all
Get-NetIPConfiguration
```

Check:

``` text
IP address
Subnet mask/prefix
Default gateway
DNS server
```

------------------------------------------------------------------------

## Step 2: Check basic reachability

``` bash
ping <destination>
```

or:

``` powershell
Test-Connection <destination>
```

Remember: failure here does not automatically mean TCP is broken because
ICMP may be blocked.

------------------------------------------------------------------------

## Step 3: Check routing

Linux:

``` bash
ip route get <destination>
```

Windows:

``` powershell
Get-NetRoute
```

Ask:

> "Where does my machine think this packet should go?"

------------------------------------------------------------------------

## Step 4: Check the actual service port

Linux:

``` bash
nc -zv <IP> <PORT>
```

Windows:

``` powershell
Test-NetConnection <IP> -Port <PORT>
```

Example:

``` bash
nc -zv 10.20.10.100 22
```

------------------------------------------------------------------------

## Step 5: Check whether the service is listening

Linux:

``` bash
ss -tln
```

Windows:

``` powershell
Get-NetTCPConnection -State Listen
```

Specific Windows port:

``` powershell
Get-NetTCPConnection -LocalPort 22
```

------------------------------------------------------------------------

## Step 6: Check the firewall

Linux depends on the firewall framework:

``` bash
sudo nft list ruleset
```

If using UFW:

``` bash
sudo ufw status
```

Windows:

``` powershell
Get-NetFirewallProfile
Get-NetFirewallRule
```

------------------------------------------------------------------------

## Step 7: Check the application

Examples:

``` bash
ssh user@10.20.10.100
```

``` bash
curl https://example.com
```

``` bash
smbclient -L //10.20.10.10 -U Administrator
```

------------------------------------------------------------------------

## Step 8: Check authentication

If the TCP connection works but login fails, you have probably moved
beyond basic network connectivity.

Example:

``` text
TCP/22 reachable
        |
        v
SSH server responds
        |
        v
Authentication fails
        |
        v
Check username/password/authentication method
```

------------------------------------------------------------------------

# 16. The "Where Is It Broken?" Cheat Sheet

``` text
ping fails
    |
    +--> Check IP
    +--> Check subnet
    +--> Check route
    +--> Check gateway
    +--> Check VLAN/network
    +--> Check ICMP firewall

ping works
    |
    v
TCP port test
    |
    +--> FAIL
    |      |
    |      +--> Firewall
    |      +--> Service not listening
    |      +--> Wrong IP
    |      +--> Routing/ACL
    |
    +--> SUCCESS
           |
           v
       Application
           |
           +--> Authentication
           |
           +--> Authorization
           |
           +--> Application configuration
```

This is one of the most useful troubleshooting mental models to keep.

------------------------------------------------------------------------

# 17. Quick Daily/Weekly Checklist

## Daily

When something suddenly cannot connect:

``` bash
ip a
ip route
ping <gateway>
ping <destination>
nc -zv <destination> <port>
```

Windows:

``` powershell
ipconfig /all
Get-NetIPConfiguration
Test-Connection <destination>
Test-NetConnection <destination> -Port <port>
```

------------------------------------------------------------------------

## Weekly

Check:

``` text
DNS resolution
DHCP leases
IP conflicts
Default gateways
Routing
Firewall rules
Listening services
Network interface status
Time synchronization
```

Useful commands:

``` bash
ip neigh
ss -tulpn
resolvectl status
```

``` powershell
Get-NetAdapter
Get-NetNeighbor
Get-NetTCPConnection -State Listen
Get-DnsClientServerAddress
w32tm /query /status
```

------------------------------------------------------------------------

## Monthly / Lab Maintenance

Review:

``` text
IP addressing
VLANs
Routing
DNS records
DHCP scopes
Firewall rules
Unused services
Listening ports
SSH access
SMB shares
AD DNS health
Time synchronization
Backup connectivity
```

For your ChocolateCompany lab, also deliberately break things and
troubleshoot them:

``` text
Wrong DNS
Wrong gateway
Blocked TCP port
Stopped service
Bad firewall rule
Wrong subnet
Broken route
Wrong DNS SRV record
Incorrect AD DNS configuration
Expired/incorrect credentials
Time skew
SMB permission problems
```

------------------------------------------------------------------------

# 18. The Core Commands Worth Memorizing

## Linux

``` bash
ip a
ip route
ip route get <IP>
ip neigh
ping <IP>
nc -zv <IP> <PORT>
ss -tulpn
nslookup <name>
dig <name>
traceroute <IP>
tcpdump
ssh user@<IP>
```

## Windows PowerShell

``` powershell
ipconfig /all
Get-NetIPConfiguration
Get-NetIPAddress
Get-NetAdapter
Get-NetRoute
Get-NetNeighbor
ping <IP>
Test-Connection <IP>
Test-NetConnection <IP> -Port <PORT>
Resolve-DnsName <name>
Get-NetTCPConnection -State Listen
Get-NetFirewallProfile
Get-NetFirewallRule
Get-Service
w32tm /query /status
```

------------------------------------------------------------------------

# 19. Grandpa Rick's Final Mental Model

When someone says:

Think in :

``` text
1. Do I have the right IP configuration?
          |
2. Do I have a route?
          |
3. Can I reach the host?
          |
4. Can I reach the required TCP/UDP port?
          |
5. Is the service listening?
          |
6. Is a firewall/ACL blocking it?
          |
7. Is the application working?
          |
8. Can I authenticate?
          |
9. Am I authorized?
```

And the troubleshooting commands

``` bash
ping 10.20.10.100
```

**ICMP reachability**

``` bash
nc -zv 10.20.10.100 22
```

**TCP/22 connectivity**

``` bash
ssh batman@10.20.10.100
```

**Actual SSH application + authentication**

That progression is the foundation of practical network troubleshooting.

------------------------------------------------------------------------

## Lab Reference

Current ChocolateCompany lab examples:

``` text
VyOS
10.20.10.1

CC-DC01
10.20.10.10
Windows Server 2022
AD DS + DNS

WIN11
10.20.10.100
Windows 11
Domain client / workstation

Debian 13
Management workstation / virt-manager host
```

Useful tests:

``` bash
ping 10.20.10.1
ping 10.20.10.10
ping 10.20.10.100

nc -zv 10.20.10.10 22
nc -zv 10.20.10.100 22
nc -zv 10.20.10.10 53
nc -zv 10.20.10.10 445
```

Windows:

``` powershell
Test-NetConnection 10.20.10.10 -Port 22
Test-NetConnection 10.20.10.10 -Port 53
Test-NetConnection 10.20.10.10 -Port 445
Test-NetConnection 10.20.10.100 -Port 22
```

> Keep this file evolving. Add commands and protocols as you encounter
> them in the lab rather than trying to memorize the entire networking
> universe at once.
