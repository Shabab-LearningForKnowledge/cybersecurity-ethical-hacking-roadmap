# 🔎 Day 7 — Introduction to Nmap & Network Scanning

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 7  
**Level:** Beginner  
**Estimated Time:** 60–90 minutes

---

## 1. What Will You Learn Today?

Until now, you have learned:

- Computer fundamentals
- Operating systems
- Networking
- IP addresses
- Ports
- Protocols
- Linux
- Linux commands
- Basic enumeration

Today, you will connect these concepts with one of the most widely used network security tools:

> **Nmap**

You will learn:

- What Nmap is
- Why Nmap is used
- What network scanning means
- Hosts and ports
- Basic Nmap syntax
- Host discovery
- Port scanning
- Service detection
- OS detection
- Scan output
- Common Nmap options
- How to interpret scan results
- How a security tester uses Nmap during an authorized assessment

---

## 2. What Is Nmap?

**Nmap** stands for **Network Mapper**.

It is a network discovery and security auditing tool.

Nmap can help identify:

- Live hosts
- Open ports
- Running services
- Service versions
- Operating system information
- Network exposure

A simple way to understand Nmap is:

```text
Target
   ↓
Nmap
   ↓
Information
   ↓
Analysis
```

Nmap does not magically tell you that a system is vulnerable.

It helps you collect information that can be investigated further.

---

## 3. Why Is Nmap Important in Cybersecurity?

Imagine you are authorized to assess a server.

You know its IP address:

```text
192.168.1.10
```

But you don't know:

- Which ports are open
- Which services are running
- Whether SSH is exposed
- Whether a web server is running
- Which services may require further investigation

Nmap can help answer these questions.

For example:

```bash
nmap 192.168.1.10
```

A scan might show:

```text
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https
```

Now you have information that can guide your next authorized testing steps.

---

## 4. What Is Network Scanning?

Network scanning is the process of sending requests or probes to a target network or host to discover information.

Depending on the scan, you may want to determine:

```text
Is the host reachable?
        ↓
Which ports are open?
        ↓
Which services are running?
        ↓
Which versions are running?
        ↓
What should be investigated?
```

This is part of the reconnaissance and enumeration process.

### Important

Scanning must only be performed against systems you own or systems for which you have explicit authorization.

Do not randomly scan public IP addresses or networks.

---

## 5. Understanding a Target

Before using Nmap, you need to understand what you are scanning.

A target can be:

### Single IP

```text
192.168.1.10
```

### Hostname

```text
example.local
```

### Network

```text
192.168.1.0/24
```

A `/24` network commonly represents addresses from:

```text
192.168.1.0
```

through:

```text
192.168.1.255
```

However, not every address is necessarily a usable host.

Understanding CIDR notation will become increasingly useful as you perform network assessments.

---

## 6. Understanding Ports

You learned about ports on Day 3 and Day 4.

A port acts as a communication endpoint for network services.

Some commonly encountered ports are:

| Port | Common Service |
|---|---|
| 21 | FTP |
| 22 | SSH |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |
| 3306 | MySQL |
| 3389 | RDP |

Remember:

> A port number is a clue about the service. It is not proof of the service.

For example, HTTP commonly uses port 80, but an application can be configured to use another port.

---

## 7. Your First Nmap Scan

If Nmap is installed, you can perform a basic scan against an authorized target.

Example:

```bash
nmap 192.168.1.10
```

Nmap will attempt to identify accessible ports on the target.

You may see output similar to:

```text
Starting Nmap

PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https

Nmap done
```

The important columns are:

```text
PORT
STATE
SERVICE
```

---

## 8. Understanding Nmap Output

Let's understand the output.

Example:

```text
22/tcp   open   ssh
```

### `22`

This is the port number.

### `tcp`

This tells you the protocol.

### `open`

This indicates that the port is accepting connections.

### `ssh`

This is the service Nmap associates with that port.

So:

```text
22/tcp open ssh
```

can be understood as:

> TCP port 22 is open and appears to be running SSH.

This does **not** automatically mean SSH is vulnerable.

---

## 9. What Does "Open Port" Mean?

An open port generally means that a service is listening and accepting connections.

For example:

```text
22 → SSH
80 → HTTP
443 → HTTPS
```

If a port is open, you have discovered an entry point into the network-facing attack surface.

But:

> **Open does not mean vulnerable.**

For example:

```text
443/tcp open https
```

