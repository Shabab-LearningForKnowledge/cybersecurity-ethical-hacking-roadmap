# Day 15: Introduction to Web Application Security & OWASP Top 10

## 1. Introduction

Web applications are used for online banking, shopping, healthcare, education, social media, and many other services.

These applications often process sensitive information, including personal details, financial data, login credentials, and business records. If an application contains security weaknesses, an attacker may misuse its functionality, access unauthorized information, or disrupt its operations.

In this lesson, we will learn the fundamentals of web application security, understand common security risks, and explore the OWASP Top 10:2025.

**Learning objectives:**
- Understand what web application security means.
- Identify the difference between a feature and a security weakness.
- Learn the basic concepts behind common web vulnerabilities.
- Understand the OWASP Top 10:2025 categories.
- Develop a structured mindset for authorized web application security testing.

## 2. What Is Web Application Security?

Web application security is the process of identifying, assessing, and reducing security weaknesses in web applications.

It involves examining how an application handles:
- User input
- Authentication and authorization
- Sessions and cookies
- Sensitive information
- File uploads and downloads
- APIs and backend requests
- Errors and unexpected conditions
- Third-party software components

**Example:**

Imagine an online shopping application.

A normal user should be able to view their own orders. If the application allows that user to view another customer's order by changing an order identifier, it may indicate a broken access control vulnerability.

The application may look and function correctly while still containing a security weakness.

## 3. What Is a Web Vulnerability?

A web vulnerability is a weakness in an application's design, implementation, configuration, or dependencies that could be exploited to affect confidentiality, integrity, or availability.

Consider these examples:

| Application feature | Possible security weakness |
|---|---|
| Login page | Weak authentication controls |
| User profile | Unauthorized access to another user's data |
| Search box | Unsafe handling of user input |
| File upload | Insufficient validation of uploaded files |
| Error page | Disclosure of sensitive technical information |
| Payment process | Incorrect validation of transaction values |

**Important:** A feature is not automatically vulnerable just because it accepts input or performs an action. A security tester must gather evidence that a weakness exists.

## 4. The CIA Triad in Web Security

The CIA triad describes three important security objectives.

### Confidentiality
Information should only be accessible to authorized users.

**Example:** A customer must not be able to view another customer's bank statements.

### Integrity
Information should not be changed by unauthorized parties.

**Example:** A customer must not be able to manipulate the amount of a payment through an untrusted request parameter.

### Availability
Systems and services should remain available to authorized users.

**Example:** A web application should handle requests safely without being easily disrupted by malformed input.

These three principles help us understand the potential impact of a vulnerability.

## 5. Understanding the Attacker's Perspective

An ethical security tester examines an application from the perspective of someone attempting to misuse its functionality.

For example, when testing a profile page, consider:

- Can an unauthenticated user access the page?
- Can one authenticated user access another user's information?
- Does the server validate submitted values?
- Can a user modify information they are not permitted to change?
- Are sensitive details exposed in responses?
- Does the application handle unexpected input safely?

The objective is not simply to make a page behave unexpectedly. It is to determine whether the behavior represents a genuine security weakness and what its impact could be.

## 6. What Is OWASP?

OWASP stands for **Open Worldwide Application Security Project**.

It is a nonprofit foundation that provides resources, guidance, tools, and educational material to help improve software security.

OWASP publishes several useful projects, including:

- **OWASP Top 10:** Awareness of major web application security risks.
- **OWASP Web Security Testing Guide (WSTG):** Guidance for testing web application security.
- **OWASP Juice Shop:** An intentionally vulnerable application for security education and practice.

Official website: https://owasp.org/

## 7. What Is the OWASP Top 10?

The OWASP Top 10 is an awareness document highlighting major web application security risk categories.

It helps developers, security testers, and organizations understand important application security risks.

The **OWASP Top 10:2025** contains the following ten categories:

1. A01:2025 – Broken Access Control
2. A02:2025 – Security Misconfiguration
3. A03:2025 – Software Supply Chain Failures
4. A04:2025 – Cryptographic Failures
5. A05:2025 – Injection
6. A06:2025 – Insecure Design
7. A07:2025 – Authentication Failures
8. A08:2025 – Software or Data Integrity Failures
9. A09:2025 – Security Logging and Alerting Failures
10. A10:2025 – Mishandling of Exceptional Conditions

