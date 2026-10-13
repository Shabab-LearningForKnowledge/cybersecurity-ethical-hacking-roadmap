# 🌐 Day 13 — Web Technologies & HTTP Fundamentals

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 13  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In previous lessons, you learned networking, Linux, Nmap, Wireshark, reconnaissance, and DNS enumeration.

Today, you will learn how websites and web applications communicate.

You will learn:

- What a web application is
- How browsers and web servers communicate
- The difference between frontend and backend
- What HTTP and HTTPS are
- How HTTP requests and responses work
- Common HTTP methods
- HTTP status codes
- Headers, parameters, and request bodies
- Cookies and sessions
- How to inspect web traffic using browser developer tools

**Goal:** Understand the communication between a browser and a web server before learning how to assess web application security.

---

## 2. What Is a Web Application?

A web application is software that users access through a web browser or another HTTP-capable client.

Examples include:

- Online banking applications
- E-commerce websites
- Email services
- Online learning platforms
- Customer portals
- Social media platforms

Unlike a simple static website, a web application often allows users to interact with data and perform actions.

For example, an online shopping application may allow users to:

- Register an account
- Log in
- Search for products
- Add items to a cart
- Make purchases
- View order history

Each action may involve communication between the browser and a server.

---

## 3. How Does a Web Application Work?

A typical web application involves a client, a server, and sometimes a database.

```text
User
 |
 v
Web Browser
 |
 | HTTP/HTTPS Request
 v
Web Server / Application
 |
 | Database Query, if required
 v
Database
 |
 | Result
 v
Application Server
 |
 | HTTP/HTTPS Response
 v
Web Browser
 |
 v
User Sees the Result
```

### Example

When you log in to a website:

1. You enter your login details.
2. The browser sends a request.
3. The server processes the request.
4. The application verifies the submitted information.
5. The server returns a response.
6. The browser displays the result.

The actual architecture may contain several services, security controls, and intermediary components.

**Security lesson:** A web application is more than its visible webpage. The browser, server, APIs, authentication mechanisms, and data stores may all affect its security.

---

## 4. Understanding Frontend and Backend

Web applications commonly have two broad components: frontend and backend.

### Frontend

The frontend is the part users interact with through their browser.

Common technologies include:

- HTML
- CSS
- JavaScript

Examples of frontend functionality include forms, menus, buttons, and displayed information.

### Backend

The backend processes requests and implements application logic.

Common backend technologies include:

- Python
- Java
- PHP
- Node.js
- C#

The backend may interact with databases, authentication systems, and external services.

```text
Frontend
HTML + CSS + JavaScript
          |
          v
     HTTP/HTTPS
          |
          v
Backend Application
          |
          v
       Database
```

The exact technologies depend on the application.

**Security lesson:** A control implemented only in the frontend may not protect an operation if the backend fails to enforce the same rule.

---

## 5. What Is HTTP?

HTTP stands for **Hypertext Transfer Protocol**.

It is an application-layer protocol used to exchange requests and responses between clients and servers.

For example, when a browser requests a webpage, it may send an HTTP request to the website's server.

A simplified flow looks like this:

```text
Browser
   |
   | HTTP Request
   v
Web Server
   |
   | HTTP Response
   v
Browser
```

HTTP can carry different types of content, including:

- HTML pages
- JSON data
- Images
- CSS files
- JavaScript files
- Other application resources

HTTP itself does not provide transport encryption. HTTPS protects HTTP communication using TLS.

---

## 6. What Is HTTPS?

HTTPS means HTTP communication protected by **Transport Layer Security (TLS)**.

TLS helps provide:

- Confidentiality through encryption
- Integrity protection
- Server authentication through certificate validation

A simplified comparison:

```text
HTTP
Browser  <------>  Server
Communication is not protected by TLS.

HTTPS
Browser  <======>  Server
Communication is protected using TLS.
```

The arrows are conceptual illustrations, not literal protocol notation.

### Important Points

- HTTPS does not automatically make an application secure.
- Application vulnerabilities may still exist over HTTPS.
- Correct certificate validation matters.
- Server-side authorization is still necessary.
- HTTPS does not guarantee that every application feature is implemented securely.

**Security lesson:** Encryption protects data in transit, but it does not replace secure application design.

---

## 7. Understanding an HTTP Request

An HTTP request is a message sent by a client to a server.

For example:

```http
GET /products HTTP/1.1
Host: example.com
Accept: text/html
User-Agent: ExampleBrowser
```

