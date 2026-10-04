# 🦈 Day 10 — Practical Wireshark Packet Analysis

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 10  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In Day 9, you learned the basics of Wireshark and how to capture network traffic.

Today, you will go one step further and learn how to **analyze captured packets**.

You will learn:

- How to understand packet details
- How to identify TCP communication
- How to analyze DNS requests
- How to identify HTTP traffic
- How to identify ICMP traffic
- How to understand ARP traffic
- How to follow a TCP conversation
- How to use practical Wireshark filters
- How security testers use packet analysis

> **Goal:** Learn to observe network communication and understand what is happening between devices.

---

## 2. Why Is Packet Analysis Important?

When applications communicate over a network, they generate packets.

For example:

```text
Your Computer
      |
      | DNS Request
      ↓
    DNS Server
      |
      | DNS Response
      ↓
Your Computer
      |
      | HTTPS Request
      ↓
   Web Server
```

A security tester can use packet analysis to understand:

- Which systems are communicating
- Which protocols are being used
- Which ports are involved
- What type of requests are being sent
- Whether communication is encrypted
- Whether unexpected communication is occurring

Packet analysis helps turn network traffic into useful security information.

---

## 3. Understanding the Three Main Wireshark Areas

Wireshark normally displays three important areas.

### Packet List

The top section shows individual packets.

Example:

```text
No.    Time       Source        Destination     Protocol
1      0.000      192.168.1.5   192.168.1.1     DNS
2      0.120      192.168.1.5   142.250.x.x     TCP
3      0.250      192.168.1.5   142.250.x.x     TLS
```

### Packet Details

The middle section shows details of the selected packet.

You may see:

```text
Frame
Ethernet II
Internet Protocol
Transmission Control Protocol
```

### Packet Bytes

The bottom section displays the raw packet data in hexadecimal and ASCII format.

Think of it like:

```text
Packet List
    ↓
Packet Details
    ↓
Raw Packet Data
```

---

## 4. Understanding Packet Details

Click on a packet and expand its protocol sections.

For example:

```text
Frame
Ethernet II
Internet Protocol Version 4
Transmission Control Protocol
Hypertext Transfer Protocol
```

Each layer provides different information.

### Example

The IP layer may show:

```text
Source:      192.168.1.10
Destination: 192.168.1.20
```

The TCP layer may show:

```text
Source Port:      54321
Destination Port: 80
```

This helps you understand how the communication is taking place.

---

## 5. Analyzing TCP Communication

TCP is one of the most important protocols to understand during packet analysis.

TCP normally starts a connection using the **three-way handshake**.

```text
Client                         Server
  |                              |
  | -------- SYN --------------> |
  |                              |
  | <------ SYN + ACK ---------- |
  |                              |
  | -------- ACK --------------> |
  |                              |
  |       Connection Ready       |
```

In Wireshark, you may see:

```text
[SYN]
[SYN, ACK]
[ACK]
```

### What Does This Tell You?

It tells you that a TCP connection is being established between two systems.

---

## 6. Understanding TCP Flags

TCP packets contain flags that indicate different actions.

Common flags include:

| Flag | Meaning |
|---|---|
| SYN | Starts a TCP connection |
| ACK | Acknowledges received data |
| FIN | Gracefully closes a connection |
| RST | Resets a connection |
| PSH | Pushes data to the application |
| URG | Indicates urgent data |

For example:

```text
[SYN]
```

usually indicates the beginning of a TCP connection.

```text
[RST]
```

may indicate that a connection was reset.

> Do not assume that a single TCP flag automatically indicates a vulnerability. Always understand the surrounding communication.

---

## 7. Analyzing DNS Traffic

DNS converts domain names into IP addresses.

For example:

```text
example.com
     ↓
DNS Query
     ↓
DNS Server
     ↓
IP Address
```

In Wireshark, use:

```text
dns
```

This displays DNS-related packets.

You may see:

```text
Standard query
Standard query response
```

### Example

A DNS query might contain:

```text
Query:
example.com
```

The response may contain:

```text
Answer:
93.184.216.34
```

This allows you to observe how domain-name resolution works.

---

## 8. Understanding DNS Query and Response

DNS communication generally contains two important parts.

### Query

The client asks:

```text
What is the IP address of example.com?
```

### Response

The DNS server responds:

```text
example.com → IP address
```

Conceptually:

```text
Client
  |
  | DNS Query
  | "What is example.com?"
  ↓
DNS Server
  |
  | DNS Response
  | "It is this IP address."
  ↓
Client
```

This is useful during network analysis because DNS can reveal which domains a system is attempting to access.

---

## 9. Analyzing HTTP Traffic

HTTP is used for communication between web browsers and web servers.

Use the following filter:

```text
http
```

You may see:

```text
GET
POST
HTTP/1.1 200 OK
HTTP/1.1 404 Not Found
```

For example:

```text
GET /index.html HTTP/1.1
```

means the client requested:

```text
/index.html
```

The server may respond with:

```text
HTTP/1.1 200 OK
```

which indicates a successful HTTP response.

> Only analyze HTTP traffic from systems and applications you are authorized to inspect.

---

## 10. HTTP Request and Response

Web communication can be visualized as:

```text
Client
   |
   | HTTP Request
   | GET /login
   ↓
Web Server
   |
   | HTTP Response
   | 200 OK
   ↓
Client
```

A request may contain:

```text
Method
URL
Headers
Parameters
Body
```

A response may contain:

```text
Status Code
Headers
Response Body
```

Understanding this structure will help you later when studying web application security.

---

## 11. Understanding HTTPS and TLS

HTTPS protects HTTP communication using TLS.

Instead of:

```text
HTTP
```

you normally see:

```text
TLS
```

in packet captures.

Use:

```text
tls
```

to filter TLS traffic.

You may observe:

```text
Client Hello
Server Hello
Certificate
Application Data
```

The important concept is:

```text
HTTP
  ↓
TLS Encryption
  ↓
HTTPS
```

With properly configured HTTPS, the application data is encrypted during transmission.

You may still be able to observe information such as:

- Source IP
- Destination IP
- Destination port
- TLS-related metadata

But the encrypted application content is generally not directly readable from the capture.

---

## 12. Analyzing ICMP Traffic

ICMP is commonly used for network diagnostics.

The most familiar example is:

```text
ping
```

For example:

```bash
ping 8.8.8.8
```

In Wireshark, use:

```text
icmp
```

You may see:

```text
Echo Request
Echo Reply
```

Conceptually:

```text
Computer
   |
   | ICMP Echo Request
   ↓
Target
   |
   | ICMP Echo Reply
   ↓
Computer
```

This helps you understand whether ICMP communication is occurring.

---

## 13. Understanding ARP Traffic

ARP stands for:

**Address Resolution Protocol**

ARP helps a device discover the MAC address associated with an IPv4 address on the local network.

For example:

```text
Who has 192.168.1.1?
```

The device owning that IP may respond:

```text
192.168.1.1 is at AA:BB:CC:DD:EE:FF
```

Use this Wireshark filter:

```text
arp
```

You may observe:

```text
ARP Request
ARP Reply
```

### Simple Flow

```text
Device A
   |
   | Who has 192.168.1.1?
   ↓
Local Network
   |
   | 192.168.1.1 is at MAC Address
   ↓
Device A
```

ARP is important because it operates within the local network and helps devices communicate using Ethernet.

---

## 14. Useful Wireshark Filters

Display filters allow you to focus on specific traffic.

### DNS

```text
dns
```

### HTTP

```text
http
```

### HTTPS/TLS

```text
tls
```

### TCP

```text
tcp
```

### UDP

```text
udp
```

### ICMP

```text
icmp
```

### ARP

```text
arp
```

### Traffic from a specific IP

