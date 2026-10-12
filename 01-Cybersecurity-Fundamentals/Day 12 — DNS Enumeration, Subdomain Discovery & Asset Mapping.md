# 🔎 Day 12 — DNS Enumeration, Subdomain Discovery & Asset Mapping

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 12  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In Day 11, you learned reconnaissance, OSINT, DNS records, and the importance of assessment scope.

Today, you will learn how to investigate DNS information and organize discovered assets.

You will learn:

- What DNS enumeration means
- How DNS resolution works
- How to inspect common DNS records
- What subdomains are and why they matter
- How subdomain discovery works
- What a DNS zone transfer is
- How to use basic DNS enumeration commands
- How to create an asset inventory
- How to document and validate reconnaissance results

**Goal:** Understand DNS enumeration and learn how to organize discovered assets for an authorized security assessment.

---

## 2. What Is DNS Enumeration?

DNS enumeration is the process of collecting information about a domain through its Domain Name System records.

DNS information can help a security tester understand how an organization has configured its internet-facing services.

For example, DNS records may identify:

- IPv4 and IPv6 addresses
- Mail servers
- Authoritative name servers
- Aliases for other domain names
- Publicly configured subdomains

### Example

Consider this fictional domain:

`example.com`

Its DNS information might include:

```text
example.com          → Website address
mail.example.com     → Mail service
api.example.com      → API endpoint
portal.example.com   → Application portal
```

These are illustrative examples, not actual findings.

**Important:** DNS enumeration provides information about an environment. It does not automatically identify a vulnerability.

---

## 3. How Does DNS Resolution Work?

When you enter a domain name into a browser, your computer needs to determine where to send the request.

DNS helps resolve the domain name to an IP address.

```text
User enters a domain
         |
         v
Browser / Operating System
         |
         v
DNS Resolver
         |
         v
DNS Response
         |
         v
IP Address
         |
         v
Connection to the destination
```

For example:

```text
example.com
     |
     v
DNS Lookup
     |
     v
IP Address
```

The actual process may involve caching and multiple DNS servers. A cached answer may be returned without contacting the authoritative DNS server for every request.

---

## 4. Understanding Common DNS Record Types

You learned about DNS records in Day 11. Now let's understand how they help with asset mapping.

| Record | Purpose | Example use |
|---|---|---|
| A | Maps a name to an IPv4 address | Website address |
| AAAA | Maps a name to an IPv6 address | IPv6-enabled service |
| CNAME | Points a name to another domain name | Service alias |
| MX | Identifies mail servers | Email infrastructure |
| NS | Identifies authoritative name servers | DNS administration |
| TXT | Stores text data | Domain verification and email policies |
| SOA | Provides authoritative zone information | Zone administration |
| PTR | Supports reverse DNS lookups | Name associated with an IP address |

The examples describe record purposes, not proof that any service is vulnerable.

---

## 5. Performing Basic DNS Enumeration with `nslookup`

`nslookup` is a command-line tool for querying DNS.

### Look Up a Domain

```bash
nslookup example.com
```

This may display the DNS server used for the query and the resulting address information.

### Query a Specific Record Type

```bash
nslookup -type=A example.com
nslookup -type=MX example.com
nslookup -type=NS example.com
nslookup -type=TXT example.com
```

The available options may vary slightly by operating system.

### What Should You Observe?

Record:

- The queried domain
- The record type
- The response
- Whether a response was returned

A missing response does not necessarily mean a configuration problem. DNS behavior can depend on caching, filtering, record availability, and resolver configuration.

Use an instructor-provided domain or an authorized lab for the exercises.

---

## 6. Using `dig` for DNS Enumeration

`dig` is a DNS query utility commonly available on Linux and other Unix-like systems.

### Query an A Record

```bash
dig example.com A
```

### Query an AAAA Record

```bash
dig example.com AAAA
```

### Query MX Records

```bash
dig example.com MX
```

### Query Name Servers

```bash
dig example.com NS
```

