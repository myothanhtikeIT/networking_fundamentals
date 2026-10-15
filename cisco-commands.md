# Cisco IOS Essential Commands N Practical References

- A structured reference guide for Cisco IOS configuration, verification, and troubleshooting. Optimized for CCNA preparation, Packet Tracer labs, and real-world virtualized environments like GNS3/EVE-NG.
- **note** Cisco's IOS and Apple's iOS are not the same xD
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

## 2 - Essential Device Initialization

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

## 3- Interface and Layer 2/3 Configuration

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

### Layer 2 (Switching and VLANs)
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

## 4 -  Fundamental Routing & Infrastructure Services

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

## 5. System Maintenance and State Preservation

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

## 6 - Verification and Troubleshooting stuff

These are the primary diagnostics tools. need to run these in Privileged EXEC mode (`#`). If inside a sub-config menu, prefix them with `do` (e.g., `do show ip interface brief`).

*   `show ip interface brief` / `sh ip int br`  
    Shows every port, its IP, status (physical layer link status), and protocol (data link layer line protocol).
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

## 7-  CLI Tips, Tricks & Efficiency Secrets

### the `do` escape work
If you are modifying a port inside `Core-Router-01(config-if)#` and want to check your interfaces, typing `show ip interface brief` returns an error. Prefix the command with `do` to run Privileged EXEC queries from any location without stepping backwards:
```text
Core-Router-01(config-if)# do show ip interface brief
```

### Navigating Terminal Interruption
If you forget to enter `logging synchronous` and a system notification breaks the command line mid-sentence:
*   Press **`Ctrl + L`** or **`Ctrl + R`** to force the CLI to redraw your active, unsubmitted line on a clean line below the system notification.

### Tab Auto-Completion & Question Mark Help
*   Type part of a command and press **`Tab`** to auto-complete it.
*   Type a command followed immediately by a **`?`** (no space) to see character extensions.
*   Type a command, enter a **space**, and then type a **`?`** to view all valid arguments or variables that must follow next.

### Aborting Runaway Commands
If you ping an invalid address or type an unrecognized word without `no ip domain-lookup` configured, the terminal will freeze while trying to broadcast a search query.
*   Press **`Ctrl + Shift + 6`** simultaneously to force-abort the runaway execution loop and regain command line control instantly.
---
# Cisco IOS Mastery: Advanced Tips, Tricks & Productivity Secrets

A compilation of advanced CLI shortcuts, navigation tricks, data filtering methods, and disaster-prevention techniques for Cisco IOS. Optimized for rapid CCNA labbing and real-world efficiency.

---

## 1. Ultimate Keyboard Shortcuts

Cisco IOS uses many classic Emacs-style keybindings. Memorizing these will keep your hands on the home row and drastically speed up your configuration velocity.

| Shortcut | Action | Practical Use Case |
| :--- | :--- | :--- |
| **`Tab`** | Auto-completes a command | Type `conf` and hit Tab to instantly get `configure`. |
| **`Ctrl + A`** | Moves cursor to the **beginning** of the line | Fast way to insert a missed `no` or `do` to a long command. |
| **`Ctrl + E`** | Moves cursor to the **end** of the line | Jump back to the end after fixing a typo at the start. |
| **`Ctrl + K`** | Deletes everything from the cursor to the **end** | Instantly clears an accidental long string of arguments. |
| **`Ctrl + W`** | Deletes the **word** to the left of the cursor | Quick backspacing without holding down the Backspace key. |
| **`Ctrl + U`** | Deletes the **entire line** | Completely clears the line to start fresh. |
| **`Up Arrow` / `Ctrl + P`** | Cycles backward through command history | Pulls up the last command you typed so you can tweak it. |
| **`Down Arrow` / `Ctrl + N`** | Cycles forward through command history | Moves back down your recent command list. |

---

## 2. Dealing with Terminal Disasters and Freezes

When you are deep in the zone, a frozen terminal or a broken command line can completely ruin your flow. Use these to maintain control.

