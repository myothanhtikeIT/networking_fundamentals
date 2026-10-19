# IPv4 Binary and Subnetting Fundamentals (Octet)

## 1 - Purpose

IPv4 subnetting divides an IP address space into smaller logical networks. Understanding binary explains how subnet masks identify the network portion of an address. In practical subnetting, the block-size method provides a faster way to find network boundaries without manually converting every address to binary.

The core relationship:

**CIDR prefix > subnet mask > block size / host bits > network boundaries**

---

## 2 - Structure of a IPv4 Address 

An IPv4 address contains *32 bits*, divided into four 8-bit groups called *octets*. Each octet is written as a decimal number from 0 to 255.

example:

```text
192.168.10.77
 192 .168 .10  .77  (four decimal octets) 
```

Each octet contains eight binary bits. The place values from left to right are:

| Bit position | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Decimal value | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |

A binary `1` means that place value is included; a `0` means it is not.

### Binary to decimal exps

| Binary | Calculation | Decimal |
|---|---|---:|
| `01000000` | 64 | 64 |
| `11000000` | 128 + 64 | 192 |
| `11100000` | 128 + 64 + 32 | 224 |
| `11110000` | 128 + 64 + 32 + 16 | 240 |
| `00101000` | 32 + 8 | 40 |

For example, `11100000` equals 224 because the first three bits are `1`, representing 128, 64, and 32. Their sum is 224.

### Decimal to binary example

To convert 77 to binary, identify which place values add up to 77:

- 64 fits; remainder: 13
- 8 fits; remainder: 5
- 4 fits; remainder: 1
- 1 fits; remainder: 0

The selected values are 64, 8, 4, and 1, so:

```text
Place value: 128  64  32  16   8   4   2   1
Bit:          0    1   0   0   1   1   0   1
```
so, decimal 77 is binary `01001101`.

Binary is the underlying representation used by subnet masks and IP calculations. Manual conversion is useful for understanding the mechanism, but the block size method is usually quicker for routine subnet calculations.

---

## 3 - What a Subnet Mask Does

A subnet mask identifies which bits in an IPv4 address represent the **network prefix** and which bits represent the **host portion**.

- Mask bits set to `1` identify the network portion.
- Mask bits set to `0` identify the host portion.

A CIDR prefix states how many leading bits are `1` in the subnet mask.

Examples:

| CIDR prefix | Subnet mask | Network bits | Host bits |
|---|---|---:|---:|
| `/0` | `0.0.0.0` | 0 | 32 |
| `/1` | `128.0.0.0` | 1 | 31 |
| `/2` | `192.0.0.0` | 2 | 30 |
| `/3` | `224.0.0.0` | 3 | 29 |
| `/4` | `240.0.0.0` | 4 | 28 |
| `/5` | `248.0.0.0` | 5 | 27 |
| `/6` | `252.0.0.0` | 6 | 26 |
| `/7` | `254.0.0.0` | 7 | 25 |
| `/8` | `255.0.0.0` | 8 | 24 |
| `/9` | `255.128.0.0` | 9 | 23 |
| `/10` | `255.192.0.0` | 10 | 22 |
| `/11` | `255.224.0.0` | 11 | 21 |
| `/12` | `255.240.0.0` | 12 | 20 |
| `/13` | `255.248.0.0` | 13 | 19 |
| `/14` | `255.252.0.0` | 14 | 18 |
| `/15` | `255.254.0.0` | 15 | 17 |
| `/16` | `255.255.0.0` | 16 | 16 |
| `/17` | `255.255.128.0` | 17 | 15 |
| `/18` | `255.255.192.0` | 18 | 14 |
| `/19` | `255.255.224.0` | 19 | 13 |
| `/20` | `255.255.240.0` | 20 | 12 |
| `/21` | `255.255.248.0` | 21 | 11 |
| `/22` | `255.255.252.0` | 22 | 10 |
| `/23` | `255.255.254.0` | 23 | 9 |
| `/24` | `255.255.255.0` | 24 | 8 |
| `/25` | `255.255.255.128` | 25 | 7 |
| `/26` | `255.255.255.192` | 26 | 6 |
| `/27` | `255.255.255.224` | 27 | 5 |
| `/28` | `255.255.255.240` | 28 | 4 |
| `/29` | `255.255.255.248` | 29 | 3 |
| `/30` | `255.255.255.252` | 30 | 2 |
| `/31` | `255.255.255.254` | 31 | 1 |
| `/32` | `255.255.255.255` | 32 | 0 |


