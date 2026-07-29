# networking fundamentals notes
## TCP - IP
- TCP/IP is a suite of communication protocols that enables devices on different networks to communicate. IP is responsible for addressing and routing packets between devices, while TCP provides reliable, ordered, and error-checked delivery of data.
```bash 
TCP/IP
│
├── IP
├── TCP
├── UDP
├── ICMP
├── ARP
├── DNS
├── DHCP
└── many others
```
---
### TPC / IP at it's core 
- TPC (Transmission Control Protocol )
Suppose you open Reddit.
Your computer wants a webpage.
How does Reddit know where you are?
How does your request travel across oceans?
How do routers know where to send it?
How does Reddit know if some data got lost?
TCP/IP answers all of these questions.

- IP (Internet Protocol)
Every device connected to a network needs an address.
IP's job is simply to deliever packets to particular address.
---
- By using TCP/IP Engineers and Scientists made the Interent
real world example : Visiting  https: //reddit. com, whats happening under the hood
```bash 
Browser => TPC create connection => IP find the route => Router forward packets => Reddit servers received request => Video packets sent back => TCP checks everything arrived => Browser plays posts/videos

For example:
Brwosers           => "What data do I want?" (HTTP)
TCP             => "How do I deliver it for sure?"
IP              => "Where should it go?"
Ethernet/Wi-Fi  => "How do I send it over this local link?"
NIC             => "How do I turn it into electrical signals or radio waves?"
```