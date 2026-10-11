# 🔎 Day 8 — Nmap Scanning Techniques & Enumeration

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 8  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In Day 7, you learned the basics of Nmap.

Today, you will go one step deeper and understand how Nmap performs different types of scans.

You will learn:

- TCP and UDP scanning
- TCP connection scanning
- SYN scanning
- UDP scanning
- Host discovery
- Port states
- Service detection
- Scan timing
- Nmap output
- Saving scan results
- Comparing different scans
- Building a basic scanning methodology

The goal is not to memorize Nmap commands.

The goal is to understand:

> **Why would I use a particular scan, and what information does it provide?**

---

## 2. Why Do Different Nmap Scan Types Exist?

You might wonder:

> If Nmap can identify open ports, why do we need different scan types?

Different scans provide different information and behave differently.

For example:

```text
TCP Scan
    ↓
TCP ports

UDP Scan
    ↓
UDP ports

Service Detection
    ↓
Service + Version

OS Detection
    ↓
Possible Operating System
```

The correct scan depends on:

- Your objective
- Target environment
- Protocol
- Time available
- Scope of the assessment

A professional tester should understand the reason behind the scan.

---

## 3. TCP and UDP

Before learning different scans, let's review TCP and UDP.

### TCP

TCP is connection-oriented.

A simplified TCP connection uses:

```text
Client                Server

  SYN  ──────────────>
       <──────────── SYN-ACK
  ACK  ──────────────>
```

TCP provides reliable communication.

Common TCP-based services include:

- HTTP
- HTTPS
- SSH
- FTP
- SMTP

### UDP

UDP is connectionless.

It does not establish a TCP-style three-way handshake.

Common UDP-based services include:

- DNS
- DHCP
- NTP
- SNMP

This difference is important when performing network scanning.

---

## 4. Understanding Port States

Nmap does not simply tell you whether a port is "open" or "closed."

It can report several states.

The most important ones are:

| State | Meaning |
|---|---|
| Open | An application is accepting connections |
| Closed | The port is reachable but no application is listening |
| Filtered | Nmap cannot determine whether the port is open because filtering is interfering |
| Unfiltered | The port is accessible, but Nmap cannot determine whether it is open or closed for that scan type |

### Important

A filtered port does not necessarily mean:

> "There is no service."

It means Nmap could not determine the state because something such as a firewall or packet filter may be interfering.

---

## 5. TCP Connect Scan

A TCP Connect scan uses:

```bash
nmap -sT 127.0.0.1
```

This performs a TCP connection to the target ports.

Conceptually:

```text
Scanner
   |
   | TCP connection
   ↓
Target Port
   |
   ↓
Connection Result
```

If the connection succeeds, the port can be identified as open.

### Why Use It?

A TCP Connect scan can be useful when:

- You don't have the required privileges for other scan types
- You want a straightforward TCP connection-based scan

It is easy to understand because it uses a normal TCP connection.

---

## 6. SYN Scan

Another important scan is the TCP SYN scan:

```bash
nmap -sS 127.0.0.1
```

It is commonly called a **SYN scan** or **half-open scan**.

The basic concept is:

```text
Scanner                Target

   SYN  ───────────────>

        <────────────── SYN-ACK

   RST  ───────────────>
```

The scanner does not complete the normal TCP connection after receiving SYN-ACK.

### Why Is It Called a Half-Open Scan?

Because the full TCP connection is not completed.

The scanner uses the response to determine information about the port.

### Important

Do not think of `-sS` as "invisible."

Network monitoring and security controls can still detect scanning activity.

---

## 7. TCP Connect vs SYN Scan

Let's compare them.

| Feature | TCP Connect | SYN Scan |
|---|---|---|
| Option | `-sT` | `-sS` |
| TCP connection | Completes connection | Does not complete normal connection |
| Common use | Straightforward TCP scanning | Common TCP port scanning |
| Privileges | Generally easier | May require elevated privileges |
| Purpose | Identify TCP ports | Identify TCP ports |

The important lesson is:

> Different scan types can achieve similar goals using different techniques.

---

## 8. UDP Scanning

TCP is not the only protocol you need to investigate.

Some services use UDP.

Nmap can perform a UDP scan using:

```bash
nmap -sU 127.0.0.1
```

UDP scanning can be slower than TCP scanning.

This is because UDP does not have the same connection mechanism as TCP.

For example:

```text
TCP
Scanner → SYN → Target
Target  → SYN-ACK

UDP
Scanner → UDP probe → Target
             ↓
       Response may vary
```

A lack of response does not always mean the UDP port is closed.

This makes UDP scanning more complicated.

---