## Fast calcing formulas

- **Network bits** = CIDR prefix.
- **Host bits** = `32 - CIDR prefix`.
- **Total IPv4 addresses** = `2^(host bits)`.
- **Conventional usable host addresses** = `2^(host bits) - 2` for ordinary subnets, with special cases for `/31` point-to-point links and `/32` host routes.


## Common Subnet Sizes

| CIDR prefix | Total addresses | Usable host addresses* | Common use |
|---|---:|---:|---|
| `/24` | 256 | 254 | Small LAN |
| `/25` | 128 | 126 | Half of a `/24` |
| `/26` | 64 | 62 | Smaller LAN or VLAN |
| `/27` | 32 | 30 | Small subnet |
| `/28` | 16 | 14 | Small device group |
| `/29` | 8 | 6 | Very small subnet |
| `/30` | 4 | 2 | Traditional router link |
| `/31` | 2 | 2 | Point-to-point link that supports `/31` addressing |
| `/32` | 1 | 1 | Single-host route |

\* Usable host counts follow conventional IPv4 subnetting rules. `/31` links can use both addresses as endpoints on suitable point-to-point links. A `/32` represents one specific IPv4 address, typically used as a host route rather than a regular multi-host subnet.

## subnetting memory tip and trikcs

everytime time the CIDR prefix increases by one, one bit is borrowed for the network and the total address count is halved:
- `/24` = 256 total addresses
- `/25` = 128 total addresses
- `/26` = 64 total addresses
- `/27` = 32 total addresses
- `/28` = 16 total addresses

The IP address alone does not determine its subnet. the prefix length or subnet mask is also required.

example, `192.168.10.139` could be configured with `/24`, `/25`, or `/23`. each configuration places the address in a different sized network.

---

## 4 -  Valid Mask-Octet Values

A conventional IPv4 subnet mask has consecutive `1` bits followed by consecutive `0` bits. Because of this pattern, an octet in a subnet mask can only have these values:

| Binary | Decimal | Number of leading network bits in the octet |
|---|---:|---:|
| `00000000` | 0 | 0 |
| `10000000` | 128 | 1 |
| `11000000` | 192 | 2 |
| `11100000` | 224 | 3 |
| `11110000` | 240 | 4 |
| `11111000` | 248 | 5 |
| `11111100` | 252 | 6 |
| `11111110` | 254 | 7 |
| `11111111` | 255 | 8 |

These values are not arbitrary. They are the decimal forms of eight-bit patterns that contain leading `1`s followed by `0`s.

The values 0 and 255 are also valid mask-octet values. For example, `255.255.255.0` is a valid `/24` mask.

### why these values matter

The mask-octet table lets a network administrator move between prefix notation and dotted-decimal notation, and calculate subnet block sizes. For instance:

```text
/27 means 255.255.255.224
Block size in the fourth octet = 256 - 224 = 32
```

---

## 5- Host Bits and Address Counts

IPv4 has 32 bits in total.

```text
Host bits = 32 - prefix length
Total addresses = 2^(host bits)
```

For ordinary IPv4 subnets where the network and broadcast addresses are reserved:

```text
Usable host addresses = 2^(host bits) - 2
```

Examples:

