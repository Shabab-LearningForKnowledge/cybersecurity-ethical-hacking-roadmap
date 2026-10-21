# Day 20 — Web Security Misconfiguration & HTTP Security Headers

## 1. Introduction

A web application can have secure code but still be vulnerable because of incorrect server settings, unnecessary services, unsafe default configurations, or missing security controls.

This is known as **security misconfiguration**.

In this lesson, you will learn how to identify common web security misconfigurations, understand important HTTP security headers, and inspect application responses during an authorized security assessment.

**Learning objectives:**
- Understand web security misconfiguration.
- Learn the purpose of important HTTP security headers.
- Inspect response headers using browser Developer Tools and command-line tools.
- Recognize potential weaknesses without reporting false positives.
- Document findings clearly.

## 2. What Is Security Misconfiguration?

Security misconfiguration occurs when an application, server, framework, cloud service, or other component is configured insecurely.

Common examples include:

- Exposing detailed error messages to users.
- Leaving default credentials unchanged.
- Enabling unnecessary services or features.
- Exposing administrative interfaces publicly.
- Using overly permissive access settings.
- Disclosing sensitive information through response headers.
- Missing or incorrectly configured security controls.

**Example:**

Imagine a web application displays this error to a user:

```text
Database connection failed.
Database host: db-prod.internal
Username: app_service
File: /var/www/application/config/database.php
```

This response exposes internal implementation details that an attacker could use during reconnaissance.

The exposure does not automatically mean the database is accessible, but it provides unnecessary information.

## 3. Understanding HTTP Security Headers

HTTP security headers are instructions or information sent by a web server to a browser as part of an HTTP response.

They can help control browser behavior and reduce certain attack risks.

A typical HTTP response might look like this:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Strict-Transport-Security: max-age=31536000
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
```

The browser receives these headers and processes them according to the relevant web standards.

Remember: **security headers are additional security controls, not a replacement for secure application code.**

## 4. Content-Security-Policy (CSP)

Content-Security-Policy, commonly called CSP, allows a website to define which sources of content the browser may load or execute.

It can help reduce the impact of certain Cross-Site Scripting (XSS) attacks.

Example:

```http
Content-Security-Policy: default-src 'self'; object-src 'none'; base-uri 'self'
```

What this policy means:

- `default-src 'self'`: Uses the site's own origin as the default permitted source for many resource types.
- `object-src 'none'`: Blocks embedded object content.
- `base-uri 'self'`: Restricts the URLs that can be used in the HTML base element.

CSP can also use directives such as `script-src`, `style-src`, `img-src`, and `frame-ancestors`.

**Security testing perspective:** Check whether the policy exists, whether it matches the application's needs, and whether it contains overly broad source rules. A CSP header alone does not prove that an application is protected against XSS.

## 5. HTTP Strict Transport Security (HSTS)

HSTS tells a browser to use HTTPS when connecting to a website after receiving the policy over a secure HTTPS connection.

Example:

```http
Strict-Transport-Security: max-age=31536000
```

Here, `max-age=31536000` tells the browser to remember the HTTPS-only policy for approximately one year.

A policy may also include directives such as:

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

Important points:

- HSTS should be delivered over HTTPS.
- `includeSubDomains` applies the policy to subdomains and should be used only when those subdomains are ready for HTTPS.
- `preload` has additional requirements and should not be enabled casually.
- HSTS does not repair an invalid TLS configuration or secure an application by itself.

## 6. X-Content-Type-Options

The `X-Content-Type-Options` header can help prevent browsers from interpreting content as a different MIME type than the server declares.

Example:

```http
X-Content-Type-Options: nosniff
```

The `nosniff` value helps prevent MIME-type sniffing in relevant browser contexts.

Why it matters:

If a server incorrectly serves uploaded or generated content with an unsuitable content type, browser interpretation may create additional risk.

However, the actual impact depends on how the application serves the content, the response headers, and the browser context.

## 7. Referrer-Policy

The `Referrer-Policy` header controls how much referrer information a browser sends when navigating to another resource.

Example:

```http
Referrer-Policy: strict-origin-when-cross-origin
```

This policy generally sends the full referrer URL for same-origin requests, but only the origin for cross-origin requests when the security conditions permit it.

Why it matters:

URLs may contain information such as search terms, record identifiers, or other sensitive parameters. Referrer policies can help limit unintended disclosure to other sites.

A policy must be assessed in context; different applications may have different requirements.

## 8. Clickjacking Protection

Clickjacking occurs when a user is tricked into interacting with a page or interface they did not intend to use, often through deceptive framing.

Two relevant controls are:

**Option 1: Content-Security-Policy**

```http
Content-Security-Policy: frame-ancestors 'none'
```

This prevents the page from being embedded in frames by any origin, subject to browser enforcement.

**Option 2: X-Frame-Options**

```http
X-Frame-Options: DENY
```

This is a legacy framing-control header supported by many browsers.

Key points:

- `frame-ancestors` is the more flexible modern control.
- `DENY` blocks framing; `SAMEORIGIN` permits framing by the same origin.
- Do not automatically report the absence of `X-Frame-Options` if an appropriate CSP `frame-ancestors` policy already provides protection.
- Framing restrictions should reflect the application's legitimate embedding requirements.

## 9. Permissions-Policy

The `Permissions-Policy` header lets a website control access to certain browser features.

Example:

```http
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

