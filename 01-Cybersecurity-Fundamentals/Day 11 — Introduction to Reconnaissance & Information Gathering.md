# 🔎 Day 11 — Introduction to Reconnaissance & Information Gathering

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 11  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In previous lessons, you learned networking fundamentals, Linux commands, Nmap scanning, and Wireshark packet analysis.

Today, you will learn the first stage of a typical security assessment: **reconnaissance**.

You will learn:

- What reconnaissance means
- Why information gathering matters
- The difference between passive and active reconnaissance
- What an attack surface is
- How domains, IP addresses, and subdomains relate to a target
- How to collect information using public sources
- How to document findings professionally
- How to stay within an authorized testing scope

**Goal:** Learn how security testers gather and organize information before beginning a security assessment.

---

## 2. What Is Reconnaissance?

Reconnaissance is the process of gathering information about a target before performing a security assessment.

In simple words, it means **understanding the target before testing it**.

For example, before assessing a web application, a security tester may need to understand:

- The application's domain name
- Publicly available website information
- Technologies used by the application
- Publicly documented services
- Potential entry points
- The approved testing scope

### Simple Example

Imagine you have permission to assess:

`https://example.com`

Before testing the application, you may document:

| Information | Example |
|---|---|
| Domain | `example.com` |
| Website | `https://example.com` |
| Application type | Web application |
| Public documentation | Available or not found |
| Testing scope | Only the approved domain |
| Assessment status | Reconnaissance |

The examples in this lesson are illustrative. Use an organization or lab only when you have permission to assess it.

---

## 3. Why Is Reconnaissance Important?

Reconnaissance helps security testers plan an assessment instead of testing randomly.

It helps answer questions such as:

- What exactly am I authorized to test?
- Which applications are included in the scope?
- What information is already publicly available?
- Which technologies may be involved?
- What information needs further verification?
- Which areas should be prioritized for assessment?

### Without Reconnaissance

```text
Start Testing
     |
     ↓
Random Commands
     |
     ↓
Unorganized Results
```

### With Reconnaissance

```text
Confirm Authorization
        |
        ↓
Understand Scope
        |
        ↓
Gather Information
        |
        ↓
Organize Findings
        |
        ↓
Plan the Assessment
```

A structured approach improves efficiency and reduces the risk of testing an unauthorized system.

---

## 4. Understanding the Attack Surface

An attack surface is the collection of points through which a system may be accessed or interacted with.

For a web application, this may include:

- Login pages
- Registration forms
- APIs
- Publicly accessible services
- File-upload functionality
- Administrative interfaces
- External integrations

### Example

Consider a fictional online shopping application:

```text
Online Shopping Application
          |
    ┌─────┼──────┐
    |     |      |
  Login  Search  API
    |     |      |
 Account Products Orders
```

Each component may introduce different security considerations.

**Important:** Finding an entry point does not mean it is vulnerable. Vulnerability assessment requires appropriate testing and evidence.

---

## 5. Passive vs Active Reconnaissance

Reconnaissance is commonly divided into two categories.

### Passive Reconnaissance

Passive reconnaissance gathers information from sources without directly probing the target's systems.

Examples include:

- Reviewing a company's public website
- Reading public documentation
- Reviewing public security policies
- Searching publicly available domain-registration information
- Reviewing published technology documentation

### Active Reconnaissance

Active reconnaissance involves directly interacting with the target or its infrastructure to gather information.

Examples include:

- Sending network requests to an authorized website
- Checking DNS records
- Performing approved port scans
- Identifying services on an in-scope host

| Feature | Passive | Active |
|---|---|---|
| Direct interaction with target | Generally no | Yes |
| Typical sources | Public records and documents | Network responses and services |
| Detection by target | Usually less likely | More likely |
| Permission requirements | Respect privacy, laws, and applicable terms | Explicit authorization is essential |

Passive does not automatically mean risk-free, and active does not automatically mean malicious. The activity, target, data, and authorization all matter.

---

## 6. Understanding the Scope of an Assessment

Before gathering information, understand the **scope**.

Scope defines what you are permitted to assess.