### Display a Short Answer

```bash
dig +short example.com A
```

The `+short` option provides a more concise response.

### Understand the Output

A DNS response may include:

- Question section
- Answer section
- Authority section
- Additional section
- Query status

The **Answer** section commonly contains the records returned for the query.

The **Authority** section may identify servers or information relevant to the authority for the requested name.

---

## 7. Understanding DNS Query Results

Not every DNS query returns an IP address.

For example:

- An A query requests an IPv4 address.
- An MX query requests mail-server information.
- An NS query requests name-server information.
- A TXT query requests text records.

A DNS query can return no answer, an error, or a response indicating that the requested name does not exist.

Some common response codes include:

| Response | General meaning |
|---|---|
| NOERROR | The DNS query completed without a DNS-level error; the requested record may or may not exist |
| NXDOMAIN | The queried domain name does not exist |
| SERVFAIL | The server could not successfully complete the query |
| REFUSED | The server refused the query |

Always interpret the response in context. For example, `NOERROR` does not guarantee that the requested record exists.

---

## 8. What Is a Subdomain?

A subdomain is a name beneath another domain in the DNS hierarchy.

For example:

```text
example.com
    |
    ├── www.example.com
    ├── portal.example.com
    ├── api.example.com
    └── mail.example.com
```

Different subdomains may support different services.

| Subdomain | Possible purpose |
|---|---|
| `www.example.com` | Public website |
| `portal.example.com` | User portal |
| `api.example.com` | API service |
| `mail.example.com` | Mail-related service |

These are common naming patterns, not guaranteed functions.

A subdomain's name alone cannot prove which application it hosts. You must verify the information.

---

## 9. What Is Subdomain Discovery?

Subdomain discovery is the process of identifying subdomains associated with a domain.

Subdomains can be discovered through several methods:

- Reviewing official documentation
- Examining publicly available DNS information
- Reviewing public certificate records
- Checking approved asset inventories
- Using authorized subdomain discovery tools

### Example

You start with:

```text
example.com
```

Your authorized research identifies:

```text
www.example.com
portal.example.com
api.example.com
```

You then document each result and verify whether it belongs to the approved assessment scope.

**Remember:** Discovering a subdomain does not automatically authorize you to scan or test it.

---

## 10. Understanding Passive Subdomain Discovery

Passive subdomain discovery uses existing information sources rather than actively probing each discovered system.

Potential sources include:

- Public certificate transparency records
- Official technical documentation
- Publicly available DNS information
- Approved asset inventories
- Publicly documented service names

Passive sources can provide useful leads, but the information may be outdated or incomplete.

For example, a public record may mention a subdomain that no longer resolves.

Therefore, record the source and observation date, and distinguish between a discovered name and a currently verified asset.

---

## 11. Understanding Active Subdomain Discovery

Active subdomain discovery involves interacting with DNS infrastructure or authorized target systems to validate or discover names.

Examples include:

- Querying known candidate names against DNS
- Checking whether an approved subdomain resolves
- Using a permitted DNS enumeration tool
- Performing authorized validation of discovered records

Active techniques can generate traffic and may be logged.

Before performing them, confirm:

1. The domain is explicitly in scope.
2. The technique is permitted.
3. The testing rate is acceptable.
4. The activity will not disrupt services.

Do not use large-scale enumeration against an organization without explicit authorization.

---

## 12. What Is a DNS Zone Transfer?

A DNS zone transfer is a mechanism that allows DNS servers to copy DNS zone information between servers.

A common type is AXFR.

Zone transfers are used for legitimate DNS administration, such as synchronizing authoritative DNS servers.

If a server improperly allows unauthorized parties to transfer a zone, it may disclose DNS information that was not intended to be publicly available.

However, a zone transfer is not automatically a vulnerability. Its security significance depends on the server's role, intended configuration, and access controls.

For this beginner lesson, understand the concept before performing any zone-transfer test. Any active validation must be explicitly permitted by the system owner.