only tells you that HTTPS is available.

You would need additional testing to determine:

- What web server is running
- Which version is being used
- How it is configured
- What application is behind it
- Whether vulnerabilities exist

This distinction is extremely important for security testing.

---

## 10. Scanning Specific Ports

You don't always need to scan every port.

You can specify a port:

```bash
nmap -p 80 192.168.1.10
```

You can specify multiple ports:

```bash
nmap -p 22,80,443 192.168.1.10
```

You can specify a range:

```bash
nmap -p 1-100 192.168.1.10
```

This scans ports from:

```text
1
```

to:

```text
100
```

### Why Is This Useful?

During an assessment, you may have a defined scope or a specific testing requirement.

Scanning only the required ports can make the assessment more controlled.

---

## 11. Scanning All TCP Ports

Nmap's default scan does not necessarily test every possible TCP port.

To scan all TCP ports:

```bash
nmap -p- 192.168.1.10
```

Here:

```text
-p-
```

means all TCP ports from 1 through 65535.

This can take longer than a basic scan.

### Security Testing Perspective

A service running on an uncommon port can be easy to miss if you only check commonly used ports.

Therefore, when the scope and time allow it, comprehensive port discovery can be useful.

---

## 12. Service Version Detection

Finding an open port is only the beginning.

You may want to know what software is actually running.

Nmap provides service/version detection using:

```bash
nmap -sV 192.168.1.10
```

You may see output similar to:

```text
PORT     STATE   SERVICE  VERSION
22/tcp   open    ssh      OpenSSH
80/tcp   open    http     Apache
443/tcp  open    https    nginx
```

The version information can help with further investigation.

For example:

```text
Service
   ↓
Version
   ↓
Research
   ↓
Configuration review
   ↓
Security assessment
```

Do not treat a version number alone as proof of a vulnerability.

---

## 13. Operating System Detection

Nmap can also attempt to identify the target operating system.

Use:

```bash
nmap -O 192.168.1.10
```

The `-O` option enables OS detection.

Nmap may attempt to identify information such as:

```text
OS
Network characteristics
Device type
```

### Important

OS detection is an inference.

It may not always be accurate.

Therefore, treat the result as information to investigate, not absolute truth.

---

## 14. Combining Nmap Options

Nmap options can be combined.

For example:

```bash
nmap -sV -O 192.168.1.10
```

This requests:

```text
-sV → Service/version detection
-O  → OS detection
```

You can also specify ports:

```bash
nmap -sV -p 22,80,443 192.168.1.10
```

The important thing is to understand each option before combining it with others.

Do not use commands simply because you found them in a random tutorial.

---

## 15. Host Discovery

Before scanning ports, you may want to determine whether hosts are alive.

Nmap can perform host discovery using:

```bash
nmap -sn 192.168.1.0/24
```

This is commonly referred to as a **ping scan** or host discovery scan.

The purpose is to identify responsive hosts rather than perform a normal port scan.

Conceptually:

```text
Network
   ↓
Host Discovery
   ↓
Identify Live Hosts
   ↓
Port Scanning
   ↓
Service Enumeration
```

This is one example of how a structured assessment can progress.

---

## 16. Think Like a Security Tester

Imagine you have been given authorization to assess:

```text
192.168.1.10
```

Don't immediately start thinking:

> "How can I exploit this?"

First think:

> "What do I know about this host?"

Start with:

```bash
nmap 192.168.1.10
```

Suppose you discover:

```text
22/tcp   open   ssh
80/tcp   open   http
443/tcp  open   https
```

Now ask:

### Port 22

What is running?

```bash
nmap -sV -p 22 192.168.1.10
```

### Port 80

Is there a web application?

### Port 443

Is HTTPS configured?

### Next Question

What information do I still need?

This is the mindset we want to develop:

> **Discover → Understand → Enumerate → Analyze → Test**

Not:

> **Scan → Attack immediately**

---

## 17. Day 7 Practical Exercise

Perform this exercise only against:

- Your own machine
- Your own virtual machine
- An intentionally vulnerable lab
- A system for which you have explicit authorization

### Step 1 — Check Nmap

Run:

```bash
nmap --version
```

Confirm that Nmap is installed.

### Step 2 — Scan Your Localhost

Run:

```bash
nmap 127.0.0.1
```

Observe the results.

### Step 3 — Scan Specific Ports

Run:

```bash
nmap -p 22,80,443 127.0.0.1
```