A scope may specify:

- Approved domain names
- Approved IP addresses
- Approved applications
- Permitted testing techniques
- Testing dates and time windows
- Prohibited activities
- Reporting requirements

### Example Scope

```text
Approved Target:
https://app.example.test

Allowed:
- Review public pages
- Identify publicly exposed information
- Perform approved, low-impact checks

Not Allowed:
- Test unrelated domains
- Access real customer accounts without approval
- Perform denial-of-service testing
- Attempt unauthorized data extraction
```

The target above is illustrative.

**Never assume that permission to test one domain automatically includes its subdomains, associated APIs, hosting provider, or third-party services.** Confirm the exact scope before testing.

---

## 7. Understanding Domains and Subdomains

A domain identifies a name on the internet.

Example:

`example.com`

A subdomain is a name beneath another domain.

Examples:

- `www.example.com`
- `portal.example.com`
- `api.example.com`
- `mail.example.com`

A fictional organization may have different services hosted under different subdomains.

```text
example.com
    |
    ├── www.example.com
    ├── portal.example.com
    ├── api.example.com
    └── mail.example.com
```

These names may point to different systems or services.

However, discovering a subdomain does not automatically authorize testing it. Every discovered asset must be checked against the approved scope.

---

## 8. Understanding IP Addresses and Domains

A domain name and an IP address are related, but they are not the same thing.

### Domain Name

A human-readable name, such as:

`example.com`

### IP Address

A network address used to identify an endpoint for communication.

Example:

`192.0.2.10`

The address above belongs to a documentation range and is used here as an example.

DNS can help resolve a domain name to an IP address.

```text
Domain Name
     |
     ↓
    DNS
     |
     ↓
 IP Address
     |
     ↓
Network Communication
```

A domain may resolve to multiple IP addresses, and multiple domains may share an IP address. Therefore, an IP address alone does not always identify one specific application.

---

## 9. What Is OSINT?

OSINT stands for **Open-Source Intelligence**.

It is the process of collecting and analyzing information from publicly available sources.

In cybersecurity, OSINT can help an assessor understand the public information associated with an organization or an approved target.

Examples of sources include:

- Official websites
- Public technical documentation
- Published security policies
- Public DNS records
- Public certificate information
- Official engineering blogs
- Publicly available job descriptions

### Example

A company publishes a technical job advertisement mentioning that its applications use a particular framework.

A security researcher can record that information as a possible technology clue.

However, a job advertisement may be outdated or describe only one team. Treat OSINT findings as leads to verify, not confirmed vulnerabilities.

---

## 10. Common Information-Gathering Categories

During reconnaissance, information can be grouped into categories.

| Category | Example |
|---|---|
| Domain information | Domain name and DNS records |
| Web information | Public pages and documentation |
| Technology information | Publicly identified frameworks |
| Network information | Approved IP addresses and services |
| Certificate information | Publicly visible certificate details |
| Application information | Login pages and documented API endpoints |
| Organizational information | Public security contacts and policies |

Organizing information by category helps avoid duplicate work and makes reporting easier.

Only collect information relevant to the authorized assessment. Avoid gathering personal or sensitive information without a clear, approved need.

---

## 11. Understanding DNS Records

DNS records provide information about how domain names are configured.

Common DNS record types include:

| Record | Purpose |
|---|---|
| A | Maps a name to an IPv4 address |
| AAAA | Maps a name to an IPv6 address |
| CNAME | Points a name to another canonical name |
| MX | Identifies mail servers |
| NS | Identifies authoritative name servers |
| TXT | Stores text data used for various purposes, including domain verification |

These records may provide useful clues about an organization's internet-facing infrastructure.

For example, an MX record may identify the mail service responsible for handling email for a domain.

A DNS record is not proof of a security weakness. Its meaning depends on the configuration and context.

---

## 12. Using Basic DNS Lookup Commands

You have already learned Linux and networking commands. Now you can use them to understand DNS information in an authorized environment.

### Command 1 — `nslookup`

```bash
nslookup example.com
```

This requests DNS information for the specified name.

### Command 2 — `dig`

