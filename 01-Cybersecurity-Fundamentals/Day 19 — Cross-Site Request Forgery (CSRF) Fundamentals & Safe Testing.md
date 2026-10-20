# Day 19 — Cross-Site Request Forgery (CSRF) Fundamentals & Safe Testing

## 1. Introduction

Modern web applications allow users to perform actions such as updating account details, changing passwords, managing addresses, and making purchases.

When a user logs in, the browser may store a session cookie that is automatically included in certain requests to the application.

But what happens if a malicious website causes the user's browser to send an unwanted request to an application where the user is already authenticated?

If the application cannot distinguish the legitimate action from the forged request, a security weakness known as **Cross-Site Request Forgery (CSRF)** may occur.

In this lesson, we will understand how CSRF works, why session cookies matter, how to recognize potentially vulnerable workflows, and how to assess CSRF protections safely in an authorized lab.

**Learning objectives:**
- Understand what CSRF is.
- Learn how browsers send cookies with requests.
- Understand the difference between CSRF, XSS, and IDOR.
- Learn about CSRF tokens and SameSite cookies.
- Identify state-changing operations that require protection.
- Practise reviewing CSRF protections in an authorized lab.

## 2. What Is CSRF?

CSRF stands for **Cross-Site Request Forgery**.

It is a vulnerability in which an attacker induces a user's browser to send an unwanted request to a website where the user is authenticated.

If the application accepts the request without adequate protections, an action may be performed using the victim's existing authenticated session.

Potential examples include:
- Changing an account email address
- Updating a delivery address
- Changing account settings
- Submitting an unauthorized transaction, where the application's design permits it

The actual impact depends on the operation, the application's security controls, the user's privileges, and whether additional verification is required.

**Important:** CSRF does not automatically allow an attacker to read the application's private responses. The core issue is the unauthorized triggering of an action.

## 3. How Does CSRF Work?

Consider a user who is logged in to an online application.

A simplified scenario looks like this:

```text
User Logs In to Trusted Website
             |
             v
Browser Stores Session Cookie
             |
             v
User Visits Another Website
             |
             v
Browser Is Induced to Send a Request
             |
             v
Trusted Website Receives the Request
             |
             v
Missing or Ineffective CSRF Protections?
             |
             v
Potential Unauthorized Action
```

This scenario depends on the browser's request rules, cookie attributes, and the target application's security controls.

A request being sent does not automatically mean CSRF is possible. The application must also fail to verify that the action was legitimately initiated.

## 4. Why Are Cookies Important in CSRF?

Web applications commonly use session cookies to identify an authenticated session.

For example:

```http
Cookie: sessionid=example-session-value
```

This is an illustrative value, not a real credential.

When a browser sends a request to a website, it may automatically attach cookies that match the request's domain, path, security, and SameSite rules.

That behaviour is useful because users do not need to log in again for every request.

However, if the application relies only on the presence of a session cookie to authorize a state-changing request, it may be vulnerable to CSRF.

**Key lesson:** A valid session cookie proves that a request is associated with a session; it does not necessarily prove that the user intentionally initiated the action.

## 5. What Is a State-Changing Request?

A state-changing request modifies data or performs an action.

Examples include:
- Updating a profile
- Changing a password
- Adding a payment recipient
- Submitting an order
- Changing account permissions

A request that only retrieves information is commonly called a read-only request, although its real behaviour must be checked.

Applications should not use ordinary GET requests for sensitive state-changing operations. Safe HTTP methods should not produce unintended business changes.

For example:

```http
GET /account
```

This would normally retrieve an account page.

A state-changing operation might instead use:

```http
POST /account/update-email
```

The method alone does not guarantee security. POST requests can also be vulnerable to CSRF if the application does not implement appropriate protections.

## 6. A Simple CSRF Scenario

Imagine a user is logged in to a website that lets them update their account email address.

The application accepts a request containing the new email address and relies on the session cookie to identify the user.

If the application does not verify that the request was legitimately initiated by its own interface, an attacker may be able to induce the user's browser to submit an unwanted update.

A secure implementation should verify the request using appropriate anti-CSRF controls and enforce all relevant authorization and business rules.

This example illustrates the concept. It is not a claim that any particular application is vulnerable.