### The Escape Hatch: `Ctrl + Shift + 6`
If you make a typo without `no ip domain-lookup` configured, or if you launch a runaway `ping` or `traceroute` that you need to kill, the CLI will lock up.
*   **Action:** Press **`Ctrl + Shift + 6`** simultaneously.
*   **Result:** Instantly aborts the execution loop and returns you to an active prompt.

### The Redraw Trick: `Ctrl + R` (or `Ctrl + L`)
If you forget to add `logging synchronous` to your lines, background system logs (syslog messages) will split your command mid-sentence:
```text
Router(config)# interface gigabitethernet 0/0
Router(config-if)# ip addr
*Oct  5 12:47:31.102: %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to up 192.168.1.1 255.255.255.0
```
*   **Problem:** You can't easily see what you typed or where your cursor is.
*   **Solution:** Press **`Ctrl + R`**. The router will cleanly redraw your current unsubmitted command line on a brand-new line right below the system message.

---

## 3. Advanced Configuration & Verification Tactics

### The `do` Escape Route
Normally, `show` commands only work in Privileged EXEC mode (`Router#`). Prefixing any command with **`do`** lets you run it from deep inside configuration hierarchies without backing out:
```text
Router(config-if-range)# do show ip interface brief
```
> **Note:** When using `do`, the CLI auto-complete feature (using `Tab` or `?`) is disabled for the rest of that command string in older IOS versions. You must type the show command fully and accurately.

### Interface Range Configuration
Instead of stepping into 10 different access ports one by one to configure your Floor 2 Office PCs, group them together using the `range` keyword to configure them simultaneously:
```text
Router(config)# interface range gigabitethernet 0/1 - 10
Router(config-if-range)# description Office_Wired_PCs
Router(config-if-range)# switchport mode access
Router(config-if-range)# switchport access vlan 20
```

---

## 4. Linux-Style Piping & Output Filtering

When verifying a real-world enterprise device, running `show running-config` will dump hundreds of lines of text. Cisco IOS includes a powerful built-in filtering engine using the pipe character (**`|`**), behaving just like `grep`, `less`, or `head` in Linux.

### `| include [regex]` (Equivalent to `grep`)
Filters the command output to display **only** the lines that match your keyword case-sensitively.
*   *Example:* View only the interfaces that are physically up:
    ```text
    Router# show ip interface brief | include up
    ```

### `| exclude [regex]` (Equivalent to `grep -v`)
Displays everything **except** the lines that contain the matching keyword.
*   *Example:* Clean up your interface list by hiding all unassigned or disconnected ports:
    ```text
    Router# show ip interface brief | exclude unassigned
    ```

### `| section [keyword]` (Unique to Cisco IOS)
Displays the entire functional block of code or configuration related to a specific feature. This is the fastest way to check a specific protocol setup.
*   *Example:* Look *only* at your DHCP pool configuration inside the massive running config:
    ```text
    Router# show running-config | section dhcp
    ```
*   *Example:* View your exact OSPF or EIGRP routing parameters:
    ```text
    Router# show running-config | section router
    ```

### `| begin [keyword]` (Equivalent to `less +/keyword`)
Dumps the entire output file but **skips directly** to the first line where the keyword is found.
*   *Example:* Skip straight down to the interface definitions inside the running config:
    ```text
    Router# show running-config | begin interface
    ```

---

## 5. Lab Design, Git Management Safety and tips

*   **Abbreviate Everything:** Cisco IOS only requires you to type enough unique characters to distinguish a command. You don't have to type `configure terminal`. Instead, type `conf t`. You don't need to type `show running-config`; type `sh run`. 
*   **The Golden Rule (`no`):** In Cisco IOS, you delete configurations by prepending the word `no` to the exact command you used to create it. If you typed `ip routing` to enable it, type `no ip routing` to turn it off. If you typed `ip address 10.1.1.1 255.255.255.0`, type `no ip address` to strip it away.
*   **Packet Tracer Auto-Save Reminder:** Because you are version-controlling your network designs in GitHub, always run `copy run start` (or `wr`) on your simulated routers *before* hitting save in Packet Tracer. Packet Tracer saves the state of the NVRAM file, meaning if you haven't written the RAM to NVRAM inside the router CLI, your configurations will be missing when you push/pull your repository files!
---

