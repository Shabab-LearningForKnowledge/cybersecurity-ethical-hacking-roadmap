# 🦈 Day 9 — Introduction to Wireshark & Network Packet Analysis

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 9  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In the previous two days, you learned how Nmap can help discover:

- Hosts
- Open ports
- Services
- Service versions
- Network exposure

Today, you will learn how to look at the actual network communication.

You will learn:

- What a network packet is
- What packet capture means
- What Wireshark is
- How Wireshark captures traffic
- Network interfaces
- Packets, frames, and protocols
- Basic Wireshark interface
- Packet details
- TCP communication
- DNS traffic
- HTTP traffic
- Basic display filters
- How to follow network conversations
- How to think like a packet analyst

The goal is:

> **Don't just know that communication happened. Learn how to inspect what actually happened.**

---

## 2. What Is Wireshark?

**Wireshark** is a network protocol analyzer.

It allows you to capture and inspect network traffic.

For example, when you visit a website, your computer may communicate with several systems.

Wireshark can help you observe that communication.

A simplified view is:

```text
Application
     ↓
Transport
     ↓
Network
     ↓
Data Link
     ↓
Network Interface
     ↓
Network
```

Wireshark can capture packets as they travel through a network interface.

---

## 3. What Is a Network Packet?

A network packet is a unit of data transmitted across a network.

For example, when you send information to a server, the information is divided into smaller units and transmitted.

A simplified packet can be visualized as:

```text
+----------------------+
| Header Information   |
+----------------------+
| Source Information   |
+----------------------+
| Destination          |
+----------------------+
| Protocol Information |
+----------------------+
| Data                 |
+----------------------+
```

Different protocols add their own information.

For example:

```text
Ethernet
   ↓
IP
   ↓
TCP
   ↓
HTTP
   ↓
Application Data
```

Wireshark allows you to inspect these layers.

---

## 4. Packet vs Frame

You may hear the terms:

- Packet
- Frame
- Segment
- Datagram

These terms can refer to data at different networking layers.

A simplified view is:

```text
Application Data
       ↓
TCP Segment
       ↓
IP Packet
       ↓
Ethernet Frame
```

For beginner-level learning, you can generally think of these as different representations of data as it moves through networking layers.

You do not need to memorize all terminology immediately.

The important thing is understanding that:

> Network communication is built in layers.

---

## 5. Why Is Packet Analysis Important in Cybersecurity?

Nmap gives you information such as:

```text
80/tcp open http
```

Wireshark can help you investigate the actual communication involving that service.

For example, you may observe:

```text
Client → Server
TCP SYN
Server → Client
TCP SYN-ACK
Client → Server
TCP ACK
```

You can then see how the connection was established.

Packet analysis can help security testers understand:

- Network protocols
- Connection behavior
- DNS requests
- HTTP communication
- TCP handshakes
- Unexpected traffic
- Communication between systems

---

## 6. Understanding Network Interfaces

A network interface allows a device to communicate with a network.

Examples include:

- Ethernet
- Wi-Fi
- Loopback

On Linux, you may see interfaces such as:

```text
eth0
```

or:

```text
ens33
```

For wireless networking, you may see:

```text
wlan0
```

The loopback interface is commonly:

```text
lo
```

with the address:

```text
127.0.0.1
```

### Important

Wireshark captures traffic from a selected network interface.

Therefore, choosing the correct interface is important.

---

## 7. Starting a Packet Capture

When you open Wireshark, you will normally see available network interfaces.

You may see something similar to:

```text
Ethernet
Wi-Fi
Loopback
```

Select the interface through which your traffic is passing.

Then start the capture.

You may immediately see packets appearing.

For example:

```text
No.    Time       Source        Destination     Protocol
1      0.000      192.168.1.5   192.168.1.1     DNS
2      0.021      192.168.1.5   93.x.x.x        TCP
3      0.045      93.x.x.x      192.168.1.5     TCP
```

Don't worry if the screen looks overwhelming.

We will break it down step by step.

---

## 8. Understanding the Wireshark Interface

A basic Wireshark window contains three important areas.

### Packet List

The top section displays captured packets.

You may see:

```text
No.
Time
Source
Destination
Protocol
Length
Info
```

### Packet Details

The middle section shows details about the selected packet.

For example:

```text
Ethernet II
Internet Protocol
Transmission Control Protocol
```

### Packet Bytes

The bottom section displays the raw packet data in hexadecimal and ASCII form.

Conceptually:

```text
+----------------------------+
| Packet List                |
+----------------------------+
| Packet Details             |
+----------------------------+
| Packet Bytes               |
+----------------------------+
```

For beginners, focus mainly on the **Packet List** and **Packet Details**.

