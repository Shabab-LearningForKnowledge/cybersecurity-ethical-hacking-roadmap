# 🌐 Day 4 — Understanding Network Communication

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 4  
**Level:** Beginner  
**Estimated Time:** 60–90 minutes

---

## 🎯 1. What Will You Learn Today?

Yesterday, we learned the basic building blocks of networking.

Today, we will understand how these components work together when devices communicate.

We will learn:

- LAN and WAN
- Switches
- Routers
- Default Gateway
- Subnet basics
- TCP 3-Way Handshake
- Common ports and services
- DNS resolution
- HTTP communication
- Network attack surface
- Basic network observation

> 💡 The goal of Day 4 is to understand what happens when one device communicates with another device.

---

## 🏠 2. What Is a LAN?

LAN stands for **Local Area Network**.

A LAN is a network that connects devices within a limited area.

Examples:

- Home network
- Office network
- School network
- Computer laboratory

A simple example:

```text
Laptop
   |
   |
Switch
   |
   +------ Desktop
   |
   +------ Printer
   |
   +------ Server
```

All these devices can communicate within the local network.

### Why Is LAN Important in Cybersecurity?

An internal company network may contain:

- Employee computers
- Servers
- Databases
- Printers
- Network devices
- Internal applications

A security tester needs to understand how these systems are connected.

---

## 🌍 3. What Is a WAN?

WAN stands for **Wide Area Network**.

A WAN connects networks across larger geographical areas.

The Internet is the largest example of interconnected networks.

A simplified example:

```text
Office Network
      |
      ↓
   Router
      |
      ↓
   Internet
      |
      ↓
Cloud / Remote Server
```

### LAN vs WAN

| LAN | WAN |
|---|---|
| Smaller geographical area | Larger geographical area |
| Home or office network | Connects networks over larger distances |
| Usually privately managed | May use multiple network providers |
| Faster local communication is common | Communication may involve multiple networks |

> **LAN = Local Network**

> **WAN = Wide Area Network**

---

## 🔀 4. What Is a Network Switch?

A switch is a networking device that connects multiple devices within a local network.

For example:

```text
           Switch
        /    |    \
       /     |     \
   Laptop  Server  Printer
```

A switch forwards network traffic between connected devices.

### Simple Example

If your laptop needs to communicate with another computer on the same local network, the switch helps forward the traffic to the appropriate device.

### Security Perspective

A security tester should understand:

- Which devices are connected?
- Which systems belong to the same network?
- Which services are exposed?
- How is network traffic flowing?

Understanding the network structure helps in understanding the attack surface.

---

## 🚦 5. What Is a Router?

A router connects different networks.

For example:

```text
Local Network
     |
     ↓
   Router
     |
     ↓
  Internet
```

Your home router may connect:

- Your private home network
- Your Internet connection

### Simple Difference

```text
Switch
↓
Connects devices within a network

Router
↓
Connects different networks
```

This is a simplified explanation, but it is enough for now.

---

## 🚪 6. What Is a Default Gateway?

A default gateway is the device that a system uses to communicate with destinations outside its local network.

For example:

```text
Laptop
IP: 192.168.1.10
       |
       ↓
Default Gateway
192.168.1.1
       |
       ↓
Internet
```

If your computer wants to communicate with a destination outside its local network, it may send the traffic to the default gateway.

### Why Does This Matter?

During network troubleshooting and security assessment, understanding the gateway helps you understand:

- How the system reaches other networks
- Where traffic is forwarded
- How the local network connects to external networks

---

## 🧮 7. What Is a Subnet?

A subnet is a logical division of an IP network.

You may have seen an IP address such as:

```text
192.168.1.10
```

Along with a subnet mask such as:

```text
255.255.255.0
```

This helps determine which devices belong to the same network.

### Example

Consider:

```text
192.168.1.10
192.168.1.20
192.168.1.30
```

If they belong to the same subnet, they can generally communicate directly within that local network without needing a router between them.

### Why Should a Security Tester Understand Subnets?

During authorized testing, network boundaries can help identify:

- Which systems belong to a network
- Which systems are outside the local network
- How the environment is segmented
- Where network boundaries may exist

> 💡 You do not need to master subnetting today. First understand what a subnet is and why it exists.

---

## 🤝 8. How Does TCP Establish a Connection?

TCP uses a process commonly called the **3-Way Handshake** to establish a connection.

The three steps are:

```text
Client                    Server
  |                         |
  | ------ SYN -----------> |
  |                         |
  | <----- SYN-ACK -------- |
  |                         |
  | ------ ACK -----------> |
  |                         |
  |     Connection          |
  |     Established         |
```

### Step 1 — SYN

The client sends a **SYN** packet to request a connection.

### Step 2 — SYN-ACK

The server responds with **SYN-ACK**.

This indicates that the server received the request and is responding.

### Step 3 — ACK

The client sends an **ACK**.

The TCP connection can now proceed.

### Why Is This Important?

You will see TCP handshakes frequently when analyzing network traffic.

Later, when using network-analysis tools, understanding this process will help you recognize normal TCP communication.

> **SYN → SYN-ACK → ACK**