```text
ip.addr == 192.168.1.10
```

### Traffic between two IP addresses

```text
ip.addr == 192.168.1.10 && ip.addr == 192.168.1.20
```

### Specific TCP port

```text
tcp.port == 80
```

### Specific UDP port

```text
udp.port == 53
```

> Replace example IP addresses with addresses from your own authorized lab environment.

---

## 15. Following a TCP Conversation

Wireshark provides a useful feature called:

**Follow → TCP Stream**

This can help you understand communication belonging to the same TCP connection.

### Steps

1. Start or open a capture.
2. Find a TCP packet.
3. Right-click the packet.
4. Select **Follow**.
5. Select **TCP Stream**.

Wireshark will display the conversation associated with that TCP stream.

Conceptually:

```text
Client
  |
  | Request
  ↓
Server
  |
  | Response
  ↓
Client
```

This is useful when you want to understand a complete conversation instead of examining packets individually.

> Do not assume that visible data is sensitive or vulnerable simply because it appears in a packet capture. Analyze the context and security controls.

---

## 16. Think Like a Security Tester

When analyzing network traffic, don't just look at packets.

Ask questions.

### Question 1

**Who is communicating?**

```text
Source IP → Destination IP
```

### Question 2

**Which protocol is being used?**

```text
HTTP?
HTTPS?
DNS?
TCP?
UDP?
ICMP?
ARP?
```

### Question 3

**Which port is involved?**

```text
Source Port
Destination Port
```

### Question 4

**Is the communication encrypted?**

For example:

```text
HTTP → generally not encrypted
HTTPS → protected using TLS
```

### Question 5

**What is the communication doing?**

For example:

```text
DNS Query
TCP Connection
HTTP Request
HTTP Response
```

A security tester should move from:

```text
Packet
   ↓
Protocol
   ↓
Communication
   ↓
Behavior
   ↓
Security Observation
```

---

## 17. Day 10 Practical Exercise

> **Perform these exercises only on your own computer or an authorized lab environment.**

### Exercise 1 — Capture Your Traffic

Open Wireshark and select the network interface that is currently being used.

Start the capture.

Generate some normal traffic, such as:

- Open an authorized website
- Run a DNS lookup
- Use `ping`
- Open a local application that generates network traffic

Stop the capture after a short period.

---

### Exercise 2 — Find DNS Traffic

Apply:

```text
dns
```

Identify:

- Source IP
- Destination IP
- DNS query
- DNS response

Record your observations.

---

### Exercise 3 — Find TCP Traffic

Apply:

```text
tcp
```

Select one TCP connection.

Look for:

```text
SYN
SYN, ACK
ACK
```

Try to identify the three-way handshake.

---

### Exercise 4 — Analyze ICMP

Run:

```bash
ping 8.8.8.8
```

Then use:

```text
icmp
```

in Wireshark.

Identify:

```text
Echo Request
Echo Reply
```

---

### Exercise 5 — Analyze ARP

Apply:

```text
arp
```

Look for:

```text
ARP Request
ARP Reply
```

Try to understand which IP and MAC addresses are involved.

---

### Exercise 6 — Analyze HTTP or TLS

If your authorized lab generates HTTP traffic, use:

```text
http
```

If it generates HTTPS traffic, use:

```text
tls
```

Identify:

- Source
- Destination
- Protocol
- Destination port
- Relevant request/response information

---

### Exercise 7 — Follow a TCP Stream

Select an appropriate TCP packet.

Go to:

```text
Right Click → Follow → TCP Stream
```

Observe how Wireshark groups packets belonging to the same TCP conversation.

---

## 18. Day 10 Assignment

Complete the following assignment using your own system or an authorized lab.

### Task 1

Capture network traffic for approximately 2–5 minutes.

### Task 2

Find at least:

- 3 DNS packets
- 3 TCP packets
- 1 ARP packet, if available
- 1 ICMP packet, if available
- 1 TLS packet, if available