Official reference: https://owasp.org/Top10/

Remember that the OWASP Top 10 is an awareness and prioritization resource, not a complete list of every possible vulnerability. An application can contain risks outside these categories.

## 8. A01:2025 – Broken Access Control

Access control determines what an authenticated or unauthenticated user is permitted to access or perform.

Broken access control occurs when the application fails to enforce those permissions correctly.

**Example:**

A shopping application uses this endpoint:

`GET /api/orders/1001`

A user can access their own order. If changing `1001` to `1002` exposes another customer's order without proper authorization, this may indicate broken access control.

**What should a tester check?**
- Can users access only their own records?
- Are administrative functions restricted?
- Does the server enforce permissions on every relevant request?
- Are direct API requests protected, not just the user interface?

**Key lesson:** Hiding a button in the frontend is not a substitute for server-side authorization.

## 9. A02:2025 – Security Misconfiguration

Security misconfiguration occurs when an application, server, framework, cloud service, or related component is configured insecurely.

**Examples:**
- Unnecessary services or features are enabled.
- Default credentials remain active.
- Sensitive debug information is exposed.
- Security headers are missing or incorrectly configured.
- Directory listings expose files that should not be publicly accessible.
- Error messages reveal unnecessary internal details.

**What should a tester check?**
- Are unnecessary features exposed?
- Are error responses appropriately handled?
- Are security settings configured correctly?
- Is sensitive information exposed through publicly accessible resources?

A missing security header may be worth reporting, but its severity depends on the header, application context, and actual impact.

## 10. A03:2025 – Software Supply Chain Failures

Modern web applications depend on external libraries, packages, frameworks, build tools, and software distribution systems.

A software supply chain failure occurs when weaknesses or compromises in these dependencies or processes affect the application's security.

**Example:**

An application uses a third-party library containing a known vulnerability. If the vulnerable component is present in a relevant execution path, an attacker may be able to exploit it.

**What should a tester check?**
- Are outdated or vulnerable components being used?
- Are dependencies obtained from trusted sources?
- Are software versions identifiable?
- Are dependency and build processes protected against tampering?

Finding an outdated library does not automatically prove that the application is exploitable. Verify the version, vulnerability, affected functionality, and practical impact.

## 11. A04:2025 – Cryptographic Failures

Cryptographic failures occur when sensitive information is not adequately protected through appropriate cryptographic controls.

**Examples:**
- Sensitive data is transmitted without suitable encryption.
- Passwords are stored in plaintext.
- Weak or inappropriate cryptographic algorithms are used.
- Encryption keys are exposed or managed insecurely.
- Sensitive information is protected using an unsuitable cryptographic design.

**What should a tester check?**
- Is HTTPS used appropriately?
- Is sensitive information exposed in requests, responses, or storage?
- Are passwords handled using suitable password-hashing mechanisms?
- Are cryptographic secrets exposed in client-side code or public repositories?

HTTPS is important, but it does not automatically make the application secure. Authorization, input handling, session management, and other controls still matter.

## 12. A05:2025 – Injection

Injection vulnerabilities occur when untrusted input is interpreted as part of a command or query rather than being handled strictly as data.

Common examples include:
- SQL injection
- Command injection
- Cross-site scripting (XSS)
- Other forms of unsafe input interpretation

**Simple conceptual example:**

An application builds a database query by directly joining user input into a SQL statement. If the application fails to handle that input safely, the input may alter the intended query.

**What should a tester check?**
- How does the application handle unexpected input?
- Is input safely processed by the relevant component?
- Are database queries parameterized?
- Is untrusted content safely encoded before being displayed in a browser?
- Are appropriate validation and output-handling controls implemented?

**Key lesson:** Input validation is useful, but it is not a universal replacement for context-appropriate encoding, parameterized queries, and safe APIs.

Only perform injection testing against systems you own or have explicit permission to assess.

## 13. A06:2025 – Insecure Design

Insecure design occurs when an application's security requirements, workflows, or fundamental design decisions do not adequately address foreseeable threats.

Unlike a simple coding mistake, a design weakness may exist even when the application is implemented as intended.

**Example:**

An online store allows a user to change a product's price in a browser request, and the backend accepts that value without independently calculating or validating the price.