---

## 13. Understanding Reverse DNS

Reverse DNS looks up a name associated with an IP address, when a corresponding record exists.

It commonly uses PTR records.

For example, a reverse lookup may return a hostname associated with an IP address.

On systems with `dig`, the following command can perform a reverse lookup:

```bash
dig -x 192.0.2.10
```

The address `192.0.2.10` is reserved for documentation examples.

### Important Limitations

- Not every IP address has a PTR record.
- A returned hostname may be generic or outdated.
- Reverse DNS does not prove that a service is running.
- A PTR record does not prove ownership of a particular web application.

Treat reverse DNS as a source of clues, not conclusive evidence.

---

## 14. Understanding DNS and the Attack Surface

DNS information can help create an initial picture of an organization's public-facing assets.

For example:

```text
Approved Domain
      |
      v
DNS Information
      |
      v
Discovered Names
      |
      v
Asset Verification
      |
      v
Scope Validation
      |
      v
Asset Inventory
```

During this process, a security tester may identify:

- Names associated with web applications
- Mail-related infrastructure
- Public API names
- Aliases pointing to external services
- Assets that require clarification from the system owner

A DNS record is not a vulnerability by itself.

For example, a mail server appearing in an MX record is normal. Security relevance depends on its configuration and the assessment objectives.

---

## 15. Creating an Asset Inventory

An asset inventory is a structured list of identified systems and resources.

It helps security testers track what they have discovered and what still requires verification.

Use a table like this:

| Asset | Record or source | Result | Scope status |
|---|---|---|---|
| `example.com` | A record | Example address | Confirmed in scope |
| `portal.example.com` | Approved inventory | Portal name found | Needs verification |
| `api.example.com` | Public documentation | API name found | Needs scope confirmation |

The entries above are illustrative.

A useful inventory may also include:

- Discovery date
- Source
- Evidence
- Resolution status
- Confidence level
- Owner or contact, when provided through authorized channels
- Notes and follow-up actions

Do not guess missing information. Mark it as unknown or not verified.

---

## 16. Think Like a Security Tester

Imagine you have permission to assess a training domain.

You discover several DNS records and subdomain names.

Ask yourself:

1. Which DNS record type produced each result?
2. What does the result actually prove?
3. Is the information current?
4. Does the asset resolve to an address?
5. Is the asset included in the approved scope?
6. Is the discovered service owned by the organization or a third party?
7. What evidence supports the observation?
8. What information still needs verification?

A professional workflow looks like this:

```text
Authorized Domain
       |
       v
DNS Queries
       |
       v
Record Collection
       |
       v
Subdomain Discovery
       |
       v
Verification
       |
       v
Scope Review
       |
       v
Asset Inventory
```

The objective is to create a reliable, scoped inventory—not to collect names without understanding them.

---

## 17. Day 12 Practical Exercise

**Use only an instructor-provided lab or a domain for which you have explicit authorization.**

### Exercise 1 — Query DNS Records

Run the following commands against your approved lab domain:

```bash
nslookup example.com
```

If `dig` is available, run:

```bash
dig example.com A
dig example.com MX
dig example.com NS
dig example.com TXT
```

Replace `example.com` with your authorized lab domain.

Record the record type, response, and observation date.

### Exercise 2 — Compare Record Types

Explain what information each query requests:

- A
- AAAA
- MX
- NS
- TXT

Do not assume that every domain has every record type.

### Exercise 3 — Review Known Subdomains

Use an instructor-provided list or approved lab inventory.

For each subdomain, record:

- Subdomain name
- Discovery source
- Whether it currently resolves
- Whether it is in scope
- Any uncertainty

Do not scan or test a discovered host merely because its name appears in the list.

### Exercise 4 — Perform a Reverse DNS Lookup

If your lab provides an appropriate IP address and reverse DNS testing is permitted, run:

```bash
dig -x 192.0.2.10
```

Explain what a PTR record can tell you and what it cannot prove.