## 7. What Is a CSRF Token?

A CSRF token is a value used to help verify that a state-changing request originated through an expected application workflow.

A simplified flow:

1. The application provides a form or page containing a CSRF token.
2. The browser submits the token with a state-changing request.
3. The server validates the token.
4. The server processes the action only if the required checks succeed.

Illustrative request:

```http
POST /account/update-email
Content-Type: application/x-www-form-urlencoded

email=user@example.test&csrf_token=example-token
```

The example token is fictional.

A token should be unpredictable and validated correctly. Depending on the application's architecture, tokens may be associated with a session or implemented through another suitable design.

**Important:** Simply including a parameter named `csrf_token` does not prove that the protection is effective. The server must validate it appropriately.

## 8. What Makes CSRF Token Validation Weak?

Possible implementation problems include:
- The server does not validate the token.
- The token is optional when it should be required.
- The token is predictable.
- The token is not correctly associated with the relevant session or request.
- The server accepts an invalid token.
- The token is reused in a way that violates the intended security design.

During an authorized assessment, the tester should determine whether the token is genuinely enforced rather than simply checking whether a token field exists.

Use designated lab accounts and controlled test data. Do not attempt to change another person's real account settings.

## 9. What Is the SameSite Cookie Attribute?

The `SameSite` cookie attribute controls when a browser sends a cookie in cross-site request contexts.

Common settings include:

- **Strict:** Generally restricts the cookie from being sent in cross-site contexts.
- **Lax:** Allows the cookie in certain cross-site situations, commonly including some top-level navigations using safe methods.
- **None:** Allows cross-site cookie use, subject to browser requirements; cookies using this setting generally require `Secure`.

Example:

```http
Set-Cookie: sessionid=example-value; Secure; HttpOnly; SameSite=Lax; Path=/
```

SameSite can reduce CSRF risk, but it is not a universal substitute for proper request validation.

Its effectiveness depends on the application workflow, browser behaviour, cookie configuration, and the type of request involved.

## 10. What Are Origin and Referer Headers?

Browsers may include headers that provide information about the origin or referring page of a request.

Example:

```http
Origin: https://app.example.test
Referer: https://app.example.test/settings
```

These are illustrative values.

An application may validate the `Origin` header and, where appropriate, use the `Referer` header as an additional signal to help determine whether a request originated from an expected location.

However:
- Headers may be absent in some legitimate requests.
- Validation must account for the application's architecture.
- A substring check is not a reliable origin validation strategy.
- These headers should not be treated as universal proof of user intent.

A robust design uses suitable, layered protections rather than depending on a single signal.

## 11. CSRF vs. XSS vs. IDOR

These vulnerabilities affect different security properties.

| Vulnerability | Main issue | Example |
|---|---|---|
| CSRF | An unwanted request is accepted through a user's browser | A forged request changes an account setting |
| XSS | Untrusted content is interpreted as executable content in a browser | Unsafe comment rendering enables script execution |
| IDOR | A referenced object is accessible without proper authorization | A user can view another customer's order |

The vulnerabilities can sometimes interact, but they are not interchangeable.

For example, XSS may undermine some CSRF protections because script executing in the application's origin may be able to interact with the application's pages. The precise impact depends on the controls in place.

## 12. How Can Developers Prevent CSRF?

Effective protection depends on the application's authentication method and architecture.

### Use CSRF tokens

Require and validate appropriate tokens for relevant state-changing requests.

### Configure SameSite cookies appropriately

Choose cookie settings that support the application while limiting unnecessary cross-site cookie transmission.

### Validate request origins where appropriate

Apply robust origin validation as an additional control, taking account of legitimate application flows.

### Avoid state-changing GET requests

Use appropriate HTTP methods and ensure that ordinary navigation or resource retrieval does not trigger sensitive changes.

### Require additional verification for sensitive actions

Depending on the risk, actions such as changing authentication details or initiating high-impact transactions may require reauthentication or another suitable confirmation.

### Enforce authorization on the server

CSRF protection does not replace authorization. The server must still verify that the user has permission to perform the requested operation.

## 13. Understanding Login CSRF and Other Edge Cases

CSRF is not limited to actions performed after an ordinary login.

Some applications may have workflows in which an unwanted request affects the account or identity under which a user becomes authenticated. This is sometimes called login CSRF.