The problem is not merely the frontend field. The transaction design incorrectly trusts an untrusted value.

**What should a tester check?**
- Are business rules enforced on the server?
- Are sensitive transactions validated independently?
- Are important security requirements defined?
- Are abuse cases considered during design?
- Can a user bypass a required business step?

A good security assessment tests business logic, not just technical configuration.

## 14. A07:2025 – Authentication Failures

Authentication verifies a user's identity. Authentication failures occur when identity verification or related account protections are implemented incorrectly.

**Examples:**
- Weak account recovery processes.
- Inadequate protection against repeated login attempts.
- Poor session invalidation after logout or password changes.
- Insecure authentication tokens.
- Incorrect implementation of multifactor authentication.

**What should a tester check?**
- Are login and recovery processes properly protected?
- Are sessions invalidated when required?
- Are authentication tokens handled securely?
- Are authentication errors designed appropriately?
- Are account security controls implemented consistently?

Authentication and authorization are different. Successfully logging in does not mean a user should have access to every resource.

## 15. A08:2025 – Software or Data Integrity Failures

This category concerns failures to maintain the integrity and trustworthiness of software or data.

**Examples:**
- An application trusts a sensitive value without verifying its source.
- A software update process does not adequately verify authenticity.
- Critical data can be modified without appropriate integrity checks.
- A serialized object or workflow value is trusted without suitable validation.

**What should a tester check?**
- Are important data changes validated on the server?
- Are software updates verified using trusted integrity mechanisms?
- Are critical operations protected against unauthorized modification?
- Does the application trust client-controlled values that should be verified independently?

The main question is whether the application can distinguish trustworthy information from untrusted or manipulated information.

## 16. A09:2025 – Security Logging and Alerting Failures

Logging records relevant events. Alerting helps responsible teams identify events that require investigation or action.

Failures in this area may allow suspicious activity to go unnoticed or make investigations difficult.

**Examples:**
- Important security events are not recorded.
- Logs do not contain enough context for investigation.
- Sensitive information, such as passwords or session tokens, is recorded unnecessarily.
- Critical security events do not trigger appropriate alerts.
- Logs can be modified or accessed by unauthorized users.

**What should a tester check?**
- Are important authentication and authorization events recorded appropriately?
- Are logs protected against unauthorized access or modification?
- Is sensitive information excluded or safely handled?
- Are relevant security alerts generated where required?

A web application security tester should assess logging only within the agreed scope and available evidence. Do not assume that an event is unlogged merely because it is not visible in the browser.

## 17. A10:2025 – Mishandling of Exceptional Conditions

Applications encounter unexpected conditions such as unavailable services, invalid data, timeouts, resource failures, and interrupted transactions.

Mishandling these conditions can cause security weaknesses when the application fails unsafely.

**Examples:**
- An error causes an operation to bypass a required permission check.
- A failed payment process leaves inconsistent transaction records.
- An unexpected exception reveals sensitive internal details.
- A security check fails open, allowing an operation to proceed when it should be denied.

**What should a tester check?**
- Does the application fail safely when a dependency is unavailable?
- Are errors handled without exposing sensitive information?
- Are partial transactions managed consistently?
- Do failed security checks deny the operation where appropriate?
- Can an unexpected condition bypass a business rule?

**Key lesson:** Test not only the normal workflow, but also how the application behaves when something goes wrong.

## 18. How to Approach a Basic Web Application Security Assessment

A structured assessment is more effective than randomly testing inputs.

### Step 1: Confirm authorization and scope
Identify the approved application, test accounts, permitted methods, and any testing restrictions.

### Step 2: Understand the application
Explore its pages, functions, user roles, and major workflows.

### Step 3: Observe requests and responses
Use browser Developer Tools or an authorized interception proxy to understand HTTP communication.

### Step 4: Identify trust boundaries
Determine which values come from the browser, which decisions belong to the server, and which data must be protected.

### Step 5: Form a security question
For example: “Does the server verify that this user is authorized to view this record?”

### Step 6: Test safely
Use controlled inputs and approved test accounts. Avoid destructive changes and unauthorized access to real users' information.

### Step 7: Validate the result
Distinguish a confirmed vulnerability from a theoretical concern or a tool-generated alert.

### Step 8: Document evidence
Record the affected feature, expected behavior, observed behavior, impact, and recommended remediation.

