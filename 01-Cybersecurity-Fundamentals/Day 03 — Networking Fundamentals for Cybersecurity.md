# 🌐 Day 3 — Networking Fundamentals for Cybersecurity

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 3  
**Level:** Beginner  
**Estimated Time:** 60–90 minutes

---

## 🎯 1. What Will You Learn Today?

Before testing a network, application or server, you need to understand how computers communicate with each other.

Today we will learn:

- What a computer network is
- How devices communicate
- IP addresses
- MAC addresses
- Public and private IP addresses
- Ports
- Protocols
- TCP and UDP
- DNS
- HTTP and HTTPS
- Client and server
- Packets
- Basic networking commands
- How a security tester thinks about a network

> 💡 The goal of Day 3 is to understand how information moves from one system to another.

---

## 🌐 2. What Is a Computer Network?

A computer network is a group of devices that can communicate with each other.

These devices can include:

- Computers
- Servers
- Mobile phones
- Routers
- Switches
- Printers
- Security devices
- IoT devices

A simple example is your home network.

```text
Laptop
   |
   |
Router
   |
   +------ Mobile Phone
   |
   +------ Smart TV
   |
   +------ Internet
```

All these devices can communicate through the network.

### Why Does Networking Matter in Cybersecurity?

Most modern attacks involve communication between systems.

For example:

```text
Attacker
   |
   ↓
Network
   |
   ↓
Target System
   |
   ↓
Application
```

Therefore, understanding networking is one of the most important foundations of cybersecurity.

---

## 🔗 3. How Do Devices Communicate?

When one device wants to communicate with another device, information is sent through the network.

For example:

```text
Computer A
    |
    | Request
    ↓
Network
    |
    ↓
Computer B
    |
    | Response
    ↓
Network
    |
    ↓
Computer A
```

The communication normally involves:

1. Source device
2. Destination device
3. Network
4. Communication protocol
5. Data

A security tester needs to understand these components to identify where security weaknesses may exist.

---

## 📍 4. What Is an IP Address?

An IP address is used to identify a device on a network.

Think of it like an address for network communication.

For example:

```text
192.168.1.10
```

Another device could have:

```text
192.168.1.20
```

The network uses these addresses to determine where information should be sent.

### IPv4

The most common IPv4 format looks like:

```text
192.168.1.10
```

It contains four numbers separated by dots.

Each section can have a value from:

```text
0 to 255
```

### Why Is an IP Address Important in Security?

During authorized security testing, identifying IP addresses can help a tester understand:

- Which systems exist
- Which system is being tested
- Which network the system belongs to
- Where services may be running

> **IP address = Network address used to identify a device.**

---

## 🏠 5. Private and Public IP Addresses

IP addresses can be used in different network environments.

### Private IP Address

Private IP addresses are commonly used inside internal networks.

Common private IPv4 ranges include:

```text
10.0.0.0 – 10.255.255.255
```

```text
172.16.0.0 – 172.31.255.255
```

```text
192.168.0.0 – 192.168.255.255
```

For example:

```text
192.168.1.10
```

is commonly used inside a local network.

### Public IP Address

A public IP address can be used to communicate over the Internet.

For example:

```text
Internet
   |
Public IP
   |
Router
   |
Private Network
```

### Important

A private IP address does not automatically mean the system is secure.

A public IP address does not automatically mean the system is vulnerable.

Security depends on configuration, services, authentication, architecture and other controls.

---

## 🆔 6. What Is a MAC Address?

MAC stands for **Media Access Control**.

A MAC address is associated with a network interface.

An example can look like:

```text
00:1A:2B:3C:4D:5E
```

The MAC address is mainly used for communication within a local network.

### IP vs MAC

A simple way to remember:

```text
IP Address
    ↓
Logical network address

MAC Address
    ↓
Network interface address
```

Both are important, but they serve different purposes.

> 💡 Don't try to memorize everything today. Understand the basic difference first.

---

## 🚪 7. What Is a Port?

A port helps identify a specific network service or application on a system.

Think of an IP address as a building address.

A port can be thought of as a specific door inside that building.

For example:

```text
192.168.1.10:80
```

Here:

```text
192.168.1.10 = IP address
80           = Port
```

A system can have many ports.

Some commonly known ports include:

| Port | Common Service |
|---|---|
| 22 | SSH |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |

### Why Are Ports Important?

Security testers may identify which ports are accessible on an authorized target.

This can help determine:

- Which services may be running
- What technologies may be exposed
- Where further testing may be required