### Exercise 5 — Build an Asset Inventory

Create a table with at least five entries, where the lab provides enough information.

If fewer than five assets are available, document the assets that exist and note the limitation. Do not invent results.

---

## 18. Day 12 Assignment

Complete the following tasks using an authorized training environment.

### Task 1

Explain DNS enumeration in your own words.

### Task 2

Describe the purpose of these DNS record types:

- A
- AAAA
- CNAME
- MX
- NS
- TXT
- SOA
- PTR

### Task 3

Explain the difference between passive and active subdomain discovery.

### Task 4

Explain what a DNS zone transfer is and why access controls matter.

### Task 5

Create an asset inventory containing:

- Asset name
- Discovery source
- DNS record type, where applicable
- Observation
- Verification status
- Scope status

### Task 6

Write three reasons why a discovered subdomain should not automatically be treated as an authorized testing target.

**Submission guideline:** Use fictional or instructor-approved lab data. Do not publish confidential company assets, private assessment results, credentials, or sensitive infrastructure information in a public repository.

---

## 19. Day 12 Quick Quiz

### Question 1

What is DNS enumeration?

A. Encrypting a network connection  
B. Collecting information from DNS records  
C. Creating Linux users  
D. Capturing keyboard input

### Question 2

Which DNS record maps a name to an IPv4 address?

A. AAAA  
B. MX  
C. A  
D. TXT

### Question 3

Which record is commonly associated with mail-server information?

A. MX  
B. PTR  
C. CNAME  
D. SOA

### Question 4

What is a subdomain?

A. A type of IP address  
B. A name beneath another domain in the DNS hierarchy  
C. A network packet  
D. A Linux process

### Question 5

What is the purpose of a PTR record?

A. Reverse DNS lookup  
B. Email encryption  
C. TCP connection setup  
D. Port scanning

### Question 6

What does AXFR commonly refer to in DNS?

A. An HTTP request method  
B. A DNS zone-transfer mechanism  
C. An IP address format  
D. A Wireshark display filter

### Question 7

Does discovering a subdomain automatically mean it is in scope?

A. Yes  
B. No

### Question 8

What is the purpose of an asset inventory?

A. To record and organize identified assets  
B. To exploit every discovered host  
C. To disable DNS  
D. To hide network traffic

---

## 20. Quiz Answers

| Question | Answer | Explanation |
|---|---|---|
| 1 | B | DNS enumeration collects information from DNS records. |
| 2 | C | An A record maps a name to an IPv4 address. |
| 3 | A | MX records identify mail servers. |
| 4 | B | A subdomain is a name beneath another domain in the DNS hierarchy. |
| 5 | A | PTR records are used in reverse DNS. |
| 6 | B | AXFR is a DNS zone-transfer mechanism. |
| 7 | B | Scope must be confirmed before testing a discovered asset. |
| 8 | A | An inventory organizes assets, evidence, and verification status. |

---

## 21. Key Takeaways

By completing Day 12, you should understand:

- What DNS enumeration is
- How to query common DNS record types
- How to use `nslookup` and `dig`
- How subdomains fit into DNS
- The difference between passive and active discovery
- What DNS zone transfers are
- How reverse DNS works
- Why DNS results need verification
- How to create an asset inventory
- Why scope validation is essential

Remember:

**Discovery identifies potential assets. Verification establishes what the evidence supports. Scope determines what you are allowed to test.**

---

## Day 12 Completion Checklist

- [ ] I understand DNS enumeration.
- [ ] I can explain common DNS record types.
- [ ] I can perform basic DNS queries in my lab.
- [ ] I understand subdomains and their purpose.
- [ ] I can distinguish passive and active discovery.
- [ ] I understand DNS zone transfers and reverse DNS.
- [ ] I can organize findings in an asset inventory.
- [ ] I can distinguish verified assets from unverified leads.
- [ ] I understand why authorization and scope must be checked.

**Learn responsibly. Test ethically.**