## 19. Practical Exercise: Build a Web Security Observation Sheet

Use an application specifically intended for security practice, such as OWASP Juice Shop running in your own lab, or another explicitly authorized test environment.

Do not test public websites without permission.

Create a table like this:

| Feature | Security question | Observation |
|---|---|---|
| Login | Does authentication behave as expected? | Record the normal behavior |
| User profile | Are records restricted to the correct user? | Record the access rules |
| Search | How is user input handled? | Record validation and output behavior |
| Error page | Does the response expose unnecessary details? | Record the visible response |
| Session | What happens after logout? | Record the session behavior |
| Business workflow | Are important values checked by the server? | Record the observed controls |

### Exercise instructions

1. Open the authorized lab application.
2. Browse its main features normally.
3. Open your browser's Developer Tools and inspect the Network panel.
4. Select a request and identify its method, URL, status code, and relevant headers.
5. Note which values are supplied by the client.
6. For each feature, write one security question.
7. Record observations without assuming that an unusual response is automatically a vulnerability.

The goal is to practise systematic observation and security reasoning, not to exploit the application.

## 20. Assignment and Knowledge Check

### Assignment

Prepare a short report containing:

- The name of the authorized lab application.
- Five features you observed.
- One security question for each feature.
- The relevant OWASP Top 10:2025 category, if applicable.
- Your observation and supporting evidence.
- One possible remediation recommendation for any confirmed issue.

If you do not confirm a vulnerability, report the observation honestly rather than inventing a finding.

### Quiz

**Q1. What is the primary purpose of web application security?**

A. To improve website colours  
B. To identify and reduce security weaknesses  
C. To increase the number of web pages  
D. To replace application testing  

**Q2. A user accesses another customer's order without permission. Which category is most relevant?**

A. Broken Access Control  
B. Cryptographic Failures  
C. Software Supply Chain Failures  
D. Security Logging and Alerting Failures  

**Q3. What does authentication verify?**

A. A user's identity  
B. A server's physical location  
C. A database's size  
D. A browser's screen resolution  

**Q4. Is HTTPS alone proof that a web application is secure?**

A. Yes  
B. No  

**Q5. What is the purpose of the OWASP Top 10?**

A. To list every possible vulnerability  
B. To provide awareness of major web application security risks  
C. To replace authorization checks  
D. To guarantee that an application has no vulnerabilities  

**Q6. A database query is constructed unsafely using user input. Which category may be relevant?**

A. Injection  
B. Security Logging and Alerting Failures  
C. Software Supply Chain Failures  
D. Broken Access Control only  

**Q7. Why should a security tester validate a scanner finding?**

A. Every scanner finding is a confirmed vulnerability  
B. Validation helps distinguish genuine issues from false positives  
C. Validation is unnecessary  
D. Scanner findings never matter  

### Answers

1. B — To identify and reduce security weaknesses.
2. A — Broken Access Control.
3. A — A user's identity.
4. B — No. HTTPS protects communication in transit but does not guarantee application security.
5. B — Awareness of major web application security risks.
6. A — Injection.
7. B — Validation helps confirm whether a real vulnerability exists.

## 21. Key Takeaways

By completing Day 15, you should understand that:

- Web application security protects data, functionality, and business processes.
- A vulnerability is a security weakness, not simply unusual application behaviour.
- Confidentiality, integrity, and availability are important security objectives.
- OWASP provides widely used application security learning resources.
- The OWASP Top 10:2025 contains ten major web application security risk categories.
- Authentication verifies identity; authorization determines permitted actions.
- Security testing requires observation, controlled testing, validation, and clear evidence.
- Findings should be reported accurately, with realistic impact and remediation guidance.
- Testing must remain within explicitly authorized scope.

### Completion Checklist

- [ ] I can explain web application security in simple terms.
- [ ] I understand the CIA triad.
- [ ] I can explain the purpose of the OWASP Top 10.
- [ ] I can identify the ten OWASP Top 10:2025 categories.
- [ ] I understand the difference between authentication and authorization.
- [ ] I can formulate basic security questions about web application features.
- [ ] I can record observations without assuming every unusual behaviour is a vulnerability.
- [ ] I understand why scope, validation, and evidence matter.

**Learn responsibly. Test ethically.**
