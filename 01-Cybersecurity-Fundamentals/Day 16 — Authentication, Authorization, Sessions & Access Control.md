# Day 16: Authentication, Authorization, Sessions & Access Control

## 1. Introduction

Most web applications require users to log in, access their accounts, and perform actions according to their permissions.

For example, in an online banking application:
- A customer can view their own account details.
- A bank employee may have access to specific customer services.
- An administrator may manage certain system settings.
- An unauthenticated visitor should not be able to access protected account information.

These restrictions are important because a web application must not simply trust a user because they are logged in.

In this lesson, we will understand authentication, authorization, sessions, cookies, access control, and the basic approach used to assess these controls during an authorized security test.

**Learning objectives:**
- Understand authentication and authorization.
- Learn how sessions and cookies work.
- Understand role-based and object-level access control.
- Recognize common access control weaknesses.
- Practise creating access control test cases in an authorized lab.

## 2. What Is Authentication?

Authentication is the process of verifying the identity of a user or system.

In simple terms, authentication answers:

**“Who are you?”**

Common authentication methods include:
- Username and password
- One-time passwords (OTP)
- Multifactor authentication (MFA)
- Passkeys
- Client certificates
- Federated login, such as signing in through an identity provider

**Example:**

A user enters their email address and password on a website. The application checks the credentials and, if they are valid, establishes an authenticated session or issues an authentication token.

A successful login confirms identity according to the application's authentication process. It does not automatically grant access to every resource.

## 3. What Is Authorization?

Authorization determines what an authenticated or unauthenticated user is allowed to do.

It answers:

**“What are you allowed to access or perform?”**

For example:

| User type | Permitted action |
|---|---|
| Guest | View public pages |
| Customer | View and update their own profile |
| Support agent | Access approved support functions |
| Administrator | Perform explicitly permitted administrative tasks |

Authorization should be enforced by the server for every protected operation.

**Important:** Hiding a button or menu in the frontend is not sufficient. A user may send requests directly to an endpoint, so the backend must independently verify permissions.

## 4. Authentication vs. Authorization

| Authentication | Authorization |
|---|---|
| Verifies identity | Checks permissions |
| Usually occurs during login or identity verification | Applies when a resource or action is requested |
| Example: validating a password | Example: deciding whether a user may delete a record |
| Failure may allow an unauthorized identity to be accepted | Failure may allow access to a forbidden resource or action |

**Simple example:**

A user successfully logs in to a shopping website. Authentication succeeds.

The user then attempts to view another customer's order. If the server permits this without authorization, authentication worked, but access control failed.

## 5. What Is a User Session?

A session allows an application to maintain state across multiple HTTP requests.

HTTP is fundamentally stateless: each request is processed as a separate HTTP message. Applications therefore use mechanisms such as sessions, cookies, and tokens to associate requests with an authenticated user.

A typical session flow looks like this:

1. The user submits valid login credentials.
2. The server verifies the credentials.
3. The server creates or establishes a session.
4. The application sends the browser a session identifier, commonly through a cookie.
5. The browser includes that cookie in later applicable requests.
6. The server uses the session identifier to retrieve session information and determine the user's identity.

A session identifier should be unpredictable and protected against unauthorized disclosure or reuse.

## 6. What Is a Cookie?

A cookie is a small piece of data that a website asks the browser to store. Depending on its attributes and scope, the browser may send it with subsequent requests.

Cookies can support:
- Session management
- User preferences
- Language settings
- Shopping cart state
- Certain tracking functions

Example of an HTTP response header:

```http
Set-Cookie: sessionid=example-value; Secure; HttpOnly; SameSite=Lax; Path=/
```

This is an illustrative example. The value shown is not a real session credential.

Important cookie attributes:

- **Secure:** The browser sends the cookie over HTTPS connections rather than ordinary HTTP.
- **HttpOnly:** JavaScript running in the browser cannot directly read the cookie through APIs such as `document.cookie`.
- **SameSite:** Controls when the browser sends the cookie in cross-site contexts and can help reduce certain CSRF risks.
- **Path:** Restricts the request paths for which the browser sends the cookie.
- **Domain:** Influences which hosts can receive the cookie.

