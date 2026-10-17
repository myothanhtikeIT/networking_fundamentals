# Subnetting Study Guide (CCNA 200-301)

A practical, no-fluff guide to IPv4 and IPv6 addressing for the CCNA: binary and masks, the block-size trick, real troubleshooting, splitting networks, VLSM, wildcard masks, route summarization, IPv6 basics, Cisco IOS commands, and 65 practice problems with answers.

**How to use this guide**

1. Read one lesson.
2. Write the [cheat sheet](#cheat-sheet) from memory on paper.
3. Do the matching [practice problems](#practice-problems) with the answers covered.
4. Follow the [daily drill routine](#daily-drill-routine). Reps matter far more than reading.

## Table of Contents

- [Study Roadmap](#study-roadmap)
- [Cheat Sheet](#cheat-sheet)
- [Lesson 1: Binary and Subnet Masks](#lesson-1-binary-and-subnet-masks)
- [Lesson 2: The Block-Size Trick](#lesson-2-the-block-size-trick)
- [Lesson 3: Subnetting in Real Troubleshooting](#lesson-3-subnetting-in-real-troubleshooting)
- [Lesson 4: Splitting a Network into Equal Subnets](#lesson-4-splitting-a-network-into-equal-subnets)
- [Lesson 5: Masks Beyond the Last Octet](#lesson-5-masks-beyond-the-last-octet)
- [Lesson 6: VLSM](#lesson-6-vlsm)
- [Lesson 7: Wildcard Masks](#lesson-7-wildcard-masks)
- [Lesson 8: Route Summarization](#lesson-8-route-summarization)
- [Lesson 9: IPv6 Addressing and Subnetting](#lesson-9-ipv6-addressing-and-subnetting)
- [Cisco IOS Quick Reference](#cisco-ios-quick-reference)
- [Common Mistakes](#common-mistakes)
- [Daily Drill Routine](#daily-drill-routine)
- [Practice Problems](#practice-problems)

## Study Roadmap

- [ ] Lesson 1: Binary and masks
- [ ] Lesson 2: Block-size trick
- [ ] Lesson 3: Troubleshooting rules
- [ ] Lesson 4: Equal subnets (borrowing bits)
- [ ] Lesson 5: Masks beyond the last octet
- [ ] Lesson 6: VLSM (subnets of different sizes)
- [ ] Lesson 7: Wildcard masks (ACLs, OSPF)
- [ ] Lesson 8: Route summarization
- [ ] Lesson 9: IPv6 addressing and subnetting
- [ ] Practice problems 1 to 65 completed
- [ ] Can find network, broadcast and range in under 30 seconds

## Cheat Sheet

**Memorize this. Everything else builds on it.**

### Mask octet values

Only eight values are possible in a mask octet:

| Ones | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|
| Value | 128 | 192 | 224 | 240 | 248 | 252 | 254 | 255 |

### Last-octet masks

| Prefix | Mask | Wildcard | Block size | Total addresses | Usable hosts |
|---|---|---|---|---|---|
| /24 | 255.255.255.0 | 0.0.0.255 | 256 | 256 | 254 |
| /25 | 255.255.255.128 | 0.0.0.127 | 128 | 128 | 126 |
| /26 | 255.255.255.192 | 0.0.0.63 | 64 | 64 | 62 |
| /27 | 255.255.255.224 | 0.0.0.31 | 32 | 32 | 30 |
| /28 | 255.255.255.240 | 0.0.0.15 | 16 | 16 | 14 |
| /29 | 255.255.255.248 | 0.0.0.7 | 8 | 8 | 6 |
| /30 | 255.255.255.252 | 0.0.0.3 | 4 | 4 | 2 |

Block size = 256 - mask octet. Usable hosts = block size - 2. Wildcard octet = block size - 1.

### Masks that cross into the third octet

The block size here is in the **third octet**.

| Prefix | Mask | Wildcard | Block size (3rd octet) | Usable hosts |
|---|---|---|---|---|
| /23 | 255.255.254.0 | 0.0.1.255 | 2 | 510 |
| /22 | 255.255.252.0 | 0.0.3.255 | 4 | 1,022 |
| /21 | 255.255.248.0 | 0.0.7.255 | 8 | 2,046 |
| /20 | 255.255.240.0 | 0.0.15.255 | 16 | 4,094 |
| /19 | 255.255.224.0 | 0.0.31.255 | 32 | 8,190 |
| /18 | 255.255.192.0 | 0.0.63.255 | 64 | 16,382 |
| /17 | 255.255.128.0 | 0.0.127.255 | 128 | 32,766 |
| /16 | 255.255.0.0 | 0.0.255.255 | (whole octet) | 65,534 |

### Host sizing

| Hosts needed | Smallest prefix |
|---|---|
| up to 2 | /30 |
| up to 6 | /29 |
| up to 14 | /28 |
| up to 30 | /27 |
| up to 62 | /26 |
| up to 126 | /25 |
| up to 254 | /24 |
| up to 510 | /23 |
| up to 1,022 | /22 |

### Special addresses

| Range | Meaning |
|---|---|
| 127.0.0.0/8 | Loopback (`127.0.0.1` = this host) |
| 169.254.0.0/16 | APIPA: the host asked for DHCP and got no answer |
| 10.0.0.0/8 | Private (RFC 1918) |
| 172.16.0.0/12 | Private (RFC 1918), 172.16.0.0 to 172.31.255.255 |
| 192.168.0.0/16 | Private (RFC 1918) |
| 224.0.0.0/4 | Multicast |
| 255.255.255.255 | Limited broadcast |
| 0.0.0.0/0 | Default route ("any address") |

### Classful ranges (for reference)

| Class | First octet | Default mask |
|---|---|---|
| A | 1 to 126 | /8 |
| B | 128 to 191 | /16 |
| C | 192 to 223 | /24 |
| D | 224 to 239 | Multicast |
| E | 240 to 255 | Reserved |

### Powers of 2 (memorize up to 2^12)

| 2^1 | 2^2 | 2^3 | 2^4 | 2^5 | 2^6 | 2^7 | 2^8 | 2^9 | 2^10 | 2^11 | 2^12 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 2 | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,096 |

## Lesson 1: Binary and Subnet Masks

### An IPv4 address is 32 bits

It's written as **4 octets** of **8 bits** each (4 x 8 = 32). Each octet is a decimal number from 0 to 255.

```
192.168.10.5
 |   |   |  |
 4 octets, 8 bits each
```

### Binary to decimal

Each octet has 8 positions with fixed values:

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---|---|---|---|---|---|---|---|

A `1` means "add this value". A `0` means "skip it".

**Always write the place values above the bits first, then add the columns that have a 1.**

```
128  64  32  16   8   4   2   1
  1   1   1   0   0   0   0   0   ->  128+64+32 = 224
  0   0   0   0   1   0   1   0   ->  8+2        = 10
  1   0   1   0   1   1   0   0   ->  128+32+8+4 = 172
```

### Decimal to binary

Go left to right. For each place value, ask: does it fit into what's left?

Example: 40
- 128? No. 64? No. 32? Yes (8 left). 16? No. 8? Yes (0 left). 4, 2, 1? No.
- Result: `00101000`

### What a subnet mask is

A mask is 32 bits: some 1s followed by all 0s.

- The **1s** mark the **network part** of the address.
- The **0s** mark the **host part**.

`/24` means "24 ones" = `255.255.255.0`.

### Host bits

```
host bits       = 32 - prefix
total addresses = 2^(host bits)
usable hosts    = 2^(host bits) - 2     (minus the network and broadcast addresses)
```

Example: `/26` -> 32 - 26 = 6 host bits -> 2^6 = 64 addresses -> **62 usable hosts**.

### Mask to prefix

Count the ones. `255.255.255.224`: 24 ones from the three 255s, plus 3 ones from 224 (`11100000`) = **/27**.

## Lesson 2: The Block-Size Trick

You don't need binary to find a subnet's details. You only need the block size.

1. Find the **interesting octet**: the one in the mask that isn't 255 or 0.
2. **Block size** = 256 - that mask octet.
3. Subnets start at multiples of the block size.

For a given host:

| Value | How to find it |
|---|---|
| Network address | Start of the block the host falls in |
| Broadcast address | Start of the next block - 1 |
| Usable range | Network + 1 to broadcast - 1 |

### Example: `192.168.10.77/26`

- Mask 255.255.255.192, so the block size is 256 - 192 = 64.
- Blocks: 0, 64, 128, 192. Host .77 falls in the 64 block.
- **Network:** 192.168.10.64
- **Broadcast:** 192.168.10.127 (next block starts at 128)
- **Usable range:** 192.168.10.65 to 192.168.10.126

### The last block

For the last block in an octet, the "next block" starts at 256, so the broadcast ends in `.255`.

Example: `172.16.50.200/26` -> block 192 to 255 -> network `172.16.50.192`, broadcast `172.16.50.255`.

## Lesson 3: Subnetting in Real Troubleshooting

### The core rule

A host decides "local or not?" using **its own IP and its own mask**.

- **Same subnet:** the host ARPs for the destination and sends directly.
- **Different subnet:** the host sends the packet to its **default gateway**, which must be a router **inside the host's own subnet**.

### "A can't reach B" checklist

1. **Same subnet?** Use the block-size trick on both IPs. If not, traffic must go through a router.
2. **Is the default gateway in the host's own subnet?** If not, the host reaches local devices but nothing outside.
3. **Is the gateway a valid host address?** It can't be the network address or the broadcast address.
4. **Do the masks match?** If one side has /24 and the other /26, they disagree about who is local, which causes odd one-way problems.
5. **Does the host show `169.254.x.x`?** That's APIPA: DHCP failed (check the cable, VLAN, DHCP server, or relay with `ip helper-address`).
6. **Duplicate IP?** Two devices with the same address cause intermittent failures.

### Worked examples

**Gateway in the wrong subnet.** PC `192.168.10.70/26`, gateway `192.168.10.1`. The PC is in the .64 to .127 block. The gateway is in the .0 to .63 block. The PC can't reach its gateway, so it has no way out.

**Gateway set to the broadcast address.** Host `192.168.1.20/26`, gateway `192.168.1.63`. In `192.168.1.0/26`, .63 is the broadcast address and can't be assigned to a device. Use something like `.1`.

**Same subnet or not?** Both hosts use /28 (blocks of 16). `10.0.0.50` and `10.0.0.60` are both in the 48 to 63 block, so they're in the same subnet. `10.0.0.15` and `10.0.0.17` are **not**: the boundary at 16 splits them.

### Sizing subnets

Pick the smallest subnet where `usable hosts >= hosts needed`.

- 40 hosts -> /26 (62 usable). A /27 only has 30.
- 100 hosts -> /25 (126 usable).
- 25 hosts -> /27 (30 usable).
- Link between two routers -> **/30** (exactly 2 usable). Modern equipment can also use /31, but the CCNA expects /30.

## Lesson 4: Splitting a Network into Equal Subnets

You get more subnets by **borrowing bits from the host part**. Each bit borrowed **doubles the subnets and halves the hosts per subnet**.

**Procedure**

1. Find `b`, the smallest number where 2^b >= the number of subnets you need.
2. New prefix = old prefix + `b`.
3. Get the block size from the cheat sheet.
4. List the subnets by counting up in blocks.

### Example: 4 subnets from `192.168.1.0/24`

- 2^2 = 4, so borrow 2 bits -> **/26**, block size 64.
- Subnets:

| # | Subnet | Usable range | Broadcast |
|---|---|---|---|
| 1 | 192.168.1.0/26 | .1 to .62 | .63 |
| 2 | 192.168.1.64/26 | .65 to .126 | .127 |
| 3 | 192.168.1.128/26 | .129 to .190 | .191 |
| 4 | 192.168.1.192/26 | .193 to .254 | .255 |

### Example: 6 subnets

2^2 = 4 is too few. 2^3 = 8 works, so borrow 3 bits -> **/27**, block size 32 -> 8 subnets of 30 hosts each (two are spare).

### Going the other way: how many subnets does a prefix give?

Number of subnets = 2^(new prefix - old prefix).

Example: splitting a /16 into /20s gives 2^(20 - 16) = 2^4 = **16 subnets**.

## Lesson 5: Masks Beyond the Last Octet

Same trick, applied to the **interesting octet** (the one that isn't 255 or 0).

- Octets **before** it: copy them from the host address.
- The interesting octet: use the block trick.
- Octets **after** it: `0` for the network address, `255` for the broadcast address.

### Example: `172.16.35.10/20`

- Mask 255.255.**240**.0 -> the interesting octet is the 3rd, block size 16.
- 35 falls in the 32 to 47 block.
- **Network:** 172.16.32.0
- **Broadcast:** 172.16.47.255
- **Usable range:** 172.16.32.1 to 172.16.47.254

### Example: `10.1.5.9/22`

- Mask 255.255.**252**.0 -> block size 4 in the 3rd octet.
- 5 falls in the 4 to 7 block.
- **Network:** 10.1.4.0
- **Broadcast:** 10.1.7.255
- **Usable range:** 10.1.4.1 to 10.1.7.254

### Example: `10.200.3.3/17`

- Mask 255.255.**128**.0 -> block size 128 in the 3rd octet.
- 3 falls in the 0 to 127 block.
- **Network:** 10.200.0.0
- **Broadcast:** 10.200.127.255

## Lesson 6: VLSM

Real networks don't need equal-sized subnets. A 100-host LAN, a 20-host LAN and a router link all need different sizes. **VLSM** (variable-length subnet masking) gives each one just what it needs.

### Rules

1. Sort the requirements **largest to smallest**.
2. Give each the smallest prefix that fits (use the host-sizing table).
3. Start each subnet where the previous one ended. Each subnet must begin at a multiple of its own block size, which is why you go **largest first**.
4. Check at the end that the subnets don't overlap and everything fits.

### Example 1

Split `192.168.1.0/24` for LAN A (100 hosts), LAN B (50), LAN C (20) and two router links.

| Need | Prefix | Subnet | Range |
|---|---|---|---|
| LAN A, 100 hosts | /25 | 192.168.1.0/25 | .0 to .127 |
| LAN B, 50 hosts | /26 | 192.168.1.128/26 | .128 to .191 |
| LAN C, 20 hosts | /27 | 192.168.1.192/27 | .192 to .223 |
| Link 1 | /30 | 192.168.1.224/30 | .224 to .227 |
| Link 2 | /30 | 192.168.1.228/30 | .228 to .231 |

Addresses .232 to .255 are still free for growth.

### Example 2

Split `192.168.5.0/24` for 60, 25 and 10 hosts, plus one router link.

| Need | Prefix | Subnet | Range |
|---|---|---|---|
| 60 hosts | /26 | 192.168.5.0/26 | .0 to .63 |
| 25 hosts | /27 | 192.168.5.64/27 | .64 to .95 |
| 10 hosts | /28 | 192.168.5.96/28 | .96 to .111 |
| Link | /30 | 192.168.5.112/30 | .112 to .115 |

### Spotting overlaps

Write the range of each subnet and check for shared addresses.

`192.168.1.0/25` covers .0 to .127. `192.168.1.64/26` covers .64 to .127. They **overlap**, which is a design mistake.

## Lesson 7: Wildcard Masks

Wildcard masks are used in **ACLs** and **OSPF `network` statements**. They look like subnet masks flipped upside down.

- A **0 bit** means "this bit must match".
- A **1 bit** means "don't care".

### How to calculate

```
wildcard = 255.255.255.255 - subnet mask
```

| Prefix | Subnet mask | Wildcard |
|---|---|---|
| /24 | 255.255.255.0 | 0.0.0.255 |
| /26 | 255.255.255.192 | 0.0.0.63 |
| /27 | 255.255.255.224 | 0.0.0.31 |
| /22 | 255.255.252.0 | 0.0.3.255 |
| /20 | 255.255.240.0 | 0.0.15.255 |

Shortcut: **the wildcard octet = block size - 1**, and fully matched octets are `0`.

### Matching a subnet

Use the **network address** plus the wildcard.

`192.168.10.64 0.0.0.63` matches **192.168.10.64 to 192.168.10.127** (the whole /26).

`172.16.8.0 0.0.3.255` matches **172.16.8.0 to 172.16.11.255** (the 3rd octet 8 to 11, which is a /22).

### Special keywords

| Keyword | Equals | Meaning |
|---|---|---|
| `host 10.1.1.1` | `10.1.1.1 0.0.0.0` | Exactly one address |
| `any` | `0.0.0.0 255.255.255.255` | Every address |

### Where you use them

```
! Standard ACL: permit one /26
access-list 10 permit 192.168.10.64 0.0.0.63

! OSPF: advertise interfaces that fall in 10.1.4.0/22
router ospf 1
 network 10.1.4.0 0.0.3.255 area 0
```

## Lesson 8: Route Summarization

Summarization (route aggregation) replaces several contiguous routes with **one shorter-prefix route**. It makes routing tables smaller and more stable.

### Method

1. List the network addresses and find the **lowest network** and the **highest broadcast** among them.
2. Find the smallest block size that covers that whole span.
3. The summary must **start at a multiple of its block size**. If it doesn't line up, you can't use one summary.
4. Use that block size to get the mask. In the 3rd octet: 2 -> /23, 4 -> /22, 8 -> /21, 16 -> /20, 32 -> /19.

### Example 1

`192.168.0.0/24`, `192.168.1.0/24`, `192.168.2.0/24`, `192.168.3.0/24`

- Span: 192.168.0.0 to 192.168.3.255 -> 4 blocks of 256 -> block size 4 in the 3rd octet.
- Starts at 0 (a multiple of 4). Mask 255.255.252.0.
- **Summary: 192.168.0.0/22**

### Example 2

`10.1.8.0/24` through `10.1.15.0/24` (8 subnets)

- Block size 8 in the 3rd octet, starting at 8 (a multiple of 8).
- **Summary: 10.1.8.0/21**

### Example 3: misaligned, so no single summary

`192.168.1.0/24` through `192.168.4.0/24` (4 subnets)

- A /22 would cover 0 to 3, which includes .0 (not yours) and misses .4.
- Best you can do: `192.168.1.0/24`, `192.168.2.0/23`, `192.168.4.0/24`.

### Rules to remember

- Only summarize subnets you actually own. Over-summarizing can attract traffic for addresses you don't have.
- A router still picks the **longest prefix match** first.
- `0.0.0.0/0` (the default route) is the ultimate summary.

## Lesson 9: IPv6 Addressing and Subnetting

### Basics

- **128 bits**, written as **8 groups of 4 hex digits**, separated by colons.
- Hex digits: 0 to 9 and a to f.
- No broadcast. IPv6 uses **multicast** and **NDP** (Neighbor Discovery, over ICMPv6) instead of ARP.

### Abbreviation rules

1. Drop **leading zeros** in each group.
2. Replace **one** run of consecutive all-zero groups with `::`. Use it **only once** per address. If there are two runs, compress the longest.

```
2001:0db8:0000:0000:0000:0000:0000:0001  ->  2001:db8::1
2001:0db8:0001:0000:0000:0ab9:c0a8:0102  ->  2001:db8:1::ab9:c0a8:102
2001:0db8:0000:0010:0000:0000:0000:0001  ->  2001:db8:0:10::1
```

### Expanding

Fill `::` with enough zero groups to reach 8 groups, then restore leading zeros.

```
fe80::a:1  ->  fe80:0000:0000:0000:0000:0000:000a:0001
```

### Address structure

```
|<--- 48 bits --->|<- 16 bits ->|<------ 64 bits ------>|
| Global routing  | Subnet ID   | Interface ID          |
| prefix          |             |                       |
|<------------ /64 network ---->|
```

- Standard LAN subnets are **/64**.
- To subnet, change the **Subnet ID** (the 4th group).
- A /48 gives 2^16 = **65,536** /64 subnets.

Example: from `2001:db8:acad::/48`, the first subnets are `2001:db8:acad:1::/64`, `2001:db8:acad:2::/64`, `2001:db8:acad:3::/64`, and so on.

### Address types

| Type | Prefix | Notes |
|---|---|---|
| Global unicast | `2000::/3` | Public and routable |
| Unique local | `FC00::/7` (in practice `FD00::/8`) | Private, like RFC 1918 |
| Link-local | `FE80::/10` | Not routable, every interface has one |
| Multicast | `FF00::/8` | `ff02::1` = all nodes, `ff02::2` = all routers |
| Anycast | (a unicast address on several devices) | Nearest device answers |
| Loopback | `::1/128` | This host |
| Unspecified | `::/128` | "No address yet" |
| Default route | `::/0` | Like 0.0.0.0/0 |
| Documentation | `2001:db8::/32` | Used in examples |

### EUI-64 (building the interface ID from a MAC)

1. Split the 48-bit MAC in half.
2. Insert `FFFE` in the middle.
3. Flip the 7th bit of the first byte (the U/L bit).

```
MAC           00:1A:2B:3C:4D:5E
Split + FFFE  00:1A:2B : FF:FE : 3C:4D:5E
Flip 7th bit  00 = 00000000  ->  00000010 = 02
Interface ID  021A:2BFF:FE3C:4D5E
```

### How hosts get addresses

- **SLAAC:** the host takes the prefix from a router advertisement and builds its own interface ID.
- **DHCPv6:** stateful (assigns addresses) or stateless (gives extra info like DNS only).

## Cisco IOS Quick Reference

### IPv4 interface and routes

```
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.192
Router(config-if)# no shutdown

Router(config)# ip route 10.1.4.0 255.255.252.0 192.168.10.2
Router(config)# ip route 0.0.0.0 0.0.0.0 192.168.10.2
```

### ACL with a wildcard mask

```
Router(config)# access-list 10 permit 192.168.10.64 0.0.0.63
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip access-group 10 out
```

### OSPF network statement

```
Router(config)# router ospf 1
Router(config-router)# network 192.168.10.64 0.0.0.63 area 0
```

### IPv6

```
Router(config)# ipv6 unicast-routing
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 address 2001:db8:acad:1::1/64
Router(config-if)# ipv6 address fe80::1 link-local
Router(config-if)# ipv6 address 2001:db8:acad:2::/64 eui-64
Router(config-if)# no shutdown
```

### Verification

```
show ip interface brief
show ip route
show running-config
show ipv6 interface brief
ping <address>
traceroute <address>
```

## Common Mistakes

| Mistake | Fix |
|---|---|
| Treating 8 bits as the whole address | 8 bits = one octet. The address is 4 octets = 32 bits. |
| Skipping a 1 when adding binary | Write the place values above the bits and check every column. |
| Misaligned place values (10 as `00010100`) | `00010100` is 20. Write 128 64 32 16 8 4 2 1 first. |
| Host bits = prefix - 24 | Host bits = **32 - prefix**. |
| Forgetting the -2 | Usable = total - 2 (network and broadcast). |
| "Close numbers = same subnet" | Only the block boundaries matter (.15 and .17 differ in a /28). |
| Choosing a subnet by total addresses | Compare with **usable** hosts (a /27 holds 30, not 32). |
| Gateway outside the host's subnet | The gateway must be in the same subnet and not the network or broadcast address. |
| Confusing 169.254 with loopback | Loopback is 127.0.0.0/8. 169.254.0.0/16 is APIPA. |
| Using the subnet mask in an ACL or OSPF `network` line | Those use the **wildcard** mask. |
| Summarizing subnets that aren't aligned | The summary must start at a multiple of its block size. |
| VLSM starting with the smallest subnet | Always allocate largest first. |
| Using `::` twice in an IPv6 address | Only once. Compress the longest zero run. |
| Forgetting to flip the 7th bit in EUI-64 | `00` becomes `02`. |

## Daily Drill Routine

About 30 minutes a day. Short and consistent beats long and rare.

1. **Cheat sheet from memory (5 min).** Write the mask table, block sizes and usable hosts on paper. Check it, fix gaps.
2. **Rapid fire (10 min).** Do 10 problems from the practice set (rotate levels). Write every step.
3. **Weak spots (10 min).** Redo yesterday's misses and one problem type you keep getting wrong.
4. **Teach it back (5 min).** Explain one lesson out loud in your own words. If you can't, reread it.

**Targets**

- Week 1: correct answers, any speed.
- Week 2: network, broadcast and range in **under 60 seconds**.
- Week 3 and beyond: under **30 seconds**, plus VLSM plans in 2 to 3 minutes.

Mix older topics into every session so they don't fade.

## Practice Problems

Cover the answers and work each one on paper.

### Level 1: Binary and basics

**1.** Convert `11000000` to decimal.

<details>
<summary>Answer</summary>

128 + 64 = **192**.
</details>

**2.** Convert `11111000` to decimal.

<details>
<summary>Answer</summary>

128 + 64 + 32 + 16 + 8 = **248**.
</details>

**3.** Convert `00001111` to decimal.

<details>
<summary>Answer</summary>

8 + 4 + 2 + 1 = **15**.
</details>

**4.** Convert 100 to 8-bit binary.

<details>
<summary>Answer</summary>

100 = 64 + 32 + 4 -> **01100100**.
</details>

**5.** What prefix length is `255.255.255.248`?

<details>
<summary>Answer</summary>

24 + 5 ones (248 = `11111000`) = **/29**.
</details>

**6.** Write /20 as a dotted-decimal mask.

<details>
<summary>Answer</summary>

16 ones + 4 ones in the 3rd octet (240) -> **255.255.240.0**.
</details>

**7.** How many host bits and usable hosts are in a /26?

<details>
<summary>Answer</summary>

32 - 26 = **6 host bits**. 2^6 - 2 = **62** usable hosts.
</details>

**8.** How many usable hosts are in a /22?

<details>
<summary>Answer</summary>

32 - 22 = 10 host bits. 2^10 - 2 = **1,022**.
</details>

### Level 2: Network, broadcast and range (last octet)

**9.** Find the network, broadcast and usable range for `192.168.1.100/27`.

<details>
<summary>Answer</summary>

Block size 32. Host in the 96 block. Network **192.168.1.96**, broadcast **192.168.1.127**, usable **.97 to .126**.
</details>

**10.** Find the network, broadcast and usable range for `10.0.0.45/28`.

<details>
<summary>Answer</summary>

Block size 16. Host in the 32 block. Network **10.0.0.32**, broadcast **10.0.0.47**, usable **.33 to .46**.
</details>

**11.** Find the network, broadcast and usable range for `172.16.8.200/26`.

<details>
<summary>Answer</summary>

Block size 64. Host in the 192 block. Network **172.16.8.192**, broadcast **172.16.8.255**, usable **.193 to .254**.
</details>

**12.** Find the network, broadcast and usable range for `192.168.50.5/29`.

<details>
<summary>Answer</summary>

Block size 8. Host in the 0 block. Network **192.168.50.0**, broadcast **192.168.50.7**, usable **.1 to .6**.
</details>

**13.** Find the network, broadcast and usable range for `192.168.7.130/25`.

<details>
<summary>Answer</summary>

Block size 128. Host in the 128 block. Network **192.168.7.128**, broadcast **192.168.7.255**, usable **.129 to .254**.
</details>

**14.** Find the network, broadcast and usable range for `10.10.10.77/30`.

<details>
<summary>Answer</summary>

Block size 4. Host in the 76 block. Network **10.10.10.76**, broadcast **10.10.10.79**, usable **.77 and .78**.
</details>

**15.** Find the network, broadcast and usable range for `200.1.1.250/27`.

<details>
<summary>Answer</summary>

Block size 32. Host in the 224 block. Network **200.1.1.224**, broadcast **200.1.1.255**, usable **.225 to .254**.
</details>

**16.** Is `192.168.3.63/26` a valid host address?

<details>
<summary>Answer</summary>

**No.** Blocks of 64: the first block is 0 to 63, so .63 is the broadcast address.
</details>

**17.** Is `10.0.0.16/28` a valid host address?

<details>
<summary>Answer</summary>

**No.** Blocks of 16: .16 is the first address of the 16 to 31 block, so it's the network address.
</details>

**18.** Is `192.168.3.62/26` a valid host address?

<details>
<summary>Answer</summary>

**Yes.** It's the last usable address of the 0 to 63 block (broadcast is .63).
</details>

### Level 3: Masks beyond the last octet

**19.** Find the network and broadcast for `172.16.35.10/20`.

<details>
<summary>Answer</summary>

Block size 16 in the 3rd octet. 35 is in the 32 block. Network **172.16.32.0**, broadcast **172.16.47.255**.
</details>

**20.** Find the network and broadcast for `10.1.5.9/22`.

<details>
<summary>Answer</summary>

Block size 4 in the 3rd octet. 5 is in the 4 block. Network **10.1.4.0**, broadcast **10.1.7.255**, usable **10.1.4.1 to 10.1.7.254**.
</details>

**21.** Find the network and broadcast for `192.168.130.5/23`.

<details>
<summary>Answer</summary>

Block size 2 in the 3rd octet. 130 is in the 130 to 131 block. Network **192.168.130.0**, broadcast **192.168.131.255**.
</details>

**22.** Find the network and broadcast for `172.20.100.1/19`.

<details>
<summary>Answer</summary>

Block size 32 in the 3rd octet. 100 is in the 96 to 127 block. Network **172.20.96.0**, broadcast **172.20.127.255**.
</details>

**23.** Find the network and broadcast for `10.50.77.8/21`.

<details>
<summary>Answer</summary>

Block size 8 in the 3rd octet. 77 is in the 72 to 79 block. Network **10.50.72.0**, broadcast **10.50.79.255**.
</details>

**24.** Find the network and broadcast for `172.31.200.9/18`.

<details>
<summary>Answer</summary>

Block size 64 in the 3rd octet. 200 is in the 192 to 255 block. Network **172.31.192.0**, broadcast **172.31.255.255**.
</details>

**25.** Find the network and broadcast for `10.200.3.3/17`.

<details>
<summary>Answer</summary>

Block size 128 in the 3rd octet. 3 is in the 0 to 127 block. Network **10.200.0.0**, broadcast **10.200.127.255**.
</details>

**26.** How many usable hosts are in a /21?

<details>
<summary>Answer</summary>

32 - 21 = 11 host bits. 2^11 - 2 = **2,046**.
</details>

### Level 4: Same subnet and troubleshooting

**27.** Both hosts use /27. Are `192.168.1.30` and `192.168.1.33` in the same subnet?

<details>
<summary>Answer</summary>

**No.** .30 is in the 0 to 31 block and .33 is in the 32 to 63 block.
</details>

**28.** Both hosts use /26. Are `10.0.0.70` and `10.0.0.120` in the same subnet?

<details>
<summary>Answer</summary>

**Yes.** Both are in the 64 to 127 block.
</details>

**29.** A PC is `172.16.5.200/25` with gateway `172.16.5.1`. What's wrong?

<details>
<summary>Answer</summary>

The PC is in the 128 to 255 block, but the gateway is in the 0 to 127 block. The **gateway is in a different subnet** from the PC.
</details>

**30.** A host is `192.168.10.80/27` and its gateway is `192.168.10.95`. What's wrong?

<details>
<summary>Answer</summary>

The subnet is 64 to 95, so **.95 is the broadcast address** and can't be used as a gateway.
</details>

**31.** PC A is `192.168.1.10/24` and PC B is `192.168.1.200/26`. What's the problem?

<details>
<summary>Answer</summary>

The **masks don't match**. A thinks B is local (same /24) and ARPs directly. B thinks A (.10) is outside its own subnet (192 to 255), so it sends replies to its default gateway instead. Traffic becomes asymmetric and unreliable.
</details>

**32.** A host shows `169.254.77.12`. What does that mean?

<details>
<summary>Answer</summary>

APIPA: the host **failed to get an address from DHCP**. Check the cable, VLAN, DHCP server or relay.
</details>

**33.** Two routers are connected using `10.0.0.4/30`. Which two addresses can you assign to them?

<details>
<summary>Answer</summary>

The block is 4 to 7. Network .4, broadcast .7, so use **10.0.0.5 and 10.0.0.6**.
</details>

**34.** How many /30 subnets fit in a /24?

<details>
<summary>Answer</summary>

256 / 4 = **64**.
</details>

### Level 5: Sizing and splitting

**35.** Split `192.168.1.0/24` into 8 equal subnets. What prefix, and what are the subnets?

<details>
<summary>Answer</summary>

Borrow 3 bits -> **/27**, block size 32. Subnets: `.0`, `.32`, `.64`, `.96`, `.128`, `.160`, `.192`, `.224` (all /27), 30 hosts each.
</details>

**36.** You need 30 subnets from a /24. What prefix, and how many hosts per subnet?

<details>
<summary>Answer</summary>

2^5 = 32 >= 30, so borrow 5 bits -> **/29**. Each has 8 addresses, **6 usable hosts**.
</details>

**37.** Split `10.0.0.0/16` into 16 equal subnets. What is the 5th subnet?

<details>
<summary>Answer</summary>

Borrow 4 bits -> /20, block size 16 in the 3rd octet. Subnets: 0, 16, 32, 48, **64** -> the 5th is **10.0.64.0/20**.
</details>

**38.** A branch needs 500 hosts. What's the smallest prefix?

<details>
<summary>Answer</summary>

**/23** (510 usable). A /24 only holds 254.
</details>

**39.** A site needs 2,000 hosts. What's the smallest prefix?

<details>
<summary>Answer</summary>

**/21** (2,046 usable). A /22 only holds 1,022.
</details>

**40.** How many subnets can you get from `172.16.0.0/16` if each subnet must hold at least 100 hosts?

<details>
<summary>Answer</summary>

100 hosts needs a /25 (126 usable). Number of subnets = 2^(25 - 16) = 2^9 = **512**.
</details>

**41.** Which is the smallest prefix for a link between two routers?

<details>
<summary>Answer</summary>

**/30** (2 usable hosts).
</details>

### Level 6: VLSM

**42.** Allocate `192.168.10.0/24` for 100, 50 and 20 hosts, plus one router link.

<details>
<summary>Answer</summary>

Largest first:
- 100 hosts: **192.168.10.0/25** (.0 to .127)
- 50 hosts: **192.168.10.128/26** (.128 to .191)
- 20 hosts: **192.168.10.192/27** (.192 to .223)
- Link: **192.168.10.224/30** (.224 to .227)
</details>

**43.** Allocate `10.0.0.0/24` for 120, 60, 28 and 12 hosts, plus two router links.

<details>
<summary>Answer</summary>

- 120 hosts: **10.0.0.0/25** (.0 to .127)
- 60 hosts: **10.0.0.128/26** (.128 to .191)
- 28 hosts: **10.0.0.192/27** (.192 to .223)
- 12 hosts: **10.0.0.224/28** (.224 to .239)
- Link 1: **10.0.0.240/30** (.240 to .243)
- Link 2: **10.0.0.244/30** (.244 to .247)
</details>

**44.** Do `192.168.2.0/26` and `192.168.2.32/27` overlap?

<details>
<summary>Answer</summary>

**Yes.** The /26 covers .0 to .63, and the /27 covers .32 to .63, which is inside it.
</details>

**45.** Will subnets of /25, /26, /27 and two /30s fit in a single /24?

<details>
<summary>Answer</summary>

128 + 64 + 32 + 4 + 4 = 232 addresses, which is at most 256, so **yes** (24 addresses spare), provided you allocate largest first.
</details>

### Level 7: Wildcard masks and ACLs

**46.** What is the wildcard mask for /24?

<details>
<summary>Answer</summary>

**0.0.0.255**.
</details>

**47.** What is the wildcard mask for `255.255.255.224`?

<details>
<summary>Answer</summary>

255 - 224 = 31 -> **0.0.0.31**.
</details>

**48.** What is the wildcard mask for /20?

<details>
<summary>Answer</summary>

Mask 255.255.240.0 -> **0.0.15.255**.
</details>

**49.** Write the ACL match for the subnet `192.168.10.64/26`.

<details>
<summary>Answer</summary>

**192.168.10.64 0.0.0.63** (matches .64 to .127).
</details>

**50.** What range does `172.16.8.0 0.0.3.255` match?

<details>
<summary>Answer</summary>

**172.16.8.0 to 172.16.11.255** (a /22).
</details>

**51.** Write the OSPF `network` statement for `10.1.4.0/22` in area 0.

<details>
<summary>Answer</summary>

`network 10.1.4.0 0.0.3.255 area 0`
</details>

**52.** What wildcard matches exactly one host, `10.1.1.1`?

<details>
<summary>Answer</summary>

**0.0.0.0** (written `10.1.1.1 0.0.0.0` or `host 10.1.1.1`).
</details>

### Level 8: Route summarization

**53.** Summarize `192.168.0.0/24`, `192.168.1.0/24`, `192.168.2.0/24`, `192.168.3.0/24`.

<details>
<summary>Answer</summary>

**192.168.0.0/22**
</details>

**54.** Summarize `10.1.8.0/24` through `10.1.15.0/24`.

<details>
<summary>Answer</summary>

8 subnets, block size 8 starting at 8 -> **10.1.8.0/21**.
</details>

**55.** Summarize `172.16.32.0/24` and `172.16.33.0/24`.

<details>
<summary>Answer</summary>

**172.16.32.0/23**
</details>

**56.** Can `192.168.1.0/24` through `192.168.4.0/24` be one summary?

<details>
<summary>Answer</summary>

**No.** A /22 would start at 0 and cover 0 to 3, which includes .0 and misses .4. Use `192.168.1.0/24`, `192.168.2.0/23` and `192.168.4.0/24`.
</details>

**57.** Summarize `10.4.0.0/16` through `10.7.0.0/16`.

<details>
<summary>Answer</summary>

Block size 4 in the 2nd octet, starting at 4 -> mask octet 252 -> **10.4.0.0/14**.
</details>

### Level 9: IPv6

**58.** Compress `2001:0db8:0000:0000:0000:0000:0000:0001`.

<details>
<summary>Answer</summary>

**2001:db8::1**
</details>

**59.** Compress `2001:0db8:0000:0010:0000:0000:0000:0001`.

<details>
<summary>Answer</summary>

**2001:db8:0:10::1** (compress the longest zero run, and only once).
</details>

**60.** Expand `fe80::a:1`.

<details>
<summary>Answer</summary>

**fe80:0000:0000:0000:0000:0000:000a:0001**
</details>

**61.** What type of address is `fe80::1`?

<details>
<summary>Answer</summary>

**Link-local** (`FE80::/10`).
</details>

**62.** How many /64 subnets are in a /48?

<details>
<summary>Answer</summary>

2^(64 - 48) = 2^16 = **65,536**.
</details>

**63.** What is the next /64 subnet after `2001:db8:acad:1::/64`?

<details>
<summary>Answer</summary>

**2001:db8:acad:2::/64**
</details>

**64.** Build the EUI-64 interface ID for MAC `00:1A:2B:3C:4D:5E`.

<details>
<summary>Answer</summary>

Split, insert FFFE: `00:1A:2B:FF:FE:3C:4D:5E`. Flip the 7th bit of the first byte (00 -> 02). Interface ID: **021A:2BFF:FE3C:4D5E**. In an abbreviated address it appears as `21a:2bff:fe3c:4d5e`.
</details>

**65.** Does IPv6 use broadcast? What replaces it?

<details>
<summary>Answer</summary>

**No.** IPv6 uses **multicast** (for example `ff02::1` for all nodes) and NDP instead of ARP.
</details>