> **IP identifies the system. Port helps identify the service.**

---

## 📡 8. What Is a Protocol?

A protocol is a set of rules used for communication.

Different types of communication use different protocols.

Examples:

- HTTP
- HTTPS
- DNS
- SSH
- FTP
- TCP
- UDP

Imagine two people trying to communicate.

If they don't follow a common language or set of rules, communication becomes difficult.

Protocols provide those rules for computers.

---

## 🔄 9. TCP and UDP

TCP and UDP are important transport-layer protocols.

### TCP

TCP is connection-oriented.

It focuses on reliable delivery of data.

A simplified view:

```text
Client
  |
  | Establish Connection
  ↓
Server
  |
  | Data Exchange
  ↓
Client
```

TCP is commonly used by protocols such as:

- HTTP
- HTTPS
- SSH
- FTP

### UDP

UDP is connectionless.

It generally has less communication overhead than TCP and does not provide the same delivery guarantees.

UDP is commonly used for protocols and applications such as:

- DNS
- DHCP
- Streaming
- Online applications

### Simple Difference

```text
TCP → Reliability
UDP → Speed / Lower Overhead
```

This is a simplified explanation, but it is enough for your current level.

---

## 🌍 10. What Is DNS?

DNS stands for **Domain Name System**.

DNS converts domain names into IP addresses.

For example:

```text
example.com
     |
     ↓
DNS
     |
     ↓
93.184.216.34
```

Humans find names easier to remember than numbers.

Instead of remembering an IP address, we can use:

```text
example.com
```

### Why Does DNS Matter in Cybersecurity?

DNS can provide useful information during authorized security assessments.

For example, security testers may investigate:

- Domain names
- Subdomains
- DNS records
- Name servers
- Related infrastructure

We will study these concepts later when we reach reconnaissance.

---

## 🌐 11. What Are HTTP and HTTPS?

HTTP stands for **Hypertext Transfer Protocol**.

It is commonly used for communication between web browsers and web servers.

For example:

```text
Browser
   |
   | HTTP Request
   ↓
Web Server
   |
   | HTTP Response
   ↓
Browser
```

### HTTPS

HTTPS is HTTP protected using TLS encryption.

A simplified comparison:

```text
HTTP
↓
Data is sent without TLS protection

HTTPS
↓
HTTP + TLS protection
```

HTTPS helps protect data while it is being transmitted.

### Why Is This Important?

Later, when we learn web application security, we will examine:

- HTTP requests
- HTTP responses
- Headers
- Cookies
- Methods
- Status codes
- Parameters

Understanding HTTP now will make web security much easier later.

---

## 🖥️ 12. Client and Server

A **client** requests a service.

A **server** provides a service.

For example:

```text
Client
(Browser)
    |
    | Request
    ↓
Web Server
    |
    | Response
    ↓
Client
(Browser)
```

### Examples of Clients

- Web browser
- Mobile application
- Desktop application
- API client

### Examples of Servers

- Web server
- Database server
- Mail server
- DNS server
- File server

### Security Perspective

A security tester should ask:

- What is the client?
- What is the server?
- How do they communicate?
- Which protocol is being used?
- Which ports are involved?
- What information is being exchanged?

---

## 📦 13. What Is a Network Packet?

Data sent across a network is divided into smaller units called packets.

A simplified example:

```text
Large Data
    |
    ↓
+---------+
| Packet 1|
+---------+
| Packet 2|
+---------+
| Packet 3|
+---------+
    |
    ↓
Destination
```

Packets contain information required for network communication.

They can include information related to:

- Source
- Destination
- Protocol
- Data

### Why Are Packets Important?

Security professionals often need to understand network traffic.

Later, tools such as **Wireshark** can help us inspect network packets in authorized environments.

> **Packet = A unit of data transmitted across a network.**

---

## 🔎 14. Basic Network Observation Commands

You can use basic commands to understand your own system's network configuration.

Perform these commands only on your own system or an authorized lab.

### Windows — View IP Configuration

```text
ipconfig
```

This displays network configuration information.

### Windows — Detailed Network Information

```text
ipconfig /all
```

This provides additional network configuration information.

### Linux — View Network Information

```text
ip addr
```

This displays network interfaces and IP addresses.

### Test Connectivity

Windows and Linux commonly support:

```text
ping 8.8.8.8
```

This can be used to test network connectivity.

> ⚠️ Do not interpret a failed ping as proof that a system is offline. Firewalls and network configurations can block ICMP traffic.

---