---

## 9. Understanding Source and Destination

Every network packet has a source and destination.

For example:

```text
Source          Destination
192.168.1.10 → 192.168.1.1
```

This means the packet is traveling from:

```text
192.168.1.10
```

to:

```text
192.168.1.1
```

The direction of communication is important when analyzing traffic.

You should always ask:

> Who sent the packet?

> Who received it?

---

## 10. Understanding Protocols in Wireshark

Wireshark can identify many different protocols.

You may encounter:

```text
TCP
UDP
DNS
HTTP
TLS
ICMP
ARP
DHCP
```

For example:

```text
Source       Destination       Protocol
192.168.1.5  8.8.8.8           DNS
192.168.1.5  93.x.x.x          TCP
93.x.x.x     192.168.1.5       TCP
```

The protocol column gives you an initial clue about what is happening.

Remember:

> The protocol tells you how the communication is being handled. It does not automatically tell you whether the communication is secure.

---

## 11. Understanding the TCP Three-Way Handshake

You learned the TCP handshake on Day 4.

Now you can actually observe it.

The basic handshake is:

```text
Client                    Server

  SYN  ------------------>

       <------------------ SYN-ACK

  ACK  ------------------>
```

In Wireshark, you can often identify these packets by looking at the TCP information.

You may see information such as:

```text
[SYN]
[SYN, ACK]
[ACK]
```

This is a practical example of the networking theory you learned earlier.

### Why Is This Important?

It helps you understand how TCP connections are established.

---

## 12. Understanding DNS Traffic

DNS converts domain names into IP addresses.

For example:

```text
example.com
     ↓
DNS
     ↓
93.x.x.x
```

When your system performs a DNS query, Wireshark may capture DNS traffic.

You can use a display filter:

```text
dns
```

This tells Wireshark to display packets identified as DNS traffic.

You may see:

```text
Query
Response
```

For example:

```text
Client → DNS Server
Query: example.com

DNS Server → Client
Response: IP address
```

This allows you to see DNS communication directly.

---

## 13. Understanding HTTP Traffic

HTTP is used for communication between web clients and web servers.

A simplified example:

```text
Browser
   ↓
HTTP Request
   ↓
Web Server
   ↓
HTTP Response
   ↓
Browser
```

You can filter HTTP traffic using:

```text
http
```

You may see methods such as:

```text
GET
POST
```

For example:

```text
GET /index.html
```

This means the client is requesting a resource from the server.

### Important Security Concept

Traditional HTTP traffic can expose application-level information because it is not encrypted.

HTTPS uses TLS to provide encryption for the HTTP communication.

Therefore, you may see:

```text
HTTP
```

or:

```text
TLS
```

rather than readable HTTP application data.

---

## 14. Understanding HTTPS and TLS

HTTPS is essentially HTTP protected using TLS.

A simplified communication flow is:

```text
Browser
   ↓
TLS Connection
   ↓
Web Server
```

With HTTPS, application data is encrypted.

Therefore, when capturing HTTPS traffic, you may see information such as:

```text
TCP
TLS
```

instead of directly seeing the HTTP request contents.

For example, you may not simply see:

```text
GET /login
```

in readable form.

### Important

Encryption does not mean that there is no metadata.

You may still be able to observe information such as:

- Source IP
- Destination IP
- Port
- Packet size
- Timing
- TLS-related information

---

## 15. Understanding Wireshark Display Filters

A busy capture can contain thousands of packets.

You don't want to manually inspect every packet.

Wireshark provides **display filters**.

### DNS

```text
dns
```

### HTTP

```text
http
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

### Specific IP

```text
ip.addr == 192.168.1.10
```

### Specific TCP Port

```text
tcp.port == 80
```

These filters help you focus on the traffic relevant to your investigation.

---

## 16. Following a Network Conversation

Sometimes you want to understand an entire communication session instead of looking at individual packets.

Wireshark provides:

**Follow → TCP Stream**

This allows you to view packets belonging to a particular TCP conversation together.

Conceptually:

```text
Client
  ↓
Request
  ↓
Server
  ↓
Response
  ↓