This example illustrates a request using HTTP/1.1.

### Main Components

| Component | Meaning |
|---|---|
| Method | The requested operation |
| Path | The requested resource |
| HTTP version | The protocol version |
| Host | The requested hostname |
| Headers | Additional request information |
| Body | Optional data sent with the request |

A request body is commonly used with methods such as POST and PUT, although HTTP does not restrict bodies exclusively to those methods.

---

## 8. Understanding an HTTP Response

After receiving a request, a server sends an HTTP response.

Example:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 120
```

The response may also contain a body with HTML, JSON, or another type of content.

### Main Components

| Component | Meaning |
|---|---|
| HTTP version | Protocol version |
| Status code | Result category |
| Reason phrase | Human-readable status text in HTTP/1.1 |
| Headers | Additional response information |
| Body | Optional returned content |

A response does not always contain a body. For example, responses to HEAD requests do not include the response body.

### Example Flow

```text
Browser
   |
   | GET /products
   v
Web Server
   |
   | 200 OK
   | Product page
   v
Browser
```

The status code and response content help the client understand the outcome.

---

## 9. Understanding HTTP Methods

HTTP methods describe the operation a client requests.

| Method | Common purpose |
|---|---|
| GET | Retrieve a resource |
| POST | Submit data or request processing |
| PUT | Create or replace a resource at a specified URI |
| PATCH | Partially modify a resource |
| DELETE | Request deletion of a resource |
| HEAD | Retrieve response headers without the response body |
| OPTIONS | Discover communication options supported by a resource |

### Example

```http
GET /products
```

Requests the products resource.

```http
POST /login
```

Submits information to a login endpoint in this illustrative example.

```http
DELETE /api/items/10
```

Requests deletion of a resource identified as item 10.

**Important:** A method's name alone does not determine the application's actual behavior or authorization requirements. The server must enforce access controls for every sensitive operation.

---

## 10. Understanding HTTP Status Codes

HTTP status codes indicate the general outcome of a request.

### Common Status Codes

| Code | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 301 | Moved Permanently |
| 302 | Found; commonly used for temporary redirects |
| 400 | Bad Request |
| 401 | Unauthorized; authentication is required or has failed in the relevant context |
| 403 | Forbidden |
| 404 | Not Found |
| 405 | Method Not Allowed |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |

### Understanding the Categories

- **1xx:** Informational
- **2xx:** Successful
- **3xx:** Redirection
- **4xx:** Client-error responses
- **5xx:** Server-error responses

A status code does not always tell the complete story. For example, a 404 response may be returned intentionally to avoid revealing whether a protected resource exists.

---

## 11. What Are HTTP Headers?

HTTP headers carry additional information about requests and responses.

Examples include:

```http
Host: example.com
Accept: application/json
Content-Type: application/json
Authorization: Bearer example-token
```

These are illustrative values; never use real credentials in public examples.

### Common Headers

| Header | Purpose |
|---|---|
| Host | Identifies the requested hostname in HTTP/1.1 |
| Accept | Indicates acceptable response media types |
| Content-Type | Describes the media type of the message body |
| Authorization | Carries authentication credentials or tokens |
| Cookie | Sends applicable cookies to the server |
| Set-Cookie | Directs the browser to store or update a cookie |
| Cache-Control | Provides caching directives |
| Location | Identifies a redirect destination or another relevant URI |

Headers can help a tester understand how an application handles content, authentication, cookies, caching, and redirects.

Treat authorization headers, cookies, and tokens as sensitive information.

---

## 12. Understanding URL Components

A URL identifies a resource or endpoint.

Consider this example:

```text
https://example.com:443/products?id=10#details
```

Its components are:

| Component | Value |
|---|---|
| Scheme | `https` |
| Hostname | `example.com` |
| Port | `443` |
| Path | `/products` |
| Query string | `id=10` |
| Fragment | `details` |

### What Do These Components Mean?

- **Scheme:** Indicates the protocol used to access the resource.
- **Hostname:** Identifies the named destination.
- **Port:** Identifies the destination port when specified or implied by the scheme.
- **Path:** Identifies a resource or route.
- **Query string:** Can carry parameters used by the application.
- **Fragment:** Identifies a location within a resource and is generally processed by the client rather than sent in the HTTP request.

Understanding URL components helps you recognize where application inputs may appear.

---

## 13. Understanding Query Parameters and Request Bodies

Applications often receive input through URLs and request bodies.

### Query Parameters

Example:

```text
/products?id=10&category=books
```

Here:

- `id` is a parameter name.
- `10` is its value.
- `category` is another parameter.
- `books` is its value.

### Request Body

A JSON request body might look like:

```json
{
  "productId": 10,
  "quantity": 2
}
```

The application may use these values to process a request.

### Security Perspective

Security testers examine how applications validate input and enforce authorization.

For example, changing a product ID may request a different resource, but whether the user can access that resource must be decided by the server.

**Changing a parameter is not automatically a vulnerability.** The security issue exists only when the application's behavior violates an intended security requirement.

---

## 14. Understanding Cookies

Cookies are small pieces of data that browsers store and send with matching requests according to cookie rules.

Applications often use cookies to maintain login state or store preferences.

Example response header:

```http
Set-Cookie: sessionid=example-value; Secure; HttpOnly; SameSite=Lax
```

This is a fictional example, not a real session cookie.

### Common Cookie Attributes

| Attribute | Purpose |
|---|---|
| Secure | Restricts cookie transmission to secure channels such as HTTPS |
| HttpOnly | Prevents ordinary JavaScript access to the cookie |
| SameSite | Controls when cookies are sent in cross-site contexts |
| Path | Restricts the request paths for which the cookie is sent |
| Domain | Controls the hosts to which the cookie applies, subject to browser rules |
| Expires / Max-Age | Controls cookie lifetime |

Cookie attributes have different purposes and limitations. For example, HttpOnly does not prevent every consequence of a cross-site scripting vulnerability.

Do not share real session cookies or authentication tokens in screenshots, assignments, or public repositories.

---

## 15. Understanding Sessions and Authentication

A session allows an application to associate multiple requests with the same user or client context.

HTTP is fundamentally stateless: each request is processed as a separate message, although applications can maintain state using sessions, cookies, tokens, or other mechanisms.

### Simplified Login Flow

```text
User
 |
 | Submits Login Form
 v
