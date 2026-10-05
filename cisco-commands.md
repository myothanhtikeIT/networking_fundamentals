# Cisco IOS Cheat Sheet: Essential CCNA Commands & Practical Reference

A structured reference guide for Cisco IOS configuration, verification, and troubleshooting. Optimized for CCNA preparation, Packet Tracer labs, and real-world virtualized environments (GNS3/EVE-NG).

---

## 1. System Navigation & Environment Tweeks

These commands control terminal behavior, prevent lockouts, and manage command-line modes.

| Command | Mode / Context | Description | Linux / Shell Analogy |
| :--- | :--- | :--- | :--- |
| `enable` / `ena` | `Router>` | Moves from User mode to Privileged admin mode. | `su` or `sudo su` |
| `disable` | `Router#` | Drops back down to User EXEC mode. | `logout` or `exit` |
| `configure terminal` / `conf t` | `Router#` | Enters Global Configuration mode to change settings. | Entering `/etc/` configuration files |
| `exit` | Any Sub-mode | Steps backward one level in the menu hierarchy. | `cd ..` |
| `end` | Any Sub-mode | Drops straight back to Privileged EXEC mode (`#`). | `Ctrl + C` or `cd ~` |
| `no ip domain-lookup` | `(config)#` | Disables DNS resolution for CLI typos (stops terminal freezing). | Disabling bad command auto-corrections |
| `logging synchronous` | `(config-line)#` | Prevents background system logs from interrupting your typing. | Clean terminal stdout redirection |
| `exec-timeout 0 0` | `(config-line)#` | Disables the terminal auto-logout timer (great for long lab sessions). | Setting `TMOUT=0` |

---

## 2. Essential Device Initialization

Run this baseline configuration block on every new router or switch you deploy.

```text
Router> enable
Router# configure terminal

! 1. Name the device
Router(config)# hostname Core-Router-01

! 2. Prevent terminal freezes on typos
Core-Router-01(config)# no ip domain-lookup

! 3. Secure Privileged EXEC Mode with strong encryption
Core-Router-01(config)# enable secret YourStrongPasswordHere

! 4. Clean up terminal typing loops on Console line
Core-Router-01(config)# line console 0
Core-Router-01(config-line)# logging synchronous
Core-Router-01(config-line)# exec-timeout 0 0
Core-Router-01(config-line)# exit
```

---

## 3. Interface & Layer 2/3 Configuration

Commands used to provision ports, build subnets, allocate VLANs, and configure inter-switch trunks.

### Layer 3 (Routing Interfaces)
*   `interface [type] [number]` (e.g., `interface gigabitethernet 0/0`)  
    Enters configuration mode for a specific physical port.
*   `description [text]`  
    Attaches a logical label to an interface (critical for tracking topologies).
*   `ip address [ip-address] [subnet-mask]`  
    Assigns a static IPv4 address to the interface.
*   `no shutdown` / `no shut`  
    Administratively activates the interface (Cisco router interfaces are shut down by default).
*   `shutdown` / `shut`  
    Disables the port physically and logically.

### Layer 2 (Switching & VLANs)
*   `vlan [vlan-id]` (e.g., `vlan 10`)  
    Creates a broadcast domain/VLAN inside the switch database.
*   `name [vlan-name]`  
    Names the VLAN (e.g., `name Office-Data`).
*   `switchport mode access`  
    Configures a switch port to connect to an endpoint (PC, printer, server).
*   `switchport access vlan [vlan-id]`  
    Binds the access port to a specific VLAN.
*   `switchport mode trunk`  
    Configures an inter-switch link to carry traffic for multiple VLANs simultaneously.
*   `interface vlan [vlan-id]`  
    Creates a Switched Virtual Interface (SVI). Used to give a switch an IP address for remote management.

---

## 4. Fundamental Routing & Infrastructure Services

Commands to establish connectivity across different broadcast domains.

### Basic Routing
*   `ip routing`  
    Enables Layer 3 routing capabilities on multilayer switches (disabled by default on some models).