Cookie attributes contribute to security, but none of them alone guarantees that a session is secure.

## 7. What Is a Session ID?

A session ID is an identifier associated with a session.

For example:

```http
Cookie: sessionid=example-value
```

The application may use this identifier to retrieve session information stored on the server.

A session ID is not necessarily a username, password, or user ID. It can nevertheless be highly sensitive because possession of a valid session identifier may allow someone to act as the associated user.

**Security considerations:**
- Session identifiers should be unpredictable.
- They should not be exposed unnecessarily in URLs or logs.
- Sessions should expire according to appropriate policies.
- Logout and other security-sensitive events should invalidate sessions when required.
- Session identifiers should be protected in transit using HTTPS.

Never share real session cookies or authentication tokens in screenshots, public reports, or repositories.

## 8. What Is Session Hijacking?

Session hijacking occurs when an attacker obtains or misuses another user's valid session to impersonate that user.

Possible contributing weaknesses include:
- Session identifiers exposed through insecure channels
- Cross-site scripting that enables session abuse
- Malware or compromised devices
- Inadequate session lifecycle controls
- Session identifiers leaked through application behaviour or logging

The exact risk depends on the application's session design and other security controls.

**Tester mindset:** Assess whether session identifiers are appropriately protected, whether sessions are invalidated when required, and whether the application prevents unauthorized session use.

Do not attempt to obtain or reuse another person's real session.

## 9. What Is Session Fixation?

Session fixation is a vulnerability in which an attacker causes a victim to use a session identifier known to the attacker, and the application continues to accept that identifier after the victim authenticates.

A common protective measure is to issue a new session identifier after successful authentication or another relevant privilege change.

During an authorized assessment, consider:
- Does the session identifier change after login?
- Are old session identifiers invalidated when appropriate?
- Is session state handled securely after authentication?
- Are session identifiers accepted from untrusted sources?

A session ID changing after login is a useful observation, but it does not by itself prove that every session fixation risk has been eliminated.

## 10. What Is Role-Based Access Control (RBAC)?

Role-Based Access Control assigns permissions based on a user's role.

Example:

```text
Customer
 ├── View own profile
 ├── Update permitted profile fields
 └── View own orders

Support Agent
 ├── Access approved support records
 └── Update permitted support information

Administrator
 ├── Manage approved user accounts
 └── Access permitted administrative functions
```

The actual permissions depend on the application's requirements.

A common weakness occurs when the application trusts a role value supplied by the browser instead of deriving and validating the user's permissions on the server.

**Security principle:** The server must enforce permissions. Client-side role checks are not a security boundary.

## 11. What Is Broken Access Control?

Broken access control occurs when an application fails to enforce the permissions required for a resource or action.

Examples include:
- A normal user can access an administrative function.
- A user can modify another user's profile.
- A restricted API endpoint accepts unauthorized requests.
- A user can perform an action outside their assigned permissions.

Broken access control is an important category in the OWASP Top 10:2025.

A tester should assess both access to information and the ability to perform actions. Read access and write access may have different permissions.

## 12. What Is IDOR?

IDOR stands for **Insecure Direct Object Reference**. It is a commonly used term for a type of access control weakness involving an object reference that an application does not authorize correctly.

Consider this illustrative endpoint:

```http
GET /api/orders/501
```

The number `501` identifies an order.

If the application allows a user to change the number and access an order belonging to someone else without proper authorization, the behaviour may indicate an IDOR vulnerability.

Object references may appear in:
- URL paths
- Query parameters
- Request bodies
- API identifiers
- File names or document references

**Important:** Sequential or predictable identifiers are not automatically vulnerabilities. The core issue is whether the application verifies that the requesting user is authorized to access the referenced object.

## 13. What Is Privilege Escalation?

Privilege escalation occurs when a user gains capabilities or permissions beyond those legitimately assigned to them.

Two common concepts are:

**Vertical privilege escalation:** A user obtains a higher level of privilege.

Example: A normal customer can perform an administrator-only action.

**Horizontal privilege escalation:** A user accesses another user's resources while operating at a similar privilege level.

Example: One customer can view another customer's private order.