# Cisco IOS Architecture: VLANs, Trunking & Inter-VLAN Routing

This guide details the exact Layer 2 and Layer 3 commands required to segment networks using Virtual LANs (VLANs), route traffic between them, and establish trunk links between interconnected switches.

---

## 1. Local VLAN Creation & Port Assignment

By default, all switch ports belong to `VLAN 1` (The Native/Default VLAN). To secure and optimize your 3-floor network, you must create distinct broadcast domains and assign physical access ports to them.

### Step 1: Define the VLANs in the Switch Database
Run this on your main switches (`Floor 1 Switch`, `Floor 2 Switch`, `Floor 3 Switch`):
```text
Switch> enable
Switch# configure terminal

Switch(config)# vlan 10
Switch(config-vlan)# name Server_Room_Data
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name Office_Data
Switch(config-vlan)# vlan 30
Switch(config-vlan)# name Lab_Data
Switch(config-vlan)# exit
```

### Step 2: Assign Access Ports to Endpoints
Configure the individual ports connecting to target devices (like your Desktop PCs, Printers, and Access Points):
```text
! Configuring a single interface (e.g., Floor 2 Printer)
Switch(config)# interface gigabitethernet 0/1
Switch(config-if)# description Office_Network_Printer
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit

! Pro Tip: Use 'range' to configure multiple local PCs at once
Switch(config)# interface range gigabitethernet 0/2 - 10
Switch(config-if-range)# description Floor2_Office_Wired_PCs
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
```

---

## 2. Inter-Switch Connectivity (Trunking)

An **Access Port** can only carry traffic for *one* specific VLAN. To allow your `Floor 1 Switch`, `Floor 2 Switch`, and `Floor 3 Switch` to pass traffic for all floors across a single physical wire, you must configure the interconnecting links as **Trunk Ports** using the standard **IEEE 802.1Q** protocol.

### Configuring an Inter-Switch Link as a Trunk
Run this on both ends of the cables linking your switches together (e.g., the link between Floor 1 Switch and Floor 2 Switch):
```text
Switch(config)# interface gigabitethernet 0/24
Switch(config-if)# description Inter-Switch_Link_To_Next_Floor

! Required on older enterprise hardware before setting trunk mode:
! Switch(config-if)# switchport trunk encapsulation dot1q 

Switch(config-if)# switchport mode trunk
```

### Optional Security Hardening for Trunks
```text
! Limit the trunk to only carry traffic for your specific lab VLANs
Switch(config-if)# switchport trunk allowed vlan 10,20,30

! Change the native (untagged) VLAN away from the default VLAN 1
Switch(config-if)# switchport trunk native vlan 99
```

---

## 3. Inter-VLAN Routing: Router-on-a-Stick (RoaS)

Devices inside `VLAN 10` cannot talk to devices in `VLAN 20` without a Layer 3 device (a router) to pass packets between them. **Router-on-a-Stick** uses a single physical router interface split into virtual **Subinterfaces** to act as the Default Gateway for every individual VLAN.

### On the "Company Router":
Connect one physical router interface (e.g., `GigabitEthernet 0/1`) to a trunk port on your `Floor 1 Switch`.

```text
Router> enable
Router# configure terminal

! Step 1: Turn on the physical interface but DO NOT give it an IP address
Router(config)# interface gigabitethernet 0/1
Router(config-if)# description Trunk_Link_To_Internal_Switches
Router(config-if)# no shutdown
Router(config-if)# exit

! Step 2: Create Subinterface for VLAN 10 (Server Room)
Router(config)# interface gigabitethernet 0/1.10
Router(config-subif)# description Default_Gateway_VLAN_10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

! Step 3: Create Subinterface for VLAN 20 (Office Floor)
Router(config)# interface gigabitethernet 0/1.20
Router(config-subif)# description Default_Gateway_VLAN_20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit

! Step 4: Create Subinterface for VLAN 30 (Laboratory Floor)
Router(config)# interface gigabitethernet 0/1.30
Router(config-subif)# description Default_Gateway_VLAN_30
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.168.30.1 255.255.255.0
Router(config-subif)# exit
```

