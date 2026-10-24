# Day 23: API Security Fundamentals & Common API Vulnerabilities

## 1. Introduction

Modern applications use APIs to exchange data between web applications, mobile applications, backend services, and third-party systems.

For example, when you open a banking application and check your account balance, the mobile app may communicate with a backend API to retrieve the information.

If an API has weak authentication, improper authorization, excessive data exposure, or insufficient input validation, an attacker may be able to access sensitive information or perform unauthorized actions.

In this lesson, you will learn how APIs work, how to identify common API security weaknesses, and how to perform basic API security testing in an authorized lab.

**Learning objectives:**
- Understand REST APIs and JSON.
- Learn common HTTP methods and status codes used by APIs.
- Understand authentication and authorization in APIs.
- Recognize common API security vulnerabilities.
- Inspect API requests and responses using Burp Suite or Postman.
- Document findings with clear evidence and appropriate impact.

## 2. What Is an API?

API stands for **Application Programming Interface**.

An API defines how software components communicate with each other through agreed-upon requests, responses, and data formats.

For example, a mobile application may request customer information from a backend API.

```text
Mobile Application
        |
        | API Request
        v
Backend API
        |
        v
Database
        |
        v
API Response
        |
        v
Mobile Application
```

The API acts as an interface between the client and the backend service.

An API does not necessarily have its own user interface. It may exchange data with a web browser, mobile app, another server, or an automated service.

## 3. What Is a REST API?

REST stands for **Representational State Transfer**. It is an architectural style commonly used to design web APIs.

REST APIs commonly use HTTP methods to perform operations on resources.

For example:

| Method | Typical purpose |
|---|---|
| GET | Retrieve a resource |
| POST | Create a resource or trigger an operation |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Delete a resource |

Example API endpoints:

```text
GET    /api/products
GET    /api/products/101
POST   /api/products
PATCH  /api/products/101
DELETE /api/products/101
```

These are illustrative routes. The actual behavior depends on the API implementation.

**Security testing perspective:** Never assume that a particular HTTP method is automatically safe or dangerous. Verify what the endpoint actually does and which permissions it requires. As per the security standard only GET and POST methods are safe. 

## 4. Understanding JSON

JSON stands for **JavaScript Object Notation**. It is a common format for exchanging structured data between clients and APIs.

Example request body:

```json
{
  "product_id": 101,
  "quantity": 2
}
```

Example response:

```json
{
  "product_id": 101,
  "product_name": "Keyboard",
  "quantity": 2,
  "status": "confirmed"
}
```

JSON can contain strings, numbers, booleans, arrays, objects, and null values.

During API testing, inspect the request and response bodies to understand which fields the client supplies and which information the server returns.

A field appearing in a request does not mean the client is authorized to control it.

## 5. Understanding an API Request

Consider this example:

```http
GET /api/products/101 HTTP/1.1
Host: api.lab.example
Accept: application/json
Authorization: Bearer <ACCESS_TOKEN>
```

Important components:

- **Method:** `GET`
- **Endpoint:** `/api/products/101`
- **Host:** The API server
- **Accept:** Indicates the response format the client prefers
- **Authorization:** May carry a bearer token used for authentication

The server processes the request and returns a response based on its implementation and security controls.

During testing, identify which parts of the request are controlled by the client and whether changing them affects the resource or operation being accessed.

Never publish real access tokens or session credentials in course materials or reports.

## 6. Understanding API Responses

An API response typically includes a status code, headers, and sometimes a response body.