Remember this sequence.

---

## 🚪 9. Common Ports and Services

A network service commonly listens on a specific port.

Some examples are:

| Port | Protocol / Service | Common Purpose |
|---|---|---|
| 21 | FTP | File transfer |
| 22 | SSH | Secure remote access |
| 25 | SMTP | Email transfer |
| 53 | DNS | Name resolution |
| 80 | HTTP | Web traffic |
| 110 | POP3 | Email retrieval |
| 143 | IMAP | Email access |
| 443 | HTTPS | Secure web traffic |
| 3306 | MySQL | Database service |
| 3389 | RDP | Remote desktop |

### Important

A port number alone does not guarantee that a particular service is running.

For example:

```text
Port 80
```

is commonly associated with HTTP, but the actual service should be verified during an authorized assessment.

> **Port number = A clue, not proof of the service.**

---

## 🔎 10. What Does "Open Port" Mean?

A port may be described as **open** when a service is listening and accepting network connections on that port.

For example:

```text
192.168.1.10:80
```

If a web server is listening on port 80, the port may be accessible to systems that can reach it.

A simplified representation:

```text
Target
192.168.1.10

Port 22  → SSH
Port 80  → HTTP
Port 443 → HTTPS
```

### Why Does This Matter in Security?

Every exposed service can become part of the system's **attack surface**.

However:

> **Open port ≠ Vulnerability**

An open port simply means that a service is accessible.

The service must then be assessed for security weaknesses.

---

## 🌐 11. How Does DNS Resolution Work?

When you enter a domain name into a browser, the computer needs to determine the corresponding IP address.

A simplified process looks like this:

```text
User
  |
  ↓
Browser
  |
  ↓
DNS Resolver
  |
  ↓
IP Address
  |
  ↓
Web Server
```

For example:

```text
example.com
     |
     ↓
DNS
     |
     ↓
IP Address
```

The browser can then use that IP address to communicate with the destination.

### Why Does DNS Matter?

DNS is an important part of the Internet and enterprise networks.

During authorized reconnaissance, security professionals may examine DNS information to understand an organization's network presence.

---

## 🌐 12. What Happens When You Open a Website?

Let's understand a simplified version of what happens when you enter:

```text
https://example.com
```

### Step 1 — DNS

The system determines the IP address associated with the domain.

### Step 2 — Network Connection

The client establishes communication with the destination.

### Step 3 — HTTPS

The browser communicates with the web server using HTTPS.

### Step 4 — HTTP Request

The browser sends a request.

### Step 5 — Server Processing

The web server processes the request.

### Step 6 — HTTP Response

The server sends a response.

A simplified flow:

```text
Browser
   |
   | DNS Query
   ↓
DNS
   |
   | IP Address
   ↓
Browser
   |
   | HTTPS Request
   ↓
Web Server
   |
   | HTTPS Response
   ↓
Browser
```

This basic flow will become extremely important when we begin studying web application security.

---

## 📡 13. Request and Response

Communication between a client and server commonly involves a request and a response.

### Request

The client sends a request to the server.

For example:

```text
Client
   |
   | Request
   ↓
Server
```

### Response

The server sends a response back.

```text
Server
   |
   | Response
   ↓
Client
```

In web applications, the request and response can contain information such as:

- HTTP method
- URL
- Headers
- Parameters
- Cookies
- Response status
- Response body

We will study these concepts in much greater detail later.

---

## 🛡️ 14. What Is Network Segmentation?

Network segmentation means dividing a network into separate logical sections.

For example:

```text
Internet
   |
   ↓
Firewall
   |
   +-------- Web Server Network
   |
   +-------- Application Network
   |
   +-------- Database Network
   |
   +-------- Employee Network
```

The purpose can include:

- Limiting communication
- Separating systems
- Reducing exposure
- Controlling access
- Limiting the impact of a security incident

### Security Perspective

A security tester should understand whether systems are:

- Directly accessible
- Behind a firewall
- Separated into different network segments
- Restricted from communicating with each other

> **Network segmentation can reduce unnecessary communication between systems.**

---

## 🎯 15. Understanding the Network Attack Surface

The **attack surface** is the collection of points where a system or network can potentially be interacted with or attacked.

A simplified example:

```text
                Network
                   |
        +----------+----------+
        |          |          |
       SSH        HTTP       HTTPS
       22          80         443
        |          |          |
      Server     Web App    Web App
```

Each exposed service may represent part of the attack surface.

A security tester may ask:

- Which hosts are accessible?
- Which ports are open?
- Which services are running?
- Which applications are exposed?
- Which systems communicate with each other?
- What authentication mechanisms are present?

### Important

Attack surface does not mean:

> "Everything is vulnerable."

It means:

> **These are the areas that may need security assessment.**

---

## 🧠 16. Think Like a Security Tester

Imagine you are authorized to test a server.

You discover:

```text
Target: 192.168.1.10

22  → SSH
80  → HTTP
443 → HTTPS
```

Do not immediately start exploiting the server.

First ask:

### About the Network

- Where is the server located?
- What network does it belong to?
- Is it internal or externally accessible?