Both may result from broken access control, but the impact depends on the affected resource, action, and business context.

## 14. What Is CSRF?

CSRF stands for **Cross-Site Request Forgery**.

It involves causing a user's browser to send an unwanted request to a website where the user is authenticated.

In some situations, the browser automatically attaches authentication cookies to the request. If the application does not have adequate protections, an unwanted state-changing action may succeed.

Possible protections include:
- Appropriate CSRF tokens
- Suitable SameSite cookie settings
- Origin or Referer validation where appropriate
- Requiring appropriate reauthentication for sensitive operations
- Designing state-changing endpoints safely

No single protection should be assumed effective in every context. The right controls depend on how authentication and requests are implemented.

CSRF is different from IDOR: CSRF concerns unwanted requests made through a user's browser, while IDOR concerns insufficient authorization for a referenced object.

## 15. Understanding Access Control Testing

Access control testing checks whether users can access only the resources and functions permitted to them.

A simple testing matrix might look like this:

| Test identity | Resource | Expected result |
|---|---|---|
| Guest | Public page | Allowed |
| Guest | Private account page | Denied or redirected to authentication |
| Customer A | Customer A's order | Allowed |
| Customer A | Customer B's order | Denied |
| Customer | Administrator-only function | Denied |
| Administrator | Permitted administrative function | Allowed |

The table is illustrative. Always follow the target application's documented permissions and authorized test scope.

A good tester evaluates:
- Different user roles
- Different resource owners
- Read and write operations
- Direct API requests
- Server-side permission checks
- Session behaviour after logout or account changes

## 16. Practical Exercise: Observe Authentication and Sessions

**Environment:** Use an application you own or an intentionally vulnerable training lab such as OWASP Juice Shop running in your own environment.

Do not test public websites without explicit authorization.

### Part A: Inspect a login request

1. Open the authorized application.
2. Open browser Developer Tools.
3. Select the **Network** tab.
4. Log in using a lab account.
5. Locate the login request.
6. Review the request method, status code, and relevant response headers.
7. Observe whether the application sets a cookie or returns a token.
8. Do not copy real credentials, cookies, or tokens into your notes.

### Part B: Observe logout

1. Record the general session behaviour after logging in.
2. Log out normally.
3. Observe what happens when you revisit a protected page.
4. Determine whether the application requires you to authenticate again.
5. Record the result without assuming that a browser redirect alone proves the server-side session was invalidated.

### Part C: Understand authorization

If your lab provides two test accounts with separate data:

1. Sign in as Test User A.
2. Identify a record belonging to User A.
3. Sign in as Test User B in a separate browser session.
4. Check whether User B can access only their permitted records.
5. Do not access real users' data or alter data outside the approved exercise.

Document expected behaviour, observed behaviour, and supporting evidence.

## 17. Common Mistakes Beginners Make

**Mistake 1: Assuming login means full authorization**

A logged-in user can still be restricted from specific resources or actions.

**Mistake 2: Treating every changing ID as IDOR**

An identifier change becomes a security concern when it results in unauthorized access or action.

**Mistake 3: Trusting frontend restrictions**

Hiding a button does not prevent a user from sending a direct request. The server must enforce access control.

**Mistake 4: Assuming HttpOnly prevents every XSS impact**

HttpOnly restricts direct JavaScript access to a cookie, but XSS can still enable other harmful actions in the user's browser.

**Mistake 5: Assuming logout always invalidates a session**

A visible logout redirect does not necessarily prove that the server invalidated the session identifier.

**Mistake 6: Reporting a vulnerability without evidence**

Record what you tested, the expected result, the actual result, and why the difference represents a security issue.

## 18. How to Document an Access Control Finding

A useful vulnerability report should explain the issue clearly enough for another person to reproduce it safely.

Example report structure:

**Title:** Unauthorized Access to Another User's Order

**Description:** The application may fail to verify that the authenticated user owns or is authorized to access the requested order.

**Affected component:** Order details API.

**Expected behaviour:** A user should access only orders permitted by their account and role.

**Observed behaviour:** Record the actual response from an authorized test using controlled test accounts. Do not invent results.

