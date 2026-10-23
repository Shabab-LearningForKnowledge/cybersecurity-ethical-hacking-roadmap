# Day 22 — Server-Side Request Forgery (SSRF) Fundamentals & Safe Testing

## 1. Introduction

Modern web applications often communicate with external services. For example, an application may fetch an image from a URL, retrieve information from an external API, or generate a preview of a webpage.

If the application does not properly validate the destination of these requests, an attacker may be able to manipulate the server into sending requests to unintended locations.

This vulnerability is called **Server-Side Request Forgery (SSRF)**.

In this lesson, you will learn how SSRF works, why it matters, how to recognize potentially vulnerable functionality, and how to test safely in an authorized lab.

**Learning objectives:**
- Understand SSRF and its common causes.
- Learn how server-side requests differ from browser requests.
- Recognize potential SSRF entry points.
- Understand basic, blind, and cloud-related SSRF risks.
- Learn safe validation and reporting techniques.

## 2. What Is SSRF?

SSRF occurs when an application can be influenced into making a server-side request to an unintended destination.

For example, imagine a website provides a feature that retrieves an image from a URL entered by the user.

The intended workflow is:

1. The user submits an image URL.
2. The application server retrieves the image.
3. The application displays the image to the user.

If destination validation is weak, a user might manipulate the URL to make the server contact a destination that was never intended by the application owner.

The impact depends on what the server can reach, what information is returned, and which security controls are in place.

**Key concept:** SSRF abuses the application's ability to make requests from its own network position.

## 3. Browser Requests vs Server-Side Requests

Consider two different situations.

**Browser-side request:**

```text
User's Browser
      |
      v
Web Application
```

The browser sends a request directly to the destination permitted by its network access and browser security rules.

**Server-side request:**

```text
User's Browser
      |
      v
Web Application Server
      |
      v
Another Destination
```

With SSRF, the application server makes the additional request on behalf of the user.

The server may have access to internal services that are not directly reachable from the user's browser. That difference is one reason SSRF can be serious.

## 4. Why SSRF Happens

Common causes include:

- Fetching a user-provided URL without adequate validation.
- Generating webpage previews from arbitrary URLs.
- Downloading files from user-supplied locations.
- Accepting unrestricted webhook destinations.
- Retrieving remote images or documents.
- Allowing users to specify API endpoints.
- Failing to validate redirects.
- Trusting DNS resolution or destination checks that can be bypassed.

A URL being syntactically valid does not mean the destination is safe.

Applications should validate both the intended destination and the network connections they establish.

## 5. Common SSRF Entry Points

During an authorized web application assessment, look for features that cause the server to retrieve remote resources.

Examples include:

| Feature | Potential concern |
|---|---|
| Image URL import | Server fetches a user-supplied image |
| Webpage preview | Server retrieves arbitrary web content |
| PDF generation | Server loads remote resources |
| Webhook configuration | Server sends requests to configured destinations |
| URL-based file import | Server downloads external files |
| URL inspection tools | Server contacts a supplied destination |
| Remote API integration | User-controlled parameters influence the destination |

These are **potential entry points**, not proof of vulnerabilities. Some features are designed securely and restrict destinations appropriately.

## 6. Basic SSRF Example

Imagine a simplified application that accepts a URL and fetches its content.

```python
import requests

def fetch_url(user_url):
    response = requests.get(user_url, timeout=5)
    return response.text
```

This illustrative example does not show destination validation or network restrictions.

If an application uses this pattern with untrusted input, the server may contact destinations the developer did not intend.

Additional issues can include redirects, unexpected protocols, large responses, and long-running requests.

**Security lesson:** Never treat a user-provided URL as trustworthy merely because it begins with `https://`.

## 7. What Is Basic SSRF?

In a basic SSRF scenario, the application makes a server-side request and some of the response is visible to the user.

For example, a URL-preview feature might display the fetched page title or content.

If the destination can be manipulated, the response may disclose information from an unintended service.

During testing, determine:

- Whether the server makes the request.
- Whether the destination is controlled by the input.
- Whether the response is returned to the user.
- Whether the destination is outside the application's intended allowlist.
- What information, if any, is exposed.

Do not access sensitive systems or data to prove a point. Use controlled destinations and harmless test content.

## 8. What Is Blind SSRF?

In blind SSRF, the application makes a server-side request, but the response content is not directly shown to the user.

For example, a webhook validation feature may contact a configured URL but return only a message such as:

```text
Webhook validation completed.
```

The absence of response content does not prove that no request occurred.

In an authorized lab, a controlled endpoint can help determine whether the application made a request and when it occurred.

Look for evidence such as:

- A request received by the controlled endpoint.
- A timestamp that matches the test.
- A unique test identifier in the request.
- Application logs available to the authorized tester.

A delayed callback or a single unexpected request should be investigated before drawing a conclusion.

## 9. SSRF and Internal Networks

Application servers sometimes communicate with internal services, such as:

- Internal APIs.
- Administrative services.
- Databases or service gateways.
- Monitoring systems.
- Cloud-hosted application components.

An SSRF vulnerability may allow a server to contact a destination outside its normal intended request flow.

However, **SSRF does not automatically mean an attacker can access every internal service**.

The result depends on network segmentation, firewall rules, application permissions, authentication, protocol restrictions, and the server's reachable destinations.

During a real assessment, use only the internal test resources explicitly approved in the scope.

## 10. SSRF and Cloud Metadata Services

Cloud environments may expose metadata services that provide information about the virtual machine or its environment.

If a vulnerable application can reach a metadata endpoint and the endpoint's controls are insufficient, SSRF may expose sensitive information.

Potential consequences vary by cloud provider and configuration. In some circumstances, metadata may contain information associated with workload identity or temporary credentials.

Modern cloud platforms offer controls that reduce these risks, but their effectiveness depends on correct configuration and application architecture.

**Testing rule:** Do not request real cloud metadata endpoints or attempt to retrieve credentials during a general SSRF exercise. Use a simulated metadata service in a lab.

## 11. SSRF vs CSRF vs XSS

These vulnerabilities have different underlying causes.

| Vulnerability | Main issue |
|---|---|
| SSRF | The server is manipulated into making an unintended request |
| CSRF | A user's browser is induced to perform an unwanted action using the user's existing authority |
| XSS | Untrusted content causes unintended script execution in a browser |
| Path Traversal | File access escapes an intended directory boundary |
| IDOR | A user accesses another object without proper authorization |

The distinction matters because each vulnerability requires different testing methods and remediation.

For example, CSRF generally concerns actions performed through a user's browser, whereas SSRF concerns requests made by the application server.

## 12. How to Identify SSRF Candidates

Start with the application's normal behavior and observe where URL input is accepted.

Examples include:

- An import form that accepts a remote URL.
- A webhook configuration page.
- An image-fetching endpoint.
- A URL-preview feature.
- An API parameter that appears to specify a remote destination.

For each candidate, ask:

1. Does the server actually fetch the supplied resource?
2. Can the input influence the destination?
3. Are destinations restricted to approved hosts?
4. Are redirects handled safely?
5. Can the request reach destinations outside the intended scope?
6. Is any sensitive response content exposed?

Do not assume every URL parameter creates an SSRF vulnerability. Some URLs are used only for display or browser navigation.

## 13. SSRF Prevention Techniques

Applications should use multiple layers of protection.

**Destination allowlisting:** Permit only the destinations required for the feature whenever practical.

**URL parsing:** Parse URLs with a trusted library rather than relying on simple string matching.

**IP address validation:** Validate resolved destination addresses against the application's intended network policy, including IPv4 and IPv6.

**Redirect validation:** Validate every redirect destination, not just the initial URL.

**Network egress controls:** Restrict outbound connections from application servers to only the destinations and ports they need.

**Protocol restrictions:** Permit only necessary protocols, such as HTTPS, where appropriate.

**Timeouts and response limits:** Prevent requests from consuming excessive resources.

**Least privilege:** Ensure the application and its cloud identity have only the permissions required.

**Safe error handling:** Avoid exposing internal response content or sensitive diagnostic details.

No single control is sufficient for every architecture. Effective protection combines application validation with network-level restrictions.

## 14. Why Simple URL Filtering Can Fail

Consider an application that blocks URLs containing a particular hostname as a string.

That approach may be insufficient because URL parsing, DNS resolution, redirects, alternative IP representations, and IPv6 can affect the final destination.

Other concerns include DNS rebinding, where DNS answers can change over time, and time-of-check/time-of-use problems, where the destination checked by the application differs from the destination ultimately contacted.

A robust implementation should enforce destination policy at the point of connection, account for redirects and address resolution, and prevent connections to prohibited network ranges.

The exact controls depend on the application's requirements and the HTTP client or networking library it uses.

## 15. Practical Lab: Safe SSRF Identification

**Objective:** Determine whether a test application makes requests to a controlled destination.

Use a deliberately vulnerable training application or a local application you own. Configure a harmless HTTP endpoint that you control to record incoming requests.

### Task A: Establish normal behavior

1. Open the lab's URL-fetching feature.
2. Submit a URL to your controlled endpoint.
3. Record the application's response.
4. Check whether your endpoint received a request.
5. Record the request time and relevant headers, excluding sensitive values.

### Task B: Review destination validation

Using only the lab's designated test cases, determine whether the application restricts destinations to the intended allowlist.

Record whether invalid or unapproved destinations are rejected.

Do not use public websites, internal networks, or cloud metadata endpoints as test targets unless the assessment explicitly authorizes them.

### Task C: Record the results

| Observation | Result |
|---|---|
| Does the application make a server-side request? | Record evidence |
| Is the destination controlled by user input? | Record evidence |
| Are unapproved destinations rejected? | Record evidence |
| Are redirects handled safely? | Record evidence |
| Is fetched response content exposed? | Record evidence |