Example:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
{
  "message": "Request completed"
}
```

Common status codes include:

| Status code | Meaning |
|---|---|
| 200 OK | Request succeeded |
| 201 Created | Resource created |
| 204 No Content | Request succeeded without a response body |
| 400 Bad Request | Request was invalid |
| 401 Unauthorized | Authentication is missing or invalid |
| 403 Forbidden | Request was refused due to authorization or policy |
| 404 Not Found | Resource or endpoint was not found, or its existence is concealed |
| 405 Method Not Allowed | HTTP method is not supported for that resource |
| 429 Too Many Requests | Request rate limit was exceeded |
| 500 Internal Server Error | Server encountered an unexpected error |

A status code alone does not prove whether an API is secure. For example, a `200 OK` response could contain data the user should not have received.

## 7. API Authentication

Authentication determines whether a caller has established an identity.

Common API authentication approaches include:

- Session cookies.
- Bearer access tokens.
- API keys.
- OAuth 2.0-based authorization flows.
- Mutual TLS in selected service-to-service environments.

Example bearer token header:

```http
Authorization: Bearer <ACCESS_TOKEN>
```

The placeholder represents a token, not a real credential.

During an assessment, check whether protected endpoints enforce the expected authentication requirements.

Questions to consider:

- Does the endpoint work without authentication?
- Does it reject an invalid or expired token?
- Does it enforce the correct identity?
- Can one user's credentials be used to access another user's information?

The correct behavior depends on the endpoint. Some APIs intentionally expose public resources.

## 8. API Authorization

Authorization determines what an authenticated user or service is allowed to do.

An API can authenticate a user correctly and still have an authorization vulnerability.

Example:

```text
User A → GET /api/orders/1001
User B → GET /api/orders/1002
```

If User A changes the order identifier and can retrieve User B's private order without permission, the API may have a broken object-level authorization vulnerability.

Changing an identifier is not itself proof of a vulnerability. The key question is whether the server improperly grants access to the referenced object.

Test authorization using accounts and resources specifically created for the authorized assessment.

## 9. BOLA and IDOR

**BOLA** stands for Broken Object Level Authorization. It occurs when an API fails to enforce authorization for a particular object.

**IDOR** stands for Insecure Direct Object Reference. It describes a common pattern in which a user manipulates an object reference to access something they are not authorized to access.

Example request:

```http
GET /api/orders/1001 HTTP/1.1
Host: api.lab.example
Authorization: Bearer <USER_A_TOKEN>
```

In a controlled lab, compare access to an order owned by User A with access to a test order owned by User B.

Secure behavior requires the server to check whether the authenticated user is permitted to access the requested order.

**Important:** Unpredictable identifiers can make guessing harder, but they do not replace authorization checks.

## 10. Broken Object Property Level Authorization

An API may correctly restrict access to a user's account but still expose or allow changes to properties the user should not control.

This category includes two common patterns:

- **Excessive data exposure:** The API returns sensitive fields that the client does not need or should not see.
- **Mass assignment:** The API accepts fields that users should not be able to modify.

Example request body:

```json
{
  "display_name": "Alex",
  "account_role": "administrator"
}
```

If an ordinary user can change their own role by submitting a field the application should control, that may indicate a serious authorization weakness.

The server should explicitly allow permitted properties and independently enforce permissions for sensitive changes.

Do not assume that a field is vulnerable merely because it appears in a request. Validate the server's behavior using test accounts and harmless data.

## 11. API Input Validation and Injection

APIs receive user-controlled input through:

- URL paths.
- Query parameters.
- JSON bodies.
- Form fields.
- HTTP headers.
- File uploads.

The application must validate and safely process that input.

Potential issues include:

- SQL injection.
- Cross-Site Scripting in data later displayed in a browser.
- Command injection.
- Path traversal.
- Server-Side Request Forgery.
- Invalid data types or unexpected values.

Example:

```json
{
  "quantity": -500
}
```

If an order API accepts a negative quantity and processes it incorrectly, the problem may be a business logic or input validation vulnerability.

The impact depends on how the application handles the value.

Validation should enforce the business rules, while secure database queries and other context-appropriate controls prevent injection.

## 12. Excessive Data Exposure

An API may return more information than the client needs.

For example:

```json
{
  "customer_id": 501,
  "name": "Example User",
  "email": "user@example.test",
  "internal_notes": "Restricted support information",
  "password_reset_token": "REDACTED"
}
```

This is an illustrative example. Sensitive tokens should not be exposed in API responses.

Even if the frontend displays only the customer's name and email, other fields returned by the backend may still be accessible to anyone authorized to inspect the response.

During testing:

1. Inspect the complete response body.
2. Identify fields that appear unnecessary or sensitive.
3. Confirm the sensitivity and intended access rules.
4. Check whether the information is available to an unauthorized user.
5. Recommend returning only the properties needed for the use case.

Do not copy real personal information, secrets, or tokens into reports unnecessarily.

## 13. API Rate Limiting and Resource Consumption

An API may perform expensive operations, process large requests, or send messages and verification codes.

Without suitable restrictions, repeated requests may consume excessive resources or enable abuse.

Examples include:

- Login attempts.
- Password reset requests.
- OTP generation.
- Large search queries.
- File processing.
- Expensive report generation.

Useful controls can include:

- Rate limiting.
- Request and file size limits.
- Execution timeouts.
- Pagination and result limits.
- Resource quotas.
- Abuse detection and monitoring.

A missing `429 Too Many Requests` response does not automatically prove a vulnerability. Rate-limiting behavior should be evaluated against the endpoint's requirements and observed controls.

Only conduct rate-limit tests within an explicitly approved testing plan. Do not generate excessive traffic against production systems.

## 14. API Security Misconfiguration

API security problems can also arise from incorrect deployment or configuration.

Examples include:

- Debug endpoints exposed in production.
- Verbose error responses.
- Unnecessary HTTP methods enabled.
- Missing or inappropriate access controls.
- Incorrect CORS policies.
- Outdated API versions left accessible.
- Insecure transport configurations.
- Sensitive data included in logs or error messages.

A secure assessment should distinguish an actual weakness from an informational observation.

For example, an older API version is not automatically vulnerable simply because it exists. Determine whether it is still supported, whether it is accessible, and whether it has known or demonstrated security weaknesses.

## 15. API Testing with Burp Suite and Postman

Two useful tools for learning API security are:

**Burp Suite:** Allows an authorized tester to intercept, inspect, and modify HTTP requests and responses.

**Postman:** Allows users to construct API requests, supply headers and request bodies, and examine responses.

A basic workflow:

1. Identify an API endpoint in your authorized lab.
2. Send a normal request.
3. Record the expected response.
4. Review authentication requirements.
5. Inspect request and response properties.
6. Change one test variable at a time.
7. Compare the result with the expected security behavior.
8. Record evidence and restore the normal test state.

Do not replay captured requests against systems outside your authorization. Redact credentials before saving or sharing requests.

## 16. Practical Lab: API Security Assessment

**Objective:** Assess an API in a local application you own or an authorized training environment.

### Task A: Understand normal behavior

Identify a documented endpoint and record:

- HTTP method.
- URL path.
- Authentication requirement.
- Request parameters or JSON body.
- Expected response.
- User permissions required.

### Task B: Test authentication

Using a test account, verify the expected behavior when:

- The request includes valid credentials.
- The request has no credentials.
- The request has an invalid or expired credential, if the lab supports this test.

Record the responses and explain whether they match the endpoint's documented access requirements.

### Task C: Test authorization

If the lab provides two test users with separate resources:

1. Access User A's own test resource.
2. Use User A's credentials to request User B's test resource.
3. Record whether access is denied or whether data is improperly disclosed.
4. Confirm the ownership of both test resources.

Do not use another real person's account or data.

### Task D: Inspect response properties

Review whether the API returns unnecessary sensitive fields or accepts properties that the current user should not control.

Use harmless test data only.

## 17. API Assessment Observation Sheet

Use this table to organize the results.

| Test area | What to verify | Result |
|---|---|---|
| Authentication | Protected endpoint rejects unauthenticated requests | Record evidence |
| Authorization | User cannot access another user's restricted object | Record evidence |
| Property authorization | Sensitive fields cannot be improperly read or modified | Record evidence |
| Input validation | Invalid values are handled safely | Record evidence |
| Data exposure | Response contains only appropriate information | Record evidence |
| Rate limiting | Relevant limits are enforced according to requirements | Record evidence |
| Error handling | Errors do not unnecessarily reveal sensitive details | Record evidence |
| Configuration | API settings align with security requirements | Record evidence |

Use **Pass**, **Fail**, **Needs Investigation**, or **Not Applicable** based on the evidence. Do not mark a test as failed just because the behavior is unfamiliar.

## 18. Writing an API Security Finding

A good report explains the weakness, the evidence, the impact, and the recommended fix.

Example template:

**Title:** Broken Object Level Authorization in Order API

**Affected endpoint:** Record the authorized test endpoint.

**Description:** Explain how the API handles requests for objects belonging to different users.

**Preconditions:** Identify the account roles, authentication state, and permissions needed to reproduce the issue.

**Evidence:** Include sanitized requests and responses from the test accounts.

**Impact:** Describe the unauthorized action or data access actually demonstrated.

**Recommendation:** Enforce object-level authorization on the server for every relevant request. Do not rely on frontend restrictions or unpredictable identifiers alone.

**Severity:** Determine severity from the actual impact, exploitability, required privileges, and applicable assessment methodology.

For other findings, adapt the same structure to the specific issue rather than using a generic API vulnerability label.

## 19. Common Mistakes to Avoid

- Assuming that a successful `200 OK` response is secure.
- Treating authentication and authorization as the same concept.
- Assuming an API is safe because its documentation is private.
- Trusting fields supplied by the client without server-side checks.
- Assuming unpredictable object identifiers prevent unauthorized access.
- Reporting every exposed field as sensitive without checking its context.
- Assuming every API needs identical rate limits.
- Treating CORS as a replacement for authentication or authorization.
- Using real credentials or other users' data during testing.
- Reporting vulnerabilities without reproducible evidence.

Professional API testing requires understanding the endpoint's intended behavior before deciding whether a response is a security issue.

## 20. Knowledge Check

**Q1. What is the main purpose of an API?**

A. To define how software components communicate  
B. To replace all databases  
C. To prevent every cyberattack  
D. To encrypt every file automatically  

**Answer:** A

**Q2. What does BOLA mean?**

A. Browser Object Link Analysis  
B. Broken Object Level Authorization  
C. Basic Online Login Authentication  
D. Backend Output Logging Assessment  

**Answer:** B

**Q3. An authenticated user can access another user's private order without permission. What should you investigate?**

A. Broken object-level authorization  
B. Image compression  
C. DNS caching only  
D. Browser theme settings  

**Answer:** A

**Q4. Why should APIs validate sensitive fields on the server?**

A. Clients can modify submitted requests  
B. JSON cannot contain numbers  
C. HTTP does not support headers  
D. APIs never use authentication  

**Answer:** A

**Q5. Does a `200 OK` response prove that an API is secure?**

A. Yes  
B. No  
C. Only for GET requests  
D. Only when the response is JSON  

**Answer:** B

## 21. Key Takeaways

- APIs allow software components to exchange data and perform operations.
- API security depends on authentication, authorization, input validation, safe data handling, and appropriate configuration.
- BOLA and IDOR involve unauthorized access to specific objects.
- Excessive data exposure and mass assignment are important API security concerns.
- Rate limits and resource controls help protect expensive or abuse-prone operations.
- Burp Suite and Postman help inspect and test API behavior in authorized environments.
- Status codes and unusual responses are clues, not proof of a vulnerability.
- A professional report explains the demonstrated impact and provides a practical remediation.

## Further Learning Resources

- [OWASP API Security Project](https://owasp.org/www-project-api-security/)
- [OWASP API Security Top 10 — 2023](https://owasp.org/API-Security/editions/2023/en/0x00-header/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [Postman Learning Center](https://learning.postman.com/)

## Completion Checklist

- [ ] I understand what an API is and how REST APIs commonly work.
- [ ] I can inspect API requests and responses.
- [ ] I understand authentication and authorization differences.
- [ ] I can explain BOLA, excessive data exposure, and mass assignment.
- [ ] I understand API input validation and resource consumption risks.
- [ ] I can use Burp Suite or Postman for basic authorized testing.
- [ ] I can document API security observations using evidence.
- [ ] I completed the practical exercises only in my own system or an authorized lab.

**Learn responsibly. Test ethically.**