---

## 4. Verification & Validation Commands

Use these essential diagnostics tools to audit your Layer 2 switching architecture.

*   `show vlan brief` (or `sh vlan br`)  
    Confirms your custom VLAN IDs exist in the database and explicitly lists which physical access ports are bound to them.
*   `show interfaces trunk` (or `sh int trunk`)  
    Lists all active trunking links on the switch, verifies the encapsulation protocol (`802.1q`), and shows which VLANs are actively allowed to pass across the connection.
*   `show mac address-table` (or `sh mac addr`)  
    Displays the CAM table, letting you see exactly which physical switch port a device's hardware MAC address is sending frames through.
*   `show ip route` (or `sh ip route`)  
    Run this on the **Company Router**. You should see each subinterface listed as a `Directly Connected` (`C`) network. If they are missing, verify that the base physical interface is set to `no shutdown`.

---
# Cisco IOS Architecture: Device Discovery & Neighbor Mapping

This guide details how to use Layer 2 discovery protocols to dynamically map network topology, trace physical cable connections, and identify remote hardware interfaces directly from the CLI without physical access or diagram software.

---

## 1. Cisco Discovery Protocol (CDP)

**CDP** is a Cisco-proprietary Layer 2 protocol enabled by default on nearly all Cisco enterprise switches, routers, and IP phones. It broadcasts periodic announcements out of all active interfaces to share device identity, hardware platforms, and port capabilities with directly connected neighbors.

### Essential Discovery Commands
Run these commands from Privileged EXEC mode (`#`). If configuring an interface, prepend them with `do`.

*   `show cdp neighbors` (or `sh cdp nei`)  
    Displays a structured, scannable table listing all directly connected neighbors, local exit interfaces, remote intake interfaces, and hardware platforms.
*   `show cdp neighbors detail` (or `sh cdp nei det`)  
    Dumps comprehensive architectural profiles for each neighbor, including **configured IPv4/IPv6 management addresses**, native VLAN settings, and remote OS versions.
*   `show cdp interface`  
    Displays the operational status of CDP across specific local physical ports along with packet timers.

### Managing CDP State
CDP can be toggled globally across the entire chassis or selectively disabled on untrusted edge interfaces (such as ports facing public networks or end-user workstations) to prevent information disclosure.

```text
! Turn off CDP globally across the entire device
Core-Router(config)# no cdp run

! Re-enable CDP globally
Core-Router(config)# cdp run

! Disable CDP selectively on a specific interface (e.g., WAN port facing ISP)
Core-Router(config)# interface gigabitethernet 0/0
Core-Router(config-if)# no cdp enable
```

---

## 2. Reading the Neighbor Table

here is a specific command that acts like a "radar" to show you exactly which physical ports are connected to other devices, without you having to manually trace cables in the Packet Tracer workspace.

## What is CDP?
- CDP stands for Cisco Discovery Protocol. It is a proprietary Cisco tool enabled by default on almost all Cisco routers and switches.
- Every few seconds, Cisco devices broadcast a tiny message out of their physical ports saying: "Hi! My name is Router A, and this message is coming out of my GigabitEthernet0/1 port." If another Cisco device is on the other end of that cable, it listens to that message and builds a local map.

### How to Read the Output
If you log into your Company Router and run the command, you will see a clean table showing exactly who is plugged into what:
CompanyRouter# show cdp neighbors

Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone

Device ID        Local Intrfce     Holdtme    Capability   Platform    Port ID
ISPRouter        Gig 0/0           148        R            ISR4331     Gig 0/1
Floor1-MainSW    Gig 0/1           162        S I          WS-C2960    Fast 0/24