**Impact:** Depending on the data and operations exposed, the issue may disclose private information or allow unauthorized changes.

**Recommendation:** Enforce server-side object-level authorization on every relevant request. Derive permissions from trusted server-side identity and authorization data rather than trusting client-supplied identifiers.

**Evidence:** Include the minimum sanitized request and response necessary to demonstrate the finding. Remove credentials, cookies, tokens, and personal data.

**Note:** This is a report template, not a claim that a real vulnerability has been found.

## 19. Assignment

Create an **Authentication and Access Control Test Matrix** for an authorized lab application.

Include at least these scenarios:

1. Access a public page without logging in.
2. Attempt to access a protected page without logging in.
3. View a user's own record.
4. Assess whether one test account can access another test account's record.
5. Check whether a lower-privileged role can access a restricted function.
6. Observe session behaviour after logout.
7. Review relevant cookie attributes.
8. Record whether the observed behaviour matches the expected result.

For each scenario, document:
- Test case ID
- Feature or endpoint
- Test account or role
- Expected behaviour
- Actual observation
- Pass, fail, or inconclusive
- Evidence
- Recommended action, if needed

Use only authorized test accounts and data. A failed test case is not automatically a confirmed vulnerability; validate the cause and impact before reporting it.

## 20. Knowledge Check

**Q1. What does authentication verify?**

A. A user's identity  
B. A user's permissions  
C. A database schema  
D. A browser's settings  

**Q2. What does authorization determine?**

A. Whether a password is long  
B. What resources and actions a user is permitted to access  
C. Whether a website uses images  
D. Whether an application has a homepage  

**Q3. What is the main issue in an IDOR vulnerability?**

A. A page contains a broken image  
B. A user can access an object without proper authorization  
C. A server returns a 404 status code  
D. A website uses HTTPS  

**Q4. What does the HttpOnly cookie attribute do?**

A. Encrypts every HTTP request  
B. Prevents the browser from storing the cookie  
C. Restricts direct JavaScript access to the cookie  
D. Automatically invalidates the session after logout  

**Q5. What is vertical privilege escalation?**

A. Accessing a public page  
B. Obtaining permissions beyond one's assigned privilege level  
C. Changing a browser's window size  
D. Using a different network connection  

**Q6. Why should authorization be enforced on the server?**

A. The frontend is always unavailable  
B. Users can manipulate client-side behaviour or send requests directly  
C. The browser cannot send HTTP requests  
D. It makes authentication unnecessary  

**Q7. Does changing an object ID automatically prove an IDOR vulnerability?**

A. Yes  
B. No; unauthorized access or action must be demonstrated  

### Answers

1. A — A user's identity.
2. B — Permitted resources and actions.
3. B — An object is accessible without proper authorization.
4. C — It restricts direct JavaScript access to the cookie.
5. B — Obtaining higher privileges than assigned.
6. B — Client-side controls can be bypassed or manipulated.
7. B — The security impact must be validated.

## 21. Key Takeaways

After completing Day 16, you should be able to explain:

- Authentication verifies identity; authorization checks permissions.
- Sessions help applications maintain state across HTTP requests.
- Cookies may carry session identifiers and have important security attributes.
- Session IDs must be handled as sensitive credentials.
- Broken access control can expose resources or functions to unauthorized users.
- IDOR involves improper authorization for access to a referenced object.
- Vertical and horizontal privilege escalation describe different permission failures.
- CSRF and IDOR are different classes of security issues.
- Access control testing requires suitable test accounts, clear expected behaviour, and evidence.
- A finding should be validated and documented accurately before reporting.

### Completion Checklist

- [ ] I can explain authentication and authorization.
- [ ] I understand sessions, cookies, and session identifiers.
- [ ] I can explain Secure, HttpOnly, and SameSite cookie attributes.
- [ ] I understand broken access control and IDOR.
- [ ] I can distinguish horizontal and vertical privilege escalation.
- [ ] I understand the basic concept of CSRF.
- [ ] I can prepare a basic access control test matrix.
- [ ] I can document observations without inventing results.
- [ ] I understand the importance of authorized testing.

**Learn responsibly. Test ethically.**