A successful callback confirms that a request occurred under the tested conditions; it does not, by itself, prove access to internal services or sensitive data.

## 16. Reporting an SSRF Finding

Use a clear, evidence-based report structure.

**Title:** Insufficient Destination Validation in URL Fetching Feature

**Affected endpoint:** Identify the authorized application endpoint.

**Description:** Explain how user-controlled input influences a server-side request.

**Evidence:** Include the relevant request and response, the controlled endpoint's logs, and timestamps or test identifiers.

**Impact:** Describe the consequence demonstrated by the evidence. Do not claim internal network compromise or credential exposure unless it was explicitly and safely validated.

**Recommendation:** Restrict destinations, validate resolved addresses, revalidate redirects, apply outbound network controls, and limit the application's permissions.

**Severity:** Assess the actual reachability, information exposure, authentication requirements, and realistic impact according to the applicable severity methodology.

## 17. Common Mistakes to Avoid

- Assuming every URL parameter is an SSRF vulnerability.
- Confusing browser-side requests with server-side requests.
- Treating a single callback as proof of internal network access.
- Assuming all internal services are reachable from the application server.
- Relying on simple hostname string filtering.
- Forgetting to validate redirect destinations.
- Ignoring IPv6 and DNS resolution behavior.
- Attempting to obtain cloud credentials during a general lab exercise.
- Reporting severe impact without supporting evidence.
- Testing destinations that are outside the authorized scope.

## 18. Knowledge Check

**Q1. What does SSRF stand for?**

A. Secure Server Request Format  
B. Server-Side Request Forgery  
C. System Service Response Failure  
D. Server Session Recovery Function  

**Answer:** B

**Q2. Where does the manipulated request originate in SSRF?**

A. Only from the user's browser  
B. From the application server  
C. Only from a database client  
D. From a DNS record  

**Answer:** B

**Q3. Which feature could potentially introduce SSRF risk?**

A. A server-side URL preview tool  
B. A static heading  
C. A local CSS color  
D. A browser bookmark  

**Answer:** A

**Q4. Does a successful controlled callback automatically prove access to internal services?**

A. Yes  
B. No  
C. Only with HTTPS  
D. Only on Linux  

**Answer:** B

**Q5. Which is a useful SSRF mitigation?**

A. Allow every destination  
B. Validate destinations and restrict outbound network access  
C. Disable all application logging  
D. Trust every redirect  

**Answer:** B

## 19. Mini Assessment Challenge

Imagine an application provides a feature that retrieves an image from a user-supplied URL.

During an authorized lab test, you observe:

- The application contacts your controlled endpoint.
- The application displays a generic success message.
- You do not see the fetched response body.
- The application documentation says only approved image-hosting services should be accepted.

Answer the following:

1. What does the controlled callback demonstrate?
2. Does the generic success message prove that the response content was exposed?
3. What would you investigate about the approved destination policy?
4. Why should redirect destinations be validated?
5. What additional evidence would be required to claim access to an internal service?
6. Which application and network controls could reduce the risk?

**Suggested answers:**

1. It demonstrates that the application made a request to the controlled endpoint under the tested conditions.
2. No. It does not establish that the fetched response body was exposed.
3. Check whether the server enforces the documented allowlist and validates the final destination.
4. A permitted initial URL might redirect the server to an unintended destination.
5. Explicitly authorized, harmless evidence from a controlled internal test service—not merely an external callback.
6. Destination allowlisting, robust URL parsing, IP validation, redirect validation, outbound network restrictions, timeouts, and least privilege.

## 20. Key Takeaways

- SSRF occurs when user-controlled input causes an application server to make an unintended request.
- URL-fetching features, webhooks, and remote resource imports are common areas to assess.
- Basic SSRF may expose response content; blind SSRF may require controlled callback evidence.
- Internal network and cloud metadata risks depend on the server's reachability and surrounding controls.
- Secure implementations validate destinations, handle redirects safely, and restrict outbound connections.
- A confirmed server-side request is not automatically proof of sensitive data exposure.
- Evidence and impact must guide vulnerability reporting.

## 21. Further Learning Resources

- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP SSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
- [PortSwigger Web Security Academy — SSRF](https://portswigger.net/web-security/ssrf)

Use deliberately vulnerable training environments and controlled endpoints to practice safely.

## Completion Checklist

- [ ] I can explain SSRF in simple terms.
- [ ] I understand the difference between browser-side and server-side requests.
- [ ] I can identify common SSRF entry points.
- [ ] I understand basic and blind SSRF.
- [ ] I can explain why redirects and DNS resolution matter.
- [ ] I understand the importance of destination validation and outbound network restrictions.
- [ ] I can document a finding using controlled evidence.
- [ ] I completed all exercises within my own system or an authorized lab.

**Learn responsibly. Test ethically.**