Application Server
 |
 | Validates Credentials
 v
Session Created
 |
 | Session Identifier
 v
Browser Stores Cookie
 |
 | Sends Cookie on Applicable Requests
 v
Server Identifies the Session
```

This is a simplified example; not all applications use cookie-based sessions.

### Important Security Questions

- Is authentication required for sensitive operations?
- Does the server validate the session?
- Is the session identifier protected?
- Does logout invalidate the session appropriately?
- Does the application enforce authorization on each sensitive request?

Authentication determines who a user is. Authorization determines what that user is allowed to do.

---

## 16. Think Like a Security Tester

Imagine that you are inspecting a web application included in an approved assessment.

For a single request, ask:

1. What HTTP method is being used?
2. Which path is requested?
3. Are query parameters present?
4. Are there request headers?
5. Is a request body present?
6. What status code does the server return?
7. Does the response contain cookies?
8. Is HTTPS being used?
9. Is authentication required?
10. Does the server enforce authorization?

### Example Observation

```text
Request:
GET /profile

Response:
200 OK

Observation:
The application returned a response for the profile route.

Further validation:
Determine whether the response belongs to the authenticated user
and whether server-side authorization is enforced.
```

A successful response alone does not establish that the application is secure or vulnerable. You need context and evidence.

---

## 17. Day 13 Practical Exercise

**Use your own local application or an authorized training lab.**

### Exercise 1 — Open Browser Developer Tools

1. Open your training website.
2. Open browser developer tools.
3. Select the **Network** tab.
4. Reload the page.
5. Select a request.

Observe:

- Request URL
- HTTP method
- Status code
- Request headers
- Response headers
- Content type

Browser menus vary, but developer tools are available in most modern browsers.

### Exercise 2 — Identify URL Components

Using this example:

```text
https://example.com/products?id=10
```

Identify:

- Scheme
- Hostname
- Port, if specified
- Path
- Query string

### Exercise 3 — Observe HTTP Status Codes

In your authorized lab, inspect responses and record any status codes you encounter.

For each response, write what the code means and what actually happened.

Do not assume that every website will return the same code for a particular request.

### Exercise 4 — Inspect Request and Response Headers

Choose a request in the Network tab.

Record:

- Method
- Status code
- Content-Type
- Cache-Control, if present
- Set-Cookie, if present

Do not copy actual session values, credentials, tokens, or personal data into your notes.

### Exercise 5 — Understand Cookies

Using your own test application, inspect the cookie names and available attributes in the browser's developer tools.

Record which attributes are present without publishing cookie values.

### Exercise 6 — Review an API Request

If your authorized lab has an API endpoint, identify:

- Endpoint path
- HTTP method
- Content type
- Parameters or request body
- Response status
- Response content type

Do not modify sensitive resources or test other users' access without explicit authorization.

---

## 18. Day 13 Assignment

Complete the following tasks using an authorized application or a local training environment.

### Task 1

Explain the difference between a website and a web application.

### Task 2

Describe the role of the frontend, backend, and database.

### Task 3

Explain the difference between HTTP and HTTPS.

### Task 4

Write the purpose of these HTTP methods:

- GET
- POST
- PUT
- PATCH
- DELETE
- HEAD
- OPTIONS

### Task 5

Explain these status codes:

- 200
- 301
- 400
- 401
- 403
- 404
- 429
- 500

### Task 6

Capture the details of five requests from your authorized lab.

Record the method, path, status code, and content type where available. Exclude sensitive values.

### Task 7

Explain the difference between authentication and authorization.

### Task 8

Describe the purpose of the Secure, HttpOnly, and SameSite cookie attributes.

**Submission guideline:** Use fictional or lab data in your public repository. Never publish passwords, session cookies, bearer tokens, private application details, or confidential assessment evidence.

---

## 19. Day 13 Quick Quiz

### Question 1

What does HTTP stand for?

A. Hypertext Transfer Protocol  
B. High Transfer Text Process  
C. Host Transmission Tool Protocol  
D. Hyperlink Tracking Program

### Question 2

Which protocol protects HTTP communication using TLS?

A. FTP  
B. HTTPS  
C. DNS  
D. ARP

### Question 3

Which HTTP method is commonly used to retrieve a resource?

A. DELETE  
B. PATCH  
C. GET  
D. CONNECT

### Question 4

What does HTTP status code 403 generally indicate?

A. Successful creation  
B. Forbidden  
C. Permanent redirect  
D. Internal server error

### Question 5

Which header is commonly used to send cookies from a browser?

A. Set-Cookie  
B. Cookie  
C. Content-Type  
D. Location

### Question 6

What is the purpose of the HttpOnly cookie attribute?

A. It encrypts the entire website  
B. It prevents ordinary JavaScript access to the cookie  
C. It blocks every form of cross-site scripting  
D. It disables HTTPS

### Question 7

What is the difference between authentication and authorization?

A. They mean exactly the same thing  
B. Authentication establishes identity; authorization determines permitted actions  
C. Authentication encrypts data; authorization resolves DNS  
D. Authorization is only used for DNS records

### Question 8

Does using HTTPS guarantee that a web application is free of vulnerabilities?

A. Yes  
B. No

---

## 20. Quiz Answers

| Question | Answer | Explanation |
|---|---|---|
| 1 | A | HTTP stands for Hypertext Transfer Protocol. |
| 2 | B | HTTPS protects HTTP communication using TLS. |
| 3 | C | GET is commonly used to retrieve a resource. |
| 4 | B | 403 generally indicates that the server refuses the request. |
| 5 | B | The Cookie header sends applicable cookies in a request. |
| 6 | B | HttpOnly restricts ordinary JavaScript access to a cookie. |
| 7 | B | Authentication establishes identity; authorization controls permitted actions. |
| 8 | B | HTTPS protects data in transit but does not eliminate application vulnerabilities. |

---

## 21. Key Takeaways

By completing Day 13, you should understand:

- What web applications are
- How browsers and servers communicate
- The difference between frontend and backend
- The purpose of HTTP and HTTPS
- How to read HTTP requests and responses
- Common HTTP methods and status codes
- The role of headers and URL parameters
- How cookies and sessions work
- The difference between authentication and authorization
- How to inspect web traffic using browser developer tools

Remember:

**A web application security tester must understand how an application communicates before assessing how securely it behaves.**

---

## Day 13 Completion Checklist

- [ ] I understand how a web application works.
- [ ] I can explain frontend and backend components.
- [ ] I understand HTTP and HTTPS.
- [ ] I can identify HTTP methods.
- [ ] I understand common HTTP status codes.
- [ ] I can identify URL components and query parameters.
- [ ] I understand HTTP headers and request bodies.
- [ ] I understand cookies and sessions.
- [ ] I can distinguish authentication from authorization.
- [ ] I can inspect basic web requests in browser developer tools.

**Learn responsibly. Test ethically.**