*   `ip route [destination-network] [subnet-mask] [next-hop-ip]`  
    Creates a static route pointing to a remote subnet.
*   `ip route 0.0.0.0 0.0.0.0 [next-hop-ip]`  
    Creates a Gateway of Last Resort (Default Route) to send all unknown traffic to an upstream router or ISP.

### DHCP Server Configuration
Defines dynamic IP assignment pools on a Cisco router.

```text
! Step 1: Reserve critical static IPs (Gateways, Servers, Printers)
Core-Router-01(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.20

! Step 2: Define the dynamic pool parameters
Core-Router-01(config)# ip dhcp pool Floor2_Office_Pool
Core-Router-01(dhcp-config)# network 192.168.10.0 255.255.255.0
Core-Router-01(dhcp-config)# default-router 192.168.10.1
Core-Router-01(dhcp-config)# dns-server 8.8.8.8
```

---

## 5. System Maintenance & State Preservation

Unlike Linux where changes persist live on disk files, Cisco IOS separates active state from saved state.

*   `copy running-config startup-config` (or short: `copy run start`)  
    Commits current active configurations from RAM to non-volatile memory (NVRAM). **If you do not run this, your work is wiped on reboot.**
*   `write memory` (or short: `wr`)  
    Legacy command that achieves the exact same result as `copy run start`. Highly popular in production due to speed.
*   `erase startup-config`  
    Wipes the saved settings file from NVRAM. Run this followed by `reload` to factory-reset a device.
*   `reload`  
    Hard reboots the hardware chassis or virtual instance.

---

## 6. The "CCNA Grind" Verification & Troubleshooting Suite

These are your primary diagnostics tools. Run these from Privileged EXEC mode (`#`). If inside a sub-config menu, prefix them with `do` (e.g., `do show ip interface brief`).

*   `show ip interface brief` / `sh ip int br`  
    **The Holy Grail Command.** Shows every port, its IP, status (physical layer link status), and protocol (data link layer line protocol).
*   `show running-config` / `sh run`  
    Dumps the entire configuration file actively executing in RAM.
*   `show vlan brief` / `sh vlan br`  
    Displays all existing VLANs on a switch and lists which physical interfaces belong to them.
*   `show interfaces trunk` / `sh int trunk`  
    Lists all active Layer 2 trunking links, showing allowed VLANs and native encapsulation status.
*   `show ip route` / `sh ip route`  
    Outputs the global routing table. This is the router’s decision engine for where packets travel.
*   `show mac address-table` / `sh mac addr`  
    Displays the Content Addressable Memory (CAM) table mapping hardware MACs to specific switch interfaces.
*   `ping [destination-ip]`  
    Sends ICMP echo requests to confirm end-to-end network reachability.
*   `traceroute [destination-ip]`  
    Traces the hop-by-hop layer 3 path across routers to discover where traffic drops.

---

## 7. CLI Tips, Tricks & Efficiency Secrets

### The `do` Escape Hatch
If you are modifying a port inside `Core-Router-01(config-if)#` and want to check your interfaces, typing `show ip interface brief` returns an error. Prefix the command with `do` to run Privileged EXEC queries from any location without stepping backwards:
```text
Core-Router-01(config-if)# do show ip interface brief
```

### Navigating Terminal Interruption
If you forget to enter `logging synchronous` and a system notification breaks your command line mid-sentence:
*   Press **`Ctrl + L`** or **`Ctrl + R`** to force the CLI to redraw your active, unsubmitted line on a clean line below the system notification.

### Tab Auto-Completion & Question Mark Help
*   Type part of a command and press **`Tab`** to auto-complete it.
*   Type a command followed immediately by a **`?`** (no space) to see character extensions.
*   Type a command, enter a **space**, and then type a **`?`** to view all valid arguments or variables that must follow next.

### Aborting Runaway Commands
If you ping an invalid address or type an unrecognized word without `no ip domain-lookup` configured, the terminal will freeze while trying to broadcast a search query.
*   Press **`Ctrl + Shift + 6`** simultaneously to force-abort the runaway execution loop and regain command line control instantly.