### Task 3

Create a small observation table:

| Protocol | Source | Destination | Port | Observation |
|---|---|---|---|---|
| DNS | Your IP | DNS Server | 53 | DNS query |
| TCP | Your IP | Target IP | 443 | TCP communication |
| ARP | Local IP | Broadcast | - | ARP request |
| ICMP | Your IP | Target IP | - | Echo request |

### Task 4

Answer these questions:

1. What is the difference between a packet and a protocol?
2. What happens during the TCP three-way handshake?
3. What is the purpose of DNS?
4. What is the difference between HTTP and HTTPS?
5. What is ARP used for?
6. What does an ICMP Echo Request indicate?
7. Why is packet analysis useful to a security tester?

---

## 19. Day 10 Quick Quiz

### Question 1

Which protocol is commonly used to resolve domain names into IP addresses?

A. HTTP  
B. DNS  
C. FTP  
D. SSH

---

### Question 2

Which TCP flag normally starts a TCP connection?

A. ACK  
B. FIN  
C. SYN  
D. RST

---

### Question 3

Which Wireshark filter displays DNS traffic?

A. `http`  
B. `tcp`  
C. `dns`  
D. `arp`

---

### Question 4

What does ARP help determine?

A. Domain name  
B. MAC address associated with an IPv4 address on the local network  
C. HTTP status code  
D. TLS certificate

---

### Question 5

Which protocol is commonly associated with the `ping` command?

A. ICMP  
B. DNS  
C. HTTP  
D. FTP

---

### Question 6

What does HTTPS primarily provide for HTTP communication?

A. Encryption through TLS  
B. Faster DNS  
C. MAC address resolution  
D. Port scanning

---

### Question 7

What does `ip.addr == 192.168.1.10` do in Wireshark?

A. Scans the IP  
B. Filters traffic involving that IP address  
C. Changes the IP address  
D. Blocks the IP address

---

### Question 8

What is the purpose of **Follow → TCP Stream**?

A. Scan a TCP port  
B. Reboot a TCP service  
C. View packets belonging to a TCP conversation  
D. Encrypt TCP traffic

---

## 20. Quiz Answers

| Question | Answer | Explanation |
|---|---|---|
| 1 | B | DNS resolves domain names to IP addresses. |
| 2 | C | SYN is normally used to initiate a TCP connection. |
| 3 | C | `dns` filters DNS traffic. |
| 4 | B | ARP maps IPv4 addresses to MAC addresses on the local network. |
| 5 | A | Ping commonly uses ICMP Echo Request and Echo Reply. |
| 6 | A | HTTPS protects HTTP communication using TLS. |
| 7 | B | It filters packets involving the specified IP address. |
| 8 | C | It displays packets belonging to the selected TCP conversation. |

---

## 21. Key Takeaways

By completing Day 10, you should understand:

- How to read packet details in Wireshark
- How TCP communication works
- TCP flags such as SYN, ACK, FIN, and RST
- How DNS requests and responses work
- How HTTP requests and responses appear in packet captures
- Why HTTPS traffic appears differently from HTTP
- How ICMP traffic works
- How ARP works on a local network
- How to use basic Wireshark display filters
- How to follow a TCP conversation
- How packet analysis can support security testing

The most important lesson is:

```text
Don't just look at packets.
Understand the communication.
```

---

## Day 10 Completion Checklist

- [ ] I understand the three main Wireshark viewing areas.
- [ ] I can identify TCP packets.
- [ ] I understand the TCP three-way handshake.
- [ ] I can identify DNS traffic.
- [ ] I understand HTTP and HTTPS traffic.
- [ ] I can identify ICMP traffic.
- [ ] I understand the basic purpose of ARP.
- [ ] I can use basic Wireshark display filters.
- [ ] I can follow a TCP stream.
- [ ] I can perform basic packet analysis on an authorized system.

**Learn responsibly. Test ethically.**