## 🧠 15. Think Like a Security Tester

Imagine you are authorized to assess a network.

You are given one system:

```text
192.168.1.10
```

Instead of immediately trying to exploit it, start by asking:

- What is this system?
- What IP address does it use?
- What services are available?
- Which ports are accessible?
- What protocols are being used?
- What applications may be running?
- How does the system communicate with other systems?

This mindset is important.

> **First understand the target. Then identify the attack surface. Then test it.**

---

## 🎯 16. Day 3 Practical Exercise

Perform this exercise only on your own computer or an authorized lab.

### Windows

Open Command Prompt and run:

```text
ipconfig
```

Record:

- IPv4 Address
- Default Gateway
- DNS information

Then run:

```text
ipconfig /all
```

Identify:

- Network adapter
- MAC address
- IPv4 address
- DNS server

Finally, test connectivity:

```text
ping 8.8.8.8
```

Record whether you receive replies.

### Linux

Open the terminal and run:

```text
ip addr
```

Identify:

- Network interface
- IPv4 address
- MAC address

Then run:

```text
ip route
```

Identify the default route.

Finally:

```text
ping 8.8.8.8
```

Record whether you receive replies.

---

## 📝 17. Day 3 Assignment

Answer the following questions in your own words.

### Question 1

What is a computer network?

### Question 2

What is an IP address?

### Question 3

What is the difference between a private IP and a public IP?

### Question 4

What is a MAC address?

### Question 5

What is a network port?

### Question 6

What is a protocol?

### Question 7

What is the basic difference between TCP and UDP?

### Question 8

What is DNS?

### Question 9

What is the difference between HTTP and HTTPS?

### Question 10

What is the difference between a client and a server?

### Question 11

What is a network packet?

### Question 12

Why are ports important during security testing?

---

## 🧠 18. Day 3 Quick Quiz

Try to answer the questions before checking the answers.

### Question 1

What identifies a device at the IP layer?

A. Port

B. IP address

C. Protocol

D. Packet

### Question 2

Which of the following is commonly a private IPv4 address?

A. 192.168.1.10

B. 8.8.8.8

C. 1.1.1.1

D. 142.250.72.14

### Question 3

Which protocol is commonly associated with port 443?

A. HTTP

B. HTTPS

C. DNS

D. SSH

### Question 4

Which protocol translates domain names into IP addresses?

A. DNS

B. FTP

C. SSH

D. SMTP

### Question 5

Which protocol is connection-oriented?

A. UDP

B. TCP

C. DNS

D. HTTP

### Question 6

What does a port help identify?

A. A specific network service

B. The computer's RAM

C. The operating system version

D. The monitor

### Question 7

What is a client?

A. A system that requests a service

B. A network cable

C. A storage device

D. A protocol

### Question 8

What is a server?

A. A system that provides a service

B. A keyboard

C. A MAC address

D. A packet

---

## ✅ 19. Quiz Answers

<details>
<summary>Click here to reveal the answers</summary>

1. **B — IP address**

2. **A — 192.168.1.10**

3. **B — HTTPS**

4. **A — DNS**

5. **B — TCP**

6. **A — A specific network service**

7. **A — A system that requests a service**

8. **A — A system that provides a service**

</details>

---

## 🔑 20. Key Takeaways

Remember these concepts:

### Network

A group of devices that communicate with each other.

### IP Address

Identifies a device at the network layer.

### MAC Address

Identifies a network interface at the local network level.

### Port

Helps identify a network service.

### Protocol

Defines rules for communication.

### TCP

Provides connection-oriented and reliable communication.

### UDP

Provides connectionless communication with lower overhead.

### DNS

Maps domain names to IP addresses.

### HTTP

Protocol commonly used for web communication.

### HTTPS

HTTP protected using TLS.

### Client

Requests a service.

### Server

Provides a service.

### Packet

A unit of data transmitted across a network.

> **Understand the communication before trying to understand the attack.**

---

## 📌 21. Day 3 Completion Checklist

Before considering Day 3 complete, make sure you can explain:

- [ ] What a computer network is
- [ ] What an IP address is
- [ ] Private vs public IP addresses
- [ ] What a MAC address is
- [ ] What a port is
- [ ] What a protocol is
- [ ] TCP vs UDP
- [ ] What DNS does
- [ ] HTTP vs HTTPS
- [ ] Client vs server
- [ ] What a network packet is
- [ ] Basic network observation commands
- [ ] Why networking knowledge is important for cybersecurity

---

> **Learn responsibly. Test ethically.**