Client
```

This is especially useful when studying application protocols.

### Important

The contents may not be readable if the communication is encrypted.

For example:

```text
HTTP → potentially readable
HTTPS → encrypted application data
```

---

## 17. Think Like a Security Tester

Imagine you are testing an authorized web application.

You perform an action in the browser:

```text
Login
```

At the same time, you capture traffic in Wireshark.

Instead of simply saying:

> "The login worked."

You can ask:

- Which server did the browser communicate with?
- Which protocol was used?
- Which port was used?
- Was DNS involved?
- Was HTTP or HTTPS used?
- How was the TCP connection established?
- Was the application traffic encrypted?
- What other network communication occurred?

This is the mindset of packet analysis:

> **Observe → Filter → Understand → Correlate**

---

## 18. Day 9 Practical Exercise

Perform this exercise on your own system or an authorized lab.

### Step 1 — Open Wireshark

Start Wireshark and identify the network interfaces.

Choose the interface that is actively carrying your network traffic.

### Step 2 — Start a Capture

Start capturing packets.

Then open a website that you are authorized to access.

After a few seconds, stop the capture.

### Step 3 — Identify Protocols

Look through the packet list.

Try to identify:

```text
TCP
UDP
DNS
TLS
HTTP
```

Not every protocol will necessarily appear.

### Step 4 — Filter DNS

Enter:

```text
dns
```

Observe the DNS packets.

Identify:

- Source
- Destination
- Query
- Response

### Step 5 — Filter TCP

Use:

```text
tcp
```

Look for TCP packets.

Try to identify:

```text
SYN
SYN-ACK
ACK
```

### Step 6 — Filter HTTP

If your authorized lab uses HTTP:

```text
http
```

Observe the requests and responses.

### Step 7 — Try an IP Filter

Use:

```text
ip.addr == 192.168.1.10
```

Replace the example IP with an IP relevant to your own authorized lab.

Observe how the packet list changes.

---

## 19. Day 9 Assignment

Complete the following tasks.

### Task 1

Explain in your own words:

> What is Wireshark?

### Task 2

Explain the difference between:

```text
Packet
Frame
```

### Task 3

Capture your own network traffic and identify at least three protocols.

Create a table:

| Protocol | Source | Destination | Purpose |
|---|---|---|---|
| | | | |
| | | | |
| | | | |

### Task 4

Use the following filters and record what you observe:

```text
dns
```

```text
tcp
```

```text
udp
```

### Task 5

Find a TCP connection and identify:

```text
SYN
SYN-ACK
ACK
```

### Task 6

If your authorized lab contains HTTP traffic, identify:

```text
HTTP Request
HTTP Response
```

### Task 7

Explain:

> Why can HTTPS traffic still reveal some network information even though the application data is encrypted?

---

## 20. Day 9 Quick Quiz

### Question 1

What is Wireshark?

A. A password manager  
B. A network protocol analyzer  
C. A web browser  
D. A firewall

### Question 2

What does a packet contain?

A. Only an IP address  
B. Network-related information and data  
C. Only a username  
D. Only a password

### Question 3

Which protocol is connection-oriented?

A. UDP  
B. TCP  
C. DNS  
D. ARP

### Question 4

Which Wireshark filter displays DNS traffic?

A. `tcp`  
B. `http`  
C. `dns`  
D. `ip`

### Question 5

Which sequence represents the TCP three-way handshake?

A. ACK → SYN → SYN-ACK  
B. SYN → SYN-ACK → ACK  
C. SYN → ACK → SYN-ACK  
D. FIN → ACK → SYN

### Question 6

What does this filter do?

```text
tcp.port == 80
```

A. Shows traffic related to TCP port 80  
B. Shows only UDP traffic  
C. Shows DNS traffic  
D. Blocks port 80

### Question 7

Why is HTTPS traffic generally not visible as readable HTTP content in a normal packet capture?

A. HTTPS does not use packets  
B. TLS encrypts the application data  
C. TCP removes the data  
D. DNS hides the traffic

### Question 8

What is one purpose of following a TCP stream?

A. Delete packets  
B. View communication belonging to a TCP conversation together  
C. Change an IP address  
D. Scan a network

---

## 21. Quiz Answers

1. **B — A network protocol analyzer**
2. **B — Network-related information and data**
3. **B — TCP**
4. **C — `dns`**
5. **B — SYN → SYN-ACK → ACK**
6. **A — Shows traffic related to TCP port 80**
7. **B — TLS encrypts the application data**
8. **B — View communication belonging to a TCP conversation together**

---

## ✅ Day 9 Completion Checklist

- [ ] I understand what Wireshark is
- [ ] I understand network packets
- [ ] I understand the difference between packets and frames
- [ ] I understand network interfaces
- [ ] I can start a packet capture
- [ ] I understand source and destination
- [ ] I can identify common protocols
- [ ] I can identify the TCP three-way handshake
- [ ] I can identify DNS traffic
- [ ] I understand HTTP traffic
- [ ] I understand why HTTPS traffic is encrypted
- [ ] I can use basic Wireshark display filters
- [ ] I understand how to follow a TCP conversation
- [ ] I completed the practical exercise
- [ ] I completed the assignment
- [ ] I completed the quiz

---

**Learn responsibly. Test ethically.**