### About the Services

- What service is running on each port?
- What software is being used?
- Is the service expected to be exposed?

### About the Application

- What application is running?
- What functionality is available?
- What authentication is used?

This is how a security tester begins building an understanding of the target.

---

## 🛠️ 17. Day 4 Practical Exercise

Perform this exercise only on your own computer or an authorized lab system.

### Windows

Open Command Prompt.

Run:

```text
ipconfig
```

Identify:

- IPv4 address
- Default gateway

Then run:

```text
ping 127.0.0.1
```

This tests communication with your own local system.

Next, run:

```text
ping 8.8.8.8
```

Observe whether you receive replies.

### Linux

Open the terminal.

Run:

```text
ip addr
```

Identify:

- Network interface
- IPv4 address

Then run:

```text
ip route
```

Identify the default route.

Next:

```text
ping 127.0.0.1
```

Finally:

```text
ping 8.8.8.8
```

Observe the results.

### Your Task

Record:

- Your local IPv4 address
- Your default gateway
- Your network interface
- Whether the local ping succeeds
- Whether the external ping succeeds

Do not share your public IP address or other sensitive network information publicly.

---

## 📝 18. Day 4 Assignment

Answer the following questions in your own words.

### Question 1

What is a LAN?

### Question 2

What is a WAN?

### Question 3

What is the purpose of a network switch?

### Question 4

What is the purpose of a router?

### Question 5

What is a default gateway?

### Question 6

What is a subnet?

### Question 7

What are the three steps of the TCP 3-Way Handshake?

### Question 8

What does an open port mean?

### Question 9

Why does an open port not automatically mean that a vulnerability exists?

### Question 10

What is DNS resolution?

### Question 11

What happens when you enter a website address into your browser?

### Question 12

What is network segmentation?

### Question 13

What is a network attack surface?

---

## 🧠 19. Day 4 Quick Quiz

Try to answer these questions before checking the answers.

### Question 1

What does LAN stand for?

A. Local Area Network

B. Large Access Network

C. Local Application Network

D. Linked Area Node

### Question 2

Which device commonly connects different networks?

A. Switch

B. Router

C. Keyboard

D. Monitor

### Question 3

What is commonly used as the default gateway in a home network?

A. A router

B. A printer

C. A monitor

D. A keyboard

### Question 4

What is the correct TCP handshake sequence?

A. ACK → SYN → SYN-ACK

B. SYN → ACK → SYN-ACK

C. SYN → SYN-ACK → ACK

D. SYN-ACK → ACK → SYN

### Question 5

Which port is commonly associated with HTTPS?

A. 21

B. 22

C. 80

D. 443

### Question 6

What does DNS primarily help with?

A. Converting domain names to IP addresses

B. Encrypting hard drives

C. Managing RAM

D. Installing applications

### Question 7

What does an open port generally indicate?

A. A service may be listening and accepting connections

B. The system is definitely vulnerable

C. The system has been hacked

D. The firewall is disabled

### Question 8

What is network segmentation?

A. Dividing a network into separate logical sections

B. Removing all network devices

C. Changing a MAC address

D. Encrypting every packet

### Question 9

What is the attack surface?

A. Only the IP address of a system

B. The collection of points that may be exposed to interaction or attack

C. Only open ports

D. Only vulnerabilities

---

## ✅ 20. Quiz Answers

<details>
<summary>Click here to reveal the answers</summary>

1. **A — Local Area Network**

2. **B — Router**

3. **A — A router**

4. **C — SYN → SYN-ACK → ACK**

5. **D — 443**

6. **A — Converting domain names to IP addresses**

7. **A — A service may be listening and accepting connections**

8. **A — Dividing a network into separate logical sections**

9. **B — The collection of points that may be exposed to interaction or attack**

</details>

---

## 🔑 21. Key Takeaways

### LAN

A network covering a limited area such as a home or office.

### WAN

A network that connects networks across larger geographical areas.

### Switch

Connects devices within a local network.

### Router

Connects different networks.

### Default Gateway

Provides a path from the local network to other networks.

### Subnet

A logical division of an IP network.

### TCP 3-Way Handshake

```text
SYN → SYN-ACK → ACK
```

### Port

Helps identify a network service.

### Open Port

Usually indicates that a service is listening and accepting connections.

### DNS

Helps resolve domain names to IP addresses.

### Network Segmentation

Separates a network into different logical sections.

### Attack Surface

The collection of points that may be exposed to interaction or attack.

> **Understand the network. Understand the communication. Then understand the attack surface.**

---

## 📌 Day 4 Completion Checklist

Before considering Day 4 complete, make sure you can explain:

- [ ] What LAN is
- [ ] What WAN is
- [ ] What a switch does
- [ ] What a router does
- [ ] What a default gateway is
- [ ] What a subnet is
- [ ] TCP 3-Way Handshake
- [ ] Common ports and services
- [ ] What an open port means
- [ ] How DNS resolution works
- [ ] Basic HTTP communication
- [ ] What network segmentation means
- [ ] What a network attack surface is
- [ ] How to perform basic network observation

---

> **Learn responsibly. Test ethically.**