On Linux systems where `dig` is installed:

```bash
dig example.com
```

To request a particular record type:

```bash
dig example.com A
dig example.com MX
dig example.com NS
```

### Command 3 — `host`

On systems where the command is available:

```bash
host example.com
```

These commands may return different results depending on DNS configuration, caching, and the environment.

For this beginner exercise, use a domain provided by your lab or instructor. For any real organization, follow the approved scope and testing rules.

---

## 13. Understanding Public Website Information

A website itself can provide useful information without requiring intrusive testing.

During an authorized assessment, review the public pages that are within scope.

Look for:

- Main website pages
- Public documentation
- Login and registration pages
- Published API documentation
- Security contact information
- Privacy policies
- Publicly disclosed technology details

### Example Observation

```text
Target: Approved training application

Observation:
A public API documentation page is available.

Evidence:
The page URL and the date it was observed.

Security Significance:
The documentation may help identify intended API functionality.

Next Step:
Confirm whether the documentation and related API are included in the approved assessment scope.
```

Public documentation is not inherently a security issue. Its relevance depends on what it exposes and the assessment requirements.

---

## 14. Understanding HTTP Response Headers as Reconnaissance Data

HTTP response headers can provide technical information about a web application.

For example, a response may contain:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: no-cache
```

Headers can provide clues about:

- Content types
- Caching behavior
- Redirects
- Security-related browser policies
- Server behavior

A response may also contain a `Server` header, but that header can be absent, altered, or misleading.

Do not assume that a particular header proves a specific software version or vulnerability.

For now, focus on observing responses from your own application or an explicitly authorized training target.

---

## 15. Creating a Reconnaissance Report

Reconnaissance is more useful when the information is documented properly.

Use a simple table to record findings.

| Field | Description |
|---|---|
| Asset | Domain, IP address, or application |
| Source | Where the information was obtained |
| Observation | What was discovered |
| Evidence | Screenshot, command output, or reference |
| Confidence | Confirmed, likely, or unverified |
| Scope Status | In scope, out of scope, or needs confirmation |
| Notes | Follow-up questions or limitations |

### Example

| Field | Example |
|---|---|
| Asset | `app.example.test` |
| Source | Official training application |
| Observation | Public login page exists |
| Evidence | Page URL and observation date |
| Confidence | Confirmed |
| Scope Status | In scope |
| Notes | No security conclusion drawn yet |

A professional report separates **observed facts** from **assumptions**.

Do not label an asset vulnerable merely because it exists or uses a particular technology.

---

## 16. Think Like a Security Tester

Imagine that you have been authorized to assess a training web application.

Before testing, ask yourself:

1. What is the exact approved target?
2. Which domains and IP addresses are in scope?
3. What public information is available?
4. What do DNS records reveal?
5. Which application pages are publicly accessible?
6. Which observations are confirmed?
7. Which findings still require verification?
8. What activities are prohibited?
9. How will the collected evidence be documented?

A useful mindset is:

```text
Authorization
     ↓
Scope Verification
     ↓
Information Gathering
     ↓
Evidence Collection
     ↓
Validation
     ↓