This example disables the listed features for the page, subject to browser support and policy behavior.

Why it matters:

If an application does not need camera, microphone, or geolocation functionality, restricting those capabilities may reduce unnecessary browser access.

Do not copy this example blindly. A video-conferencing or location-based application may legitimately require some of these features.

## 10. Secure Cookie Attributes

Cookies often store session identifiers or other application state. Their configuration can influence session security.

Example:

```http
Set-Cookie: session_id=example; Secure; HttpOnly; SameSite=Lax; Path=/
```

Understand the attributes:

- `Secure`: The browser sends the cookie over secure HTTPS connections.
- `HttpOnly`: JavaScript cannot directly read the cookie through APIs such as `document.cookie`.
- `SameSite=Lax`: Restricts some cross-site cookie sending and can help mitigate certain CSRF scenarios.
- `Path=/`: Makes the cookie available to matching paths across the site; it is not a security boundary.

Additional notes:

- The cookie name and value above are illustrative only.
- The `Domain` attribute, if present, affects which hosts receive the cookie.
- Cookie configuration must be assessed against the application's authentication and cross-site request requirements.
- `HttpOnly` does not prevent every consequence of XSS, and `SameSite` is not a universal CSRF defense.

## 11. Other Common Misconfigurations

Security misconfiguration extends beyond HTTP headers.

| Misconfiguration | Potential concern |
|---|---|
| Verbose error messages | Reveals internal implementation details |
| Directory listing enabled | Exposes files and directory structure |
| Default credentials | May permit unauthorized access |
| Unnecessary services | Expands the attack surface |
| Public administrative interface | May increase exposure to attacks |
| Overly permissive CORS | May allow unintended cross-origin access |
| Exposed configuration or backup files | May disclose sensitive information |
| Debug mode enabled in production | May reveal sensitive diagnostic data |

A visible configuration weakness is not automatically a confirmed exploitable vulnerability. Validate its context and impact.

## 12. Understanding CORS Misconfiguration

Cross-Origin Resource Sharing (CORS) is a browser mechanism that controls whether web pages from one origin can access responses from another origin.

Example of a potentially risky response:

```http
Access-Control-Allow-Origin: *
```

This permits browser-based access to certain responses from any origin, subject to the CORS rules and request details.

However, `*` is not automatically a vulnerability. Its risk depends on the sensitivity of the response and whether credentials or other protected resources are involved.

Be cautious when reviewing configurations such as:

```http
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Credentials: true
```