Here is how you decode that table to understand your connections:
- Device ID: The hostname of the device on the other end of the wire (ISPRouter, Floor1-MainSW).
- Local Intrfce: The physical port on the current router you are logged into (e.g., your Company Router is using its own Gig 0/0 to talk to the ISP).
- Capability: What kind of device it is (R for Router, S for Switch).
- Platform: The actual model number of the hardware (e.g., a Cisco 4331 Router or a 2960 Switch).
- Port ID: The physical port on the remote device that the cable plugs into (e.g., the cable lands on Gig 0/1 on the ISP Router).

#### Quick Tips for Lab
- The "Too Much Information" Escape: If you want to see the specific IP address of the neighboring router without logging into it, type ` show cdp neighbors detail`. It will print out a breakdown including the remote interface's configured IP.
- What if it's not a Cisco device? If you eventually test this in the virt-manager lab with mixed vendors (like an open-source VyOS router or a Linux server), CDP won't work because it's Cisco-only. Instead, use the open industry standard command: `show lldp neighbors` (Link Layer Discovery Protocol). It produces the exact same style of table.

That command is `show cdp neighbors` (and its detailed version, show cdp neighbors detail).
When you execute `show cdp neighbors`, the CLI outputs a matrix. Understanding how to cross-reference this table is critical for tracing connectivity:

```text
Device ID        Local Intrfce     Holdtme    Capability   Platform    Port ID
ISP-Router       Gig 0/0           148        R            ISR4331     Gig 0/1
Floor1-MainSW    Gig 0/1           162        S I          WS-C2960    Fast 0/24
```

### Column Breakdown:
1.  **Device ID:** The configured hostname of the remote device sitting on the other end of the cable.
2.  **Local Intrfce:** The physical port on the **current device you are logged into**. This is where your packet exits.
3.  **Holdtme:** A countdown timer (in seconds) representing how long the device will cache this neighbor's data if no new announcements arrive. It refreshes automatically.
4.  **Capability:** The functional role of the remote machine (`R` = Router, `S` = Switch, `H` = Host/PC, `P` = IP Phone).
5.  **Platform:** The physical hardware chassis type or model number of the remote machine.
6.  **Port ID:** The exact physical port on the **remote device** where the cable lands.

---

## 3. Link Layer Discovery Protocol (LLDP)

If you migrate your labs out of Packet Tracer into open virtual environments (like `virt-manager` using GNS3 or EVE-NG) and mix Cisco nodes with alternative operating systems (Linux servers, firewalls, open-source routers), CDP will fail because it is proprietary. 

To bridge this gap, use **LLDP (IEEE 802.1AB)**, which is the open vendor-neutral industry standard. It behaves almost identically to CDP but must be enabled manually on many Cisco devices.

### Enabling & Verifying LLDP
```text
! Step 1: Turn on LLDP globally across the device
Core-Router(config)# lldp run

! Step 2: Verify neighbors using open-standard syntax
Core-Router# show lldp neighbors
```

### Granular Port Control for LLDP
Unlike CDP, LLDP allows you to granularly control whether an interface only transmits discovery info or only receives it:
```text
Core-Router(config)# interface gigabitethernet 0/2
Core-Router(config-if)# no lldp transmit     ! Stop telling this port who we are
Core-Router(config-if)# no lldp receive      ! Stop listening for neighbors on this port
```

---

## 4. Operational Tips & Shortcuts

*   **Finding Secret IPs:** If you inherit a network lab with zero documentation and don't know what IP address to use to SSH into the next switch, run `show cdp neighbors detail`. The router will show you the exact management IP address of the neighboring box.
*   **Layer 2 Dependency:** Because CDP and LLDP run directly inside the Data Link Layer (Layer 2), **they do not require IP addresses to function**. Two switches with blank, unconfigured configurations will still see each other perfectly via discovery tables the moment you plug a cable between them.
---