## 9. Why UDP Scanning Matters

Imagine you only perform TCP scanning.

You might discover:

```text
22/tcp   open   ssh
80/tcp   open   http
443/tcp  open   https
```

But the system could also have UDP services.

For example:

```text
53/udp
161/udp
```

These could potentially represent services such as DNS or SNMP.

Therefore:

> A TCP-only scan does not provide a complete picture of network exposure.

When the assessment scope requires it, UDP should also be considered.

---

## 10. Combining TCP and UDP Scanning

You can perform both TCP and UDP scans.

For example:

```bash
nmap -sS -sU 127.0.0.1
```

However, scanning all TCP and UDP ports can take considerable time.

A more controlled approach is to specify the ports you want to test.

Example:

```bash
nmap -sS -sU -p 53,80,443 127.0.0.1
```

The exact scan strategy should depend on the assessment requirements.

Do not automatically assume that:

```text
More scanning = Better assessment
```

A professional assessment balances:

- Coverage
- Scope
- Time
- Network impact
- Testing objectives

---

## 11. Host Discovery

Before scanning ports, you may want to identify which hosts are available.

Nmap can perform host discovery with:

```bash
nmap -sn 192.168.1.0/24
```

Conceptually:

```text
Network
   ↓
Host Discovery
   ↓
Live Hosts
   ↓
Port Scanning
```

For example:

```text
192.168.1.1   → Host detected
192.168.1.10  → Host detected
192.168.1.20  → Host detected
```

You can then investigate the discovered hosts within the authorized scope.

---

## 12. Service and Version Detection

Once you identify open ports, you usually want to know what is running behind them.

Use:

```bash
nmap -sV 127.0.0.1
```

Example:

```text
PORT     STATE   SERVICE   VERSION
22/tcp   open    ssh       OpenSSH
80/tcp   open    http      Apache
```

Now you have more useful information.

Instead of:

```text
80/tcp open
```

you may know:

```text
80/tcp open http Apache
```

This can help determine what should be investigated next.

### Remember

Service detection is not perfect.

Always validate important findings.

---

## 13. Nmap Timing

Nmap provides timing templates.

They range from:

```text
-T0
-T1
-T2
-T3
-T4
-T5
```

The general idea is:

```text
Slower
  ↓
T0
T1
T2
T3
T4
T5
  ↓
Faster
```

The default timing is:

```bash
-T3
```

A commonly used faster scan is:

```bash
-T4
```

### Important

Faster does not automatically mean better.

Increasing scan speed can:

- Generate more traffic
- Increase the chance of packet loss
- Affect accuracy in some environments
- Create more noticeable scanning activity

Use timing appropriate to the authorized environment.

---

## 14. Saving Nmap Results

During a real assessment, you should not rely only on terminal output.

Nmap can save results to files.

### Normal Output

```bash
nmap 127.0.0.1 -oN scan.txt
```

This saves normal output to:

```text
scan.txt
```

### XML Output

```bash
nmap 127.0.0.1 -oX scan.xml
```

XML output can be useful for tools that process Nmap results.

### All Major Formats

You can use:

```bash
nmap 127.0.0.1 -oA scan
```

This can create multiple output formats using the same base name.

### Why Save Results?

Saved results allow you to:

- Review findings later
- Compare scans
- Document assessment results
- Share evidence with authorized stakeholders

---

## 15. Comparing Scan Results

Suppose you perform:

```bash
nmap 127.0.0.1
```

and later:

```bash
nmap -sV 127.0.0.1
```

The second scan may provide more information.

You can compare:

```text
Basic Scan
    ↓
Open Ports

Service Detection
    ↓
Open Ports + Services + Possible Versions
```

This shows an important assessment principle:

> **Enumeration should gradually increase your understanding of the target.**

You should not collect information without knowing why you are collecting it.

---

## 16. Think Like a Security Tester

Imagine an authorized target:

```text
192.168.56.10
```

You begin with:

```bash
nmap 192.168.56.10
```

You discover:

```text
22/tcp   open   ssh
80/tcp   open   http
```

Your next question should not immediately be:

> "How do I exploit SSH?"

Instead:

### Step 1

Identify the services:

```bash
nmap -sV -p 22,80 192.168.56.10
```

### Step 2

Understand the results.

### Step 3

Determine what additional information is required.

### Step 4

Perform the next authorized assessment activity.

This creates a structured methodology:

```text
Discover
   ↓
Identify
   ↓
Enumerate
   ↓
Analyze
   ↓
Validate
```

---

## 17. Day 8 Practical Exercise

Perform these exercises only against your own system or an authorized lab.

### Step 1 — TCP Scan