| Prefix | Host bits | Total addresses | Normally usable hosts |
|---|---:|---:|---:|
| `/23` | 9 | 512 | 510 |
| `/24` | 8 | 256 | 254 |
| `/25` | 7 | 128 | 126 |
| `/26` | 6 | 64 | 62 |
| `/27` | 5 | 32 | 30 |
| `/28` | 4 | 16 | 14 |
| `/29` | 3 | 8 | 6 |
| `/30` | 2 | 4 | 2 |

exceptions to the usual `-2` formula include `/31`, commonly used for point-to-point links, and `/32`, which identifies one IPv4 address.

### Relationship to `/24`

A `/24` has 8 host bits in the fourth octet, so it contains `2^8 = 256` total addresses.

- `/25` has 7 host bits: `2^7 = 128` total addresses, half as many as `/24`.
- `/26` has 6 host bits: `2^6 = 64` total addresses, one-quarter as many as `/24`.
- `/23` has 9 host bits: `2^9 = 512` total addresses, twice as many as `/24`.

each time the prefix length increases by one, the address block becomes half as large. Each time the prefix decreases by one, the address block becomes twice as large.

---

## 6 -  The Block-Size Method

The block-size method is a practical shortcut for finding subnet boundaries.

### Formula

For the octet where the subnet mask is neither 255 nor 0:

```text
Block size = 256 - mask-octet value
```

This octet is often called the *interesting octet*. Subnet boundaries in that octet occur at multiples of the block size.

| Mask octet | Block size |
|---:|---:|
| 128 | 128 |
| 192 | 64 |
| 224 | 32 |
| 240 | 16 |
| 248 | 8 |
| 252 | 4 |
| 254 | 2 |

### Example A: `192.168.10.77/27`

1. `/27` corresponds to `255.255.255.224`.
2. The interesting octet is the fourth octet, with value 224.
3. Block size: `256 - 224 = 32`.
4. The subnet boundaries are `.0`, `.32`, `.64`, `.96`, `.128`, `.160`, `.192`, and `.224`.
5. The address `.77` falls in the `.64-.95` block.

result:
- Network address: `192.168.10.64`
- Broadcast address: `192.168.10.95`
- Usable range: `192.168.10.65-192.168.10.94`
- Total addresses: 32
- Normally usable hosts: 30

### example B: `192.168.10.139/25`

1. `/25` corresponds to `255.255.255.128`.
2. Block size in the fourth octet: `256 - 128 = 128`.
3. The two blocks are `.0-.127` and `.128-.255`.
4. `.139` falls in the second block.

result:
- Network address: `192.168.10.128/25`
- Broadcast address: `192.168.10.255`
- Usable range: `192.168.10.129-192.168.10.254`
- Total addresses: 128
- Normally usable hosts: 126

### example C: `10.1.5.9/23`
For `/23`, the mask is `255.255.254.0`. The interesting octet is the **third** octet.

1. Block size: `256 - 254 = 2`.
2. Third octet subnet boundaries occur at 0, 2, 4, 6, 8, 10, and so on.
3. The third octet is 5, which falls in the block from 4 through 5.

Result:
- Network address: `10.1.4.0/23`
- Broadcast address: `10.1.5.255`
- Usable range: `10.1.4.1-10.1.5.254`
- Total addresses: 512
- Normally usable hosts: 510

### Full octet boundaries
The block-size shortcut is applied to the interesting octet. When a prefix ends on an octet boundary, such as `/8`, `/16`, or `/24`, complete octets are copied or treated as fully variable as appropriate. For example, a `/24` has mask `255.255.255.0` and spans all 256 values of the fourth octet.

---

## 7 - how binary calculates a network address

A host can determine its network address by applying a bitwise **AND** operation between its IPv4 address and subnet mask.

For an AND operation, the output bit is `1` only when both input bits are `1`.

Example: `192.168.10.77/27`

The final octet of the IP address is 77, which is `01001101` in binary. The final octet of the subnet mask is 224, which is `11100000` in binary.