The key question is whether the application trusts only appropriate origins and exposes only the intended data.

**Testing principle:** Never label a CORS configuration vulnerable based only on the presence of a particular header. Verify the browser behavior and potential impact using an authorized test environment.

## 13. How to Inspect Security Headers in a Browser

You can inspect HTTP response headers using browser Developer Tools.

Steps:

1. Open your own test application or an authorized training lab.
2. Open Developer Tools using `F12` or the browser menu.
3. Select the **Network** tab.
4. Reload the page.
5. Select the main document request.
6. Open **Headers**.
7. Review the response headers.
8. Record relevant observations.

Look for headers such as:

- `Content-Security-Policy`
- `Strict-Transport-Security`
- `X-Content-Type-Options`
- `Referrer-Policy`
- `Permissions-Policy`
- `X-Frame-Options`
- `Set-Cookie`

Check the main document response as well as relevant API or resource responses. Different endpoints may return different headers.

## 14. Inspect Headers Using cURL

`curl` is a command-line utility for making HTTP requests.

To inspect the response headers of a local test application:

```bash
curl -I http://127.0.0.1:3000/
```

To inspect a local HTTPS application:

```bash
curl -I https://localhost:8443/
```

Replace the example addresses and ports with those of your own lab.

Useful command:

```bash
curl -sS -D - -o /dev/null http://127.0.0.1:3000/
```

This displays the response headers while discarding the response body.

Important details:

- `-I` requests headers using the HTTP HEAD method. Some applications handle HEAD differently from GET.
- The second command sends a GET request and displays its response headers.
- If the application uses a self-signed or untrusted certificate, investigate the certificate issue rather than immediately bypassing verification.
- Redirects can result in different headers at different stages of the request.

## 15. Analyze Findings Correctly

Suppose your test application returns:

```http
HTTP/1.1 200 OK
Content-Type: text/html
```

You do not see a CSP or `X-Content-Type-Options` header.

What should you conclude?

Not necessarily that the application is exploitable.

Instead:

1. Confirm that you inspected the correct response.
2. Check whether the application needs the control.
3. Look for alternative controls, such as a CSP policy that already restricts framing.
4. Review the application's behavior and deployment requirements.
5. Determine whether the omission creates a meaningful security risk.
6. Record the evidence and explain the potential impact.

A missing header may be a hardening recommendation rather than a high-severity vulnerability.

## 16. Security Testing Workflow

Follow this workflow during an authorized assessment:

**Step 1 — Confirm scope:** Identify the approved application, hostnames, and test environment.

**Step 2 — Collect responses:** Use browser Developer Tools or cURL.

**Step 3 — Review configuration:** Check relevant headers, cookies, errors, and exposed resources.

**Step 4 — Validate behavior:** Determine whether the configuration creates a realistic security weakness.

**Step 5 — Assess impact:** Consider affected users, sensitive data, and realistic attack scenarios.

**Step 6 — Document evidence:** Save the relevant response and explain the finding.

**Step 7 — Recommend remediation:** Provide a solution appropriate to the application's requirements.

Do not perform intrusive tests against production systems without explicit authorization.

## 17. Practical Lab Exercise

**Objective:** Inspect the HTTP security configuration of an application you own or a deliberately vulnerable training lab.

### Task A: Inspect response headers

Use the browser Network tab to record the response headers of the main document.

### Task B: Collect headers using cURL

```bash
curl -sS -D - -o /dev/null http://127.0.0.1:3000/
```

If your lab uses another port or HTTPS, update the URL accordingly.

### Task C: Create an observation table

| Header or control | Observed? | Notes |
|---|---|---|
| Content-Security-Policy | Yes/No | Review directives and purpose |
| Strict-Transport-Security | Yes/No/Not applicable | Check HTTPS deployment |
| X-Content-Type-Options | Yes/No | Check for `nosniff` |
| Referrer-Policy | Yes/No | Review information disclosure implications |
| Framing protection | Yes/No | Check CSP `frame-ancestors` or X-Frame-Options |
| Cookie attributes | Yes/No/Not applicable | Review Secure, HttpOnly, SameSite |