Assessment Planning
```

The objective is not to collect as much information as possible. It is to collect **relevant, authorized, verifiable information**.

---

## 17. Day 11 Practical Exercise

**Perform this exercise using your own lab, an instructor-provided target, or another explicitly authorized environment.**

### Exercise 1 — Confirm the Scope

Write down:

- The approved domain or lab URL
- Any approved IP addresses
- The permitted activities
- The prohibited activities

If scope is unclear, stop and ask for clarification.

### Exercise 2 — Perform a Basic DNS Lookup

Using a lab domain provided by your instructor, run:

```bash
nslookup example.com
```

Or, if available:

```bash
dig example.com
```

Record the result without sharing sensitive network details publicly.

### Exercise 3 — Review Public Pages

Visit the approved training website.

Record:

- Main page
- Public documentation, if available
- Login page, if available
- Public security contact, if available

Do not attempt to log in using accounts that you do not own or have explicit permission to use.

### Exercise 4 — Identify Information Categories

Classify each observation as one of the following:

- Domain information
- DNS information
- Web application information
- Technology information
- Scope information

### Exercise 5 — Create a Reconnaissance Table

Use the reporting template from Section 15.

Record at least five observations, where available. If a particular type of information is not available, write **Not observed** rather than inventing a result.

---

## 18. Day 11 Assignment

Complete the following tasks using an authorized training target.

### Task 1

Explain reconnaissance in your own words.

### Task 2

Describe the difference between passive and active reconnaissance. Give two examples of each.

### Task 3

Explain the difference between a domain name and an IP address.

### Task 4

Explain the purpose of these DNS record types:

- A
- AAAA
- CNAME
- MX
- NS
- TXT

### Task 5

Create a reconnaissance report containing:

- Target
- Scope
- Information source
- Observation
- Evidence
- Confidence
- Scope status

### Task 6

Write three important rules that must be followed before performing active reconnaissance.

**Submission guideline:** Do not publish private company information, personal data, credentials, or confidential assessment results in a public repository. Use fictional or instructor-approved lab data in your learning project.

---

## 19. Day 11 Quick Quiz

### Question 1

What is reconnaissance?

A. Installing an operating system  
B. Gathering information about a target  
C. Encrypting network traffic  
D. Writing a firewall rule

### Question 2

Which is an example of passive reconnaissance?

A. Performing an approved port scan  
B. Sending test requests directly to a server  
C. Reviewing publicly available documentation  
D. Attempting to exploit a service

### Question 3

What does OSINT stand for?

A. Open-Source Intelligence  
B. Operating System Internal Network Tool  
C. Open Security Inspection Network Test  
D. Online System Information Node

### Question 4

Which DNS record is commonly used to map a domain name to an IPv4 address?

A. MX  
B. A  
C. TXT  
D. NS

### Question 5

What is the primary purpose of defining scope?

A. To guarantee that a target is vulnerable  
B. To determine what is authorized for testing  
C. To hide all network traffic  
D. To identify every user account

### Question 6

Does discovering a subdomain automatically authorize you to test it?

A. Yes  
B. No

### Question 7

Which command can perform a basic DNS lookup?

A. `nslookup`  
B. `chmod`  
C. `mkdir`  
D. `whoami`

### Question 8

If a technology is identified during reconnaissance, does that prove a vulnerability exists?

A. Yes  
B. No

---

## 20. Quiz Answers

| Question | Answer | Explanation |
|---|---|---|
| 1 | B | Reconnaissance gathers relevant information about a target. |
| 2 | C | Reviewing public documentation does not require directly probing the target. |
| 3 | A | OSINT means Open-Source Intelligence. |
| 4 | B | An A record maps a name to an IPv4 address. |
| 5 | B | Scope defines which targets and activities are authorized. |
| 6 | B | Discovered assets must be checked against the approved scope. |
| 7 | A | `nslookup` is used for DNS queries. |
| 8 | B | A technology clue needs further validation; it is not proof of a vulnerability. |

---

## 21. Key Takeaways

By completing Day 11, you should understand:

- What reconnaissance is and why it matters
- The difference between passive and active reconnaissance
- The meaning of attack surface
- The relationship between domains, subdomains, and IP addresses
- What OSINT means in cybersecurity
- The purpose of common DNS record types
- How to perform basic DNS lookups
- How to review public website information
- How to document observations and evidence
- Why scope verification is essential before active testing

Remember:

**Reconnaissance is about understanding the target, verifying information, and planning responsibly—not simply running commands.**

---

## Day 11 Completion Checklist

- [ ] I understand the purpose of reconnaissance.
- [ ] I can explain passive and active reconnaissance.
- [ ] I understand the importance of assessment scope.
- [ ] I can distinguish domain names from IP addresses.
- [ ] I understand common DNS record types.
- [ ] I can perform a basic DNS lookup in my lab.
- [ ] I can document reconnaissance observations.
- [ ] I can distinguish confirmed facts from assumptions.
- [ ] I understand that discovered assets are not automatically in scope.

**Learn responsibly. Test ethically.**