Run:

```bash
nmap -sT 127.0.0.1
```

Record the results.

### Step 2 — SYN Scan

If your environment allows it, run:

```bash
sudo nmap -sS 127.0.0.1
```

Compare the results with the TCP Connect scan.

### Step 3 — UDP Scan

Run:

```bash
sudo nmap -sU --top-ports 20 127.0.0.1
```

Observe the results.

### Step 4 — Service Detection

Run:

```bash
nmap -sV 127.0.0.1
```

Record the services and versions identified.

### Step 5 — Save the Results

Run:

```bash
nmap -sV 127.0.0.1 -oN day8-scan.txt
```

Open the file:

```bash
cat day8-scan.txt
```

### Step 6 — Compare

Compare:

```text
TCP Connect Scan
SYN Scan
UDP Scan
Service Detection
```

Write down what was different.

---

## 18. Day 8 Assignment

Complete the following tasks on your own system or an authorized lab.

### Task 1

Explain the difference between:

```text
TCP
UDP
```

### Task 2

Explain the difference between:

```bash
-sT
```

and:

```bash
-sS
```

### Task 3

Explain why UDP scanning can be more difficult than TCP scanning.

### Task 4

Perform:

```bash
nmap -sV 127.0.0.1
```

Create a table:

| Port | Protocol | State | Service | Version |
|---|---|---|---|---|
| | | | | |
| | | | | |

### Task 5

Save a scan using:

```bash
nmap -sV 127.0.0.1 -oN day8-scan.txt
```

Verify that the file was created.

### Task 6

Answer:

> Why should a security tester save scan results instead of relying only on terminal output?

### Task 7

Explain the following Nmap states in your own words:

```text
Open
Closed
Filtered
```

---

## 19. Day 8 Quick Quiz

### Question 1

Which protocol is connection-oriented?

A. UDP  
B. TCP  
C. DNS  
D. ICMP

### Question 2

Which Nmap option performs a TCP Connect scan?

A. `-sS`  
B. `-sU`  
C. `-sT`  
D. `-sV`

### Question 3

Which Nmap option performs a SYN scan?

A. `-sS`  
B. `-sT`  
C. `-sU`  
D. `-O`

### Question 4

Which option is used for UDP scanning?

A. `-sT`  
B. `-sU`  
C. `-sV`  
D. `-sn`

### Question 5

What does a filtered port generally indicate?

A. The port definitely has no service  
B. Nmap cannot determine the state because filtering may be interfering  
C. The port is definitely vulnerable  
D. The operating system is Linux

### Question 6

What does `-sV` do?

A. Performs UDP scanning  
B. Performs OS detection  
C. Detects service/version information  
D. Saves output

### Question 7

What does this command do?

```bash
nmap -oN scan.txt 127.0.0.1
```

A. Deletes scan results  
B. Saves normal Nmap output to a file  
C. Scans only UDP  
D. Detects the operating system

### Question 8

Does a TCP-only scan necessarily identify all network services?

A. Yes  
B. No

---

## 20. Quiz Answers

1. **B — TCP**
2. **C — `-sT`**
3. **A — `-sS`**
4. **B — `-sU`**
5. **B — Nmap cannot determine the state because filtering may be interfering**
6. **C — Detects service/version information**
7. **B — Saves normal Nmap output to a file**
8. **B — No**

---

## 21. Key Takeaways

Today you learned:

- The difference between TCP and UDP
- Why different Nmap scan types exist
- TCP Connect scanning
- SYN scanning
- UDP scanning
- Host discovery
- Open, closed, and filtered ports
- Service/version detection
- Nmap timing
- Saving scan results
- Comparing scan results
- Building a structured scanning methodology

### Remember

> **Scanning is not about running the maximum number of commands. It is about collecting the right information for the assessment objective.**

A structured Nmap assessment can look like:

```text
Host Discovery
      ↓
Port Discovery
      ↓
Service Detection
      ↓
Version Identification
      ↓
Further Enumeration
      ↓
Security Validation
```

---

## ✅ Day 8 Completion Checklist

- [ ] I understand TCP and UDP
- [ ] I understand TCP Connect scanning
- [ ] I understand SYN scanning
- [ ] I understand UDP scanning
- [ ] I understand Nmap port states
- [ ] I understand service/version detection
- [ ] I understand Nmap timing
- [ ] I can save Nmap results
- [ ] I can compare different scan results
- [ ] I understand why UDP scanning is important
- [ ] I understand structured network enumeration
- [ ] I completed the practical exercise
- [ ] I completed the assignment
- [ ] I completed the quiz

---

**Learn responsibly. Test ethically.**