Compare the output with the previous scan.

### Step 4 — Perform Service Detection

Run:

```bash
nmap -sV 127.0.0.1
```

Observe whether Nmap identifies any services.

### Step 5 — Scan All TCP Ports

If appropriate for your own lab:

```bash
nmap -p- 127.0.0.1
```

Compare the results with the basic scan.

### Step 6 — Record Your Findings

Create a simple table:

| Port | State | Service | Version |
|---|---|---|---|
|  |  |  |  |
|  |  |  |  |

Do not copy someone else's results.

Your results depend on your own system.

---

## 18. Day 7 Assignment

Complete the following tasks on your own system or an authorized lab.

### Task 1

Run:

```bash
nmap 127.0.0.1
```

Record the results.

### Task 2

Run:

```bash
nmap -sV 127.0.0.1
```

Identify any services and versions detected.

### Task 3

Run:

```bash
nmap -p 1-100 127.0.0.1
```

Compare this with the default scan.

### Task 4

Run:

```bash
nmap -p- 127.0.0.1
```

Compare the results with the previous scans.

### Task 5

Answer the following:

1. What is Nmap?
2. What is a port?
3. What does an open port mean?
4. What does `-sV` do?
5. What does `-O` do?
6. What does `-p-` mean?
7. Does an open port automatically mean a vulnerability exists?
8. Why is service enumeration important?

### Task 6 — Think

Suppose you discover:

```text
8080/tcp open
```

What questions would you ask next?

Write at least three questions.

---

## 19. Day 7 Quick Quiz

### Question 1

What does Nmap stand for?

A. Network Monitor  
B. Network Mapper  
C. Network Manager  
D. Network Machine

### Question 2

What is Nmap primarily used for?

A. Creating websites  
B. Network discovery and security auditing  
C. Editing images  
D. Managing databases

### Question 3

What does this command do?

```bash
nmap 192.168.1.10
```

A. Deletes the target  
B. Performs a basic Nmap scan  
C. Installs Nmap  
D. Starts a web server

### Question 4

What does `-sV` do?

A. Enables service/version detection  
B. Scans only port 22  
C. Detects the user's password  
D. Deletes scan results

### Question 5

What does `-O` attempt to identify?

A. Open files  
B. Operating system  
C. User accounts  
D. Web pages

### Question 6

What does `-p-` generally mean?

A. Scan only port 80  
B. Scan all TCP ports  
C. Scan only UDP ports  
D. Disable port scanning

### Question 7

Does an open port automatically mean a vulnerability exists?

A. Yes  
B. No

### Question 8

What should you do before scanning a system?

A. Obtain proper authorization  
B. Start exploiting immediately  
C. Disable the target  
D. Guess the administrator password

---

## 20. Quiz Answers

1. **B — Network Mapper**
2. **B — Network discovery and security auditing**
3. **B — Performs a basic Nmap scan**
4. **A — Enables service/version detection**
5. **B — Operating system**
6. **B — Scan all TCP ports**
7. **B — No**
8. **A — Obtain proper authorization**

---

## 21. Key Takeaways

Today you learned:

- What Nmap is
- Why Nmap is important
- What network scanning means
- How to identify targets
- How ports relate to services
- How to perform a basic Nmap scan
- How to scan specific ports
- How to scan all TCP ports
- How to identify services and versions
- How to perform basic OS detection
- How to perform host discovery
- How to interpret basic Nmap output
- Why an open port does not automatically mean a vulnerability
- How to approach scanning systematically

### Remember

> **Nmap helps you discover the attack surface. It does not automatically prove that the target is vulnerable.**

A security tester should move from:

```text
Discovery
    ↓
Port Identification
    ↓
Service Identification
    ↓
Version Identification
    ↓
Further Enumeration
    ↓
Security Testing
```

---

## ✅ Day 7 Completion Checklist

- [ ] I understand what Nmap is
- [ ] I understand network scanning
- [ ] I understand ports and services
- [ ] I performed a basic Nmap scan
- [ ] I scanned specific ports
- [ ] I understand `-sV`
- [ ] I understand `-O`
- [ ] I understand `-p-`
- [ ] I understand host discovery
- [ ] I can read basic Nmap output
- [ ] I understand that an open port is not automatically a vulnerability
- [ ] I completed the practical exercise
- [ ] I completed the assignment
- [ ] I completed the quiz

---

**Learn responsibly. Test ethically.**