Other complex workflows may involve:
- Multiple application subdomains
- Third-party identity providers
- Embedded forms
- Cross-origin integrations
- API authentication using cookies

These cases require careful analysis of the complete authentication flow and browser behaviour.

Do not assume that a protection designed for one request type automatically protects every workflow.

## 14. How to Assess CSRF in an Authorized Security Test

Follow a structured approach.

### Step 1: Confirm authorization

Identify the approved application, test accounts, permitted endpoints, and testing restrictions.

### Step 2: Identify state-changing operations

Review forms and requests that update settings, create records, or perform other sensitive actions.

### Step 3: Understand the normal workflow

Perform the action normally and inspect the relevant request using browser Developer Tools or an authorized interception proxy.

### Step 4: Review existing protections

Observe whether the application uses CSRF tokens, SameSite cookies, origin validation, or other relevant controls.

### Step 5: Verify enforcement safely

Within the lab's rules, assess how the server responds when the required anti-CSRF protection is missing or invalid.

### Step 6: Evaluate the result

Determine whether the operation is actually protected, whether the request is rejected as expected, and what evidence supports the conclusion.

### Step 7: Document the finding

Record the affected operation, required conditions, observed behaviour, impact, and recommended remediation.

Avoid submitting unwanted transactions, changing real users' settings, or testing outside the approved scope.

## 15. Practical Exercise: Inspect CSRF Protections

**Environment:** Use an intentionally vulnerable training application or a dedicated local test application. PortSwigger Web Security Academy provides CSRF learning material and controlled labs.

Learning resource: https://portswigger.net/web-security/csrf

Do not test public websites without explicit authorization.

### Part A: Inspect a normal request

1. Open the authorized lab application.
2. Sign in using a designated test account.
3. Navigate to a profile or settings page.
4. Open Developer Tools and select the Network panel.
5. Perform a permitted test action.
6. Inspect the request method, body, cookies, and relevant headers.
7. Record any anti-CSRF token or other protection that is visible.

Do not copy real credentials, cookies, or tokens into your report.

### Part B: Review the controls

1. Identify whether the request changes application data.
2. Determine which protection appears to be used.
3. Review the application's expected behaviour for missing or invalid protection.
4. Follow the training lab's instructions to test the protection in a controlled way.
5. Record the actual response.
6. Do not assume that a visible token proves server-side validation.

### Part C: Document the outcome

Prepare a table:

| Test item | Observation |
|---|---|
| State-changing operation | Record the feature |
| HTTP method | Record the method |
| CSRF token present | Yes, no, or not applicable |
| Cookie SameSite setting | Record the observed value |
| Origin validation | Evidence available or unknown |
| Invalid protection test | Record actual result |
| Conclusion | Confirmed, not confirmed, or inconclusive |

If the available evidence is insufficient, record the result as inconclusive rather than claiming a vulnerability.

## 16. Common Beginner Mistakes

**Mistake 1: Assuming every POST request is CSRF-protected**

POST is not an anti-CSRF mechanism. Appropriate request validation is still required.

**Mistake 2: Assuming a token's presence proves security**

The server must validate the token correctly.

**Mistake 3: Treating SameSite as a complete solution**

SameSite can reduce risk, but the right protection depends on the workflow and browser behaviour.

**Mistake 4: Confusing CSRF with IDOR**

CSRF concerns unwanted requests through a user's browser. IDOR concerns insufficient authorization for a referenced object.

**Mistake 5: Assuming CSRF always exposes private data**

CSRF commonly concerns unauthorized actions; it does not inherently give the attacker access to the response body.

**Mistake 6: Testing against real users**

Use controlled test accounts and approved environments. Never make unauthorized changes to someone else's account.

**Mistake 7: Reporting without confirming impact**

A missing token may be a warning sign, but the tester should assess the actual operation and relevant protections before assigning severity.

## 17. How to Document a CSRF Finding

Use a structured report.

**Title:** Missing or Ineffective CSRF Protection on an Account Update Operation

**Affected component:** Record the actual endpoint and operation.

**Description:** Explain how the application handles state-changing requests and which required anti-CSRF control appears to be ineffective.