Use `Not applicable` where a control does not fit the response or deployment. Do not mark a finding as confirmed solely because a header is absent.

### Task D: Write a short assessment

For each observation, document:

- What you observed.
- Why it might matter.
- Whether you validated an actual risk.
- What remediation may be appropriate.

## 18. Writing a Security Finding

A good security finding is evidence-based and clearly distinguishes confirmed risk from recommendations.

Example template:

**Title:** Recommended HTTP Security Header Configuration

**Description:** The assessed response did not include a particular security header. The relevance of this omission depends on the application's behavior and existing security controls.

**Evidence:** Include the affected endpoint and the relevant HTTP response headers, with secrets and session values removed.

**Impact:** Describe the realistic risk only if it is supported by testing. Otherwise, identify the observation as a hardening recommendation.

**Recommendation:** Configure the appropriate header and directives based on the application's architecture and functional requirements, then retest.

**Severity:** Assign severity according to validated impact, exploitability, and the applicable assessment methodology—not simply because a header is missing.

## 19. Common Mistakes to Avoid

- Treating every missing security header as a critical vulnerability.
- Assuming that HTTPS means the application is fully secure.
- Copying CSP directives without understanding their effect.
- Enabling HSTS for subdomains that are not ready for HTTPS.
- Reporting missing `X-Frame-Options` without checking `frame-ancestors`.
- Treating `Access-Control-Allow-Origin: *` as automatically exploitable.
- Ignoring application functionality when recommending restrictive policies.
- Publishing session cookies, tokens, or sensitive response data in reports.
- Testing systems outside the approved scope.

A professional tester validates the security impact instead of relying only on a checklist.

## 20. Knowledge Check

**Q1. What is security misconfiguration?**

A. A programming language  
B. An insecure setting or configuration  
C. A network cable  
D. A database query  

**Answer:** B

**Q2. Which header can help mitigate certain XSS risks by restricting resource sources?**

A. Content-Security-Policy  
B. User-Agent  
C. Accept-Language  
D. Host  

**Answer:** A

**Q3. What does the `Secure` cookie attribute do?**

A. Encrypts the database  
B. Restricts the cookie to secure HTTPS connections  
C. Prevents all XSS attacks  
D. Blocks every cross-site request  

**Answer:** B

**Q4. Does a missing security header automatically prove an exploitable vulnerability?**

A. Yes, always  
B. No, context and impact must be evaluated  
C. Only on Linux  
D. Only when the server uses HTTPS  

**Answer:** B

**Q5. Which header is specifically designed to control whether a page can be embedded in frames?**

A. `frame-ancestors` in CSP  
B. `Content-Length`  
C. `Accept-Encoding`  
D. `Cache-Control`  

**Answer:** A

## 21. Key Takeaways

- Security misconfiguration can expose an application even when its code appears secure.
- HTTP security headers influence browser behavior and provide additional protection.
- CSP, HSTS, `X-Content-Type-Options`, `Referrer-Policy`, and framing controls have different purposes.
- Cookie attributes help protect session cookies but do not eliminate every web attack.
- CORS settings must be evaluated according to actual browser behavior and data sensitivity.
- Missing headers require context-based analysis.
- Strong security assessment combines evidence, validation, impact analysis, and practical remediation.

## Completion Checklist

- [ ] I understand web security misconfiguration.
- [ ] I can explain the purpose of common HTTP security headers.
- [ ] I can inspect response headers using browser Developer Tools.
- [ ] I can collect response headers using cURL.
- [ ] I understand why a missing header is not automatically a confirmed vulnerability.
- [ ] I can document a finding with evidence and an appropriate recommendation.
- [ ] I performed the exercise only on my own system or an authorized lab.

**Learn responsibly. Test ethically.**