```text
IP address:   01001101   (77)
Subnet mask:  11100000   (224)
              --------
AND result:   01000000   (64)
```

The network address is therefore `192.168.10.64/27`.
This operation explains the mechanism behind subnet calculations. For many manual subnetting exercises, the block-size method provides the same result more quickly.

---

## 8 - Determining Whether Two Hosts Share a Subnet

The prefix or subnet mask must be known for each host. An IP address alone is insufficient to determine its subnet.

Example:

```text
Host A: 192.168.10.70/26
Host B: 192.168.10.100/26
```

A `/26` uses mask `255.255.255.192` and has a block size of `256 - 192 = 64` in the fourth octet.

- Host A (`.70`) belongs to the `.64-.127` block.
- Host B (`.100`) also belongs to the `.64-.127` block.

Both hosts are in `192.168.10.64/26`.

Contrast this with:

```text
Host A: 192.168.10.70/26
Host B: 192.168.10.139/26
```

- Host A belongs to `192.168.10.64/26`.
- Host B belongs to `192.168.10.128/26`.

They are in different IP subnets. Communication between them requires Layer 3 routing, assuming appropriate routes and security policies permit the traffic.

---

## 9- Practical Uses in Network Administration

Subnetting knowledge is used to:

- Plan address ranges for departments, VLANs, sites, and point-to-point links.
- Choose a prefix that supports the required number of hosts.
- Configure interface addresses, subnet masks, and DHCP scopes correctly.
- Check whether a default gateway is in the client's local subnet.
- Determine whether traffic should be delivered locally or sent to a gateway.
- Troubleshoot devices that have incorrect masks, gateways, or overlapping subnets.
- Understand route entries, access-control rules, and network summaries.

Binary explains how the mask works. The mask table and block-size method make most everyday subnet calculations faster. In a timed certification exam, being able to calculate common subnet ranges without an external calculator is especially useful.

---

## 10- Quick Reference

### Common masks and block sizes

| Prefix | Subnet mask | Interesting octet | Block size | Total addresses | Normally usable hosts |
|---|---|---|---:|---:|---:|
| `/23` | `255.255.254.0` | Third: 254 | 2 | 512 | 510 |
| `/24` | `255.255.255.0` | Fourth: 0 / full octet | 256 in fourth octet | 256 | 254 |
| `/25` | `255.255.255.128` | Fourth: 128 | 128 | 128 | 126 |
| `/26` | `255.255.255.192` | Fourth: 192 | 64 | 64 | 62 |
| `/27` | `255.255.255.224` | Fourth: 224 | 32 | 32 | 30 |
| `/28` | `255.255.255.240` | Fourth: 240 | 16 | 16 | 14 |
| `/29` | `255.255.255.248` | Fourth: 248 | 8 | 8 | 6 |
| `/30` | `255.255.255.252` | Fourth: 252 | 4 | 4 | 2 |

### main formulas

```text
Host bits = 32 - prefix length
Total addresses = 2^(host bits)
Normally usable hosts = 2^(host bits) - 2
Block size = 256 - interesting mask-octet value
Wildcard mask = 255.255.255.255 - subnet mask
```

The usual usable host formula has exceptions, notably `/31` point-to-point links and `/32` host routes.

### facts

- `/24` = `255.255.255.0` = 256 total addresses, normally 254 usable.
- `/25` = `255.255.255.128` = two `/25` networks within one `/24`.
- `/26` = `255.255.255.192` = 64-address blocks.
- `/27` = `255.255.255.224` = 32-address blocks.
- `/23` = `255.255.254.0` = two consecutive `/24` address ranges.
- A smaller prefix number means a larger network, a larger prefix number means a smaller network.
- An IP address without a prefix length or subnet mask does not uniquely identify its subnet.
- The block-size method is usually quicker than converting every octet into binary manually.