**Preconditions:** Specify the authentication state, user role, and other conditions necessary for the issue.

**Steps to reproduce:** Provide minimal, safe steps using an authorized test account.

**Expected behaviour:** The application should reject requests that fail the required anti-CSRF validation.

**Observed behaviour:** Record the actual response and whether the protected action occurred.

**Impact:** Describe the realistic unauthorized action that may be possible, supported by evidence.

**Recommendation:** Apply appropriate CSRF tokens, suitable SameSite cookie settings, robust origin validation where appropriate, and additional confirmation for sensitive operations.

**Evidence:** Include sanitized requests and responses. Remove session cookies, tokens, credentials, and personal data.

**Severity:** Consider the sensitivity of the action, required user interaction, authentication conditions, and actual impact.

This is a report template, not a claim that a real vulnerability has been found.

## 18. Assignment

Prepare a short report titled **CSRF Fundamentals and Request Protection**.

Include:

1. A definition of CSRF.
2. A diagram showing how a forged request may reach a trusted application.
3. An explanation of why browsers automatically send some cookies.
4. The purpose of CSRF tokens.
5. The role of the SameSite cookie attribute.
6. An explanation of Origin and Referer validation.
7. The difference between CSRF, XSS, and IDOR.
8. Observations from an authorized training lab.
9. Three possible prevention techniques.
10. A conclusion explaining why token presence alone does not prove that an application is protected.

Do not invent test results. If a vulnerability is not confirmed, report the observations and remaining uncertainty accurately.

## 19. Knowledge Check

**Q1. What does CSRF stand for?**

A. Cross-Site Request Forgery  
B. Cross-System Request Function  
C. Central Session Request Framework  
D. Cross-Server Response Format  

**Q2. What is the main issue in CSRF?**

A. An application accepts an unwanted request without adequate protections  
B. A database contains duplicate records  
C. A browser displays an image incorrectly  
D. A server uses a large amount of memory  

**Q3. What is the purpose of a CSRF token?**

A. To help validate that a state-changing request follows the expected application workflow  
B. To encrypt every database record  
C. To replace user authentication  
D. To guarantee that an application has no vulnerabilities  

**Q4. Does using POST automatically prevent CSRF?**

A. Yes  
B. No  

**Q5. What does SameSite control?**

A. When the browser sends a cookie in cross-site contexts  
B. How a database stores passwords  
C. How a server assigns IP addresses  
D. Whether a website uses JavaScript  

**Q6. What is the main difference between CSRF and IDOR?**

A. They are exactly the same vulnerability  
B. CSRF involves unwanted requests through a user's browser; IDOR involves insufficient authorization for a referenced object  
C. CSRF affects only databases  
D. IDOR affects only cookies  

**Q7. Does a visible CSRF token automatically prove that protection is effective?**

A. Yes  
B. No  

### Answers

1. A — Cross-Site Request Forgery.
2. A — An unwanted request is accepted without adequate protection.
3. A — It helps validate the request.
4. B — POST alone does not prevent CSRF.
5. A — It controls cookie transmission in cross-site contexts.
6. B — They involve different security weaknesses.
7. B — Server-side validation must be assessed.

## 20. Key Takeaways

After completing Day 19, you should be able to explain:

- CSRF can cause a user's authenticated browser to send an unwanted request.
- Session cookies help maintain authenticated state but do not prove user intent.
- State-changing operations need appropriate request protections.
- CSRF tokens must be validated by the server.
- SameSite cookies and origin validation can provide additional protection.
- POST requests are not automatically protected against CSRF.
- CSRF, XSS, and IDOR are different vulnerability classes.
- A missing token or unusual response is not sufficient by itself to prove a vulnerability.
- Security findings should be supported by controlled tests and evidence.

### Completion Checklist

- [ ] I can explain CSRF in simple terms.
- [ ] I understand why session cookies are relevant.
- [ ] I can identify state-changing operations.
- [ ] I understand the purpose of CSRF tokens.
- [ ] I understand the SameSite cookie attribute.
- [ ] I can distinguish CSRF from XSS and IDOR.
- [ ] I can inspect requests in browser Developer Tools.
- [ ] I can document observations without inventing findings.
- [ ] I understand why CSRF testing must be authorized.

**Learn responsibly. Test ethically.**
