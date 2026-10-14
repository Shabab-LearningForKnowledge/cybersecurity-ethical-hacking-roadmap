# 🌐 Day 14 — Web Application Architecture & Request Flow

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 14  
**Level:** Beginner  
**Estimated Time:** 75–90 minutes

---

## 1. What Will You Learn Today?

In Day 13, you learned about HTTP requests, responses, headers, cookies, and sessions.

Today, you will learn how the different components of a web application work together.

You will learn:

- What web application architecture means
- The difference between two-tier and three-tier architecture
- How browsers communicate with web servers
- The role of application servers and databases
- How APIs fit into an application
- What reverse proxies and load balancers do
- How authentication and authorization fit into request processing
- What happens when a user logs in or accesses a protected page
- How to identify important components from a security perspective

**Goal:** Understand the architecture and request flow of a web application so you can reason about its security boundaries.

---

## 2. What Is Web Application Architecture?

Web application architecture describes how the different components of an application are organized and communicate.

A typical application may contain:

- A browser or mobile client
- A web server
- An application server
- A database
- An API
- Authentication services
- External integrations

These components work together to deliver application functionality.

### Example

When a user checks their bank account balance, the browser may send a request to the application. The application verifies the user's identity and permissions, retrieves the appropriate information, and returns a response.

```text
User
  |
  v
Browser
  |
  v
Web Application
  |
  v
Database
  |
  v
Account Information
```

This is a simplified illustration. Real applications may contain many additional components and security controls.

---

## 3. Understanding Two-Tier Architecture

Two-tier architecture separates an application into two main tiers.

A simplified example is a client communicating directly with a database server.

```text
Client Application
        |
        | Request
        v
Database Server
        |
        | Response
        v
Client Application
```

### Components

**Tier 1 — Client**

The client presents the interface and sends requests.

**Tier 2 — Data Server**

The database stores and retrieves application data.

### Security Considerations

Depending on the design, direct client-to-database communication may expose database services to clients and complicate authorization.

Possible concerns include:

- Excessive database access
- Exposed database credentials
- Insufficient separation of responsibilities
- Inadequate network restrictions

Two-tier designs are not automatically insecure. Their security depends on the implementation, trust boundaries, and controls in place.

---

## 4. Understanding Three-Tier Architecture

Three-tier architecture separates the application into three logical layers.

```text
┌──────────────────────────┐
│ Presentation Tier        │
│ Browser / User Interface │
└────────────┬─────────────┘
             |
             v
┌──────────────────────────┐
│ Application Tier         │
│ Business Logic / APIs    │
└────────────┬─────────────┘
             |
             v
┌──────────────────────────┐
│ Data Tier                │
│ Database                 │
└──────────────────────────┘
```

### Tier 1 — Presentation

Displays information and collects user input.

### Tier 2 — Application

Processes requests, applies business rules, and performs authentication and authorization checks.

### Tier 3 — Data

Stores and retrieves information.

### Why Is This Useful?

Separating responsibilities can help organizations apply different security controls to each layer.

For example:

- The database may accept connections only from approved application servers.
- The application may enforce user permissions.
- The browser may receive only the information required for its task.

**Important:** Separating an application into tiers does not automatically make it secure. Each tier must be configured and implemented correctly.

---

## 5. Understanding the Client-Server Model

The client-server model describes how one system requests a service from another system.

In a web application:

- The browser usually acts as the client.
- The web application receives and processes requests.
- The server returns responses.

### Example

A user opens:

```text
https://example.com/products
```

A simplified flow is:

```text
Browser
   |
   | GET /products
   v
Web Server / Application
   |
   | Process Request
   v
HTTP Response
   |
   v
Browser Displays Page
```

The server may return HTML directly or return data that the browser uses to construct the interface.

Modern applications may also communicate with several backend services during a single user action.

---

## 6. What Is a Web Server?

A web server accepts HTTP requests and sends HTTP responses.

Common web server software includes:

- Nginx
- Apache HTTP Server
- Microsoft IIS

Depending on the architecture, a web server may:

- Serve static files
- Forward requests to an application
- Handle TLS connections
- Apply routing rules
- Generate access logs
- Apply certain request restrictions

### Static vs Dynamic Content

**Static content** may include images, CSS files, and JavaScript files served without generating application-specific content for each request.

**Dynamic content** is generated or retrieved based on application logic, request parameters, user state, or stored data.

A web server may serve static content and forward dynamic requests to an application server.

---

## 7. What Is an Application Server?

An application server runs or supports the application logic that processes requests.

It may handle:

- Login processing
- Business rules
- User permissions
- API operations
- Data validation
- Database queries
- Integration with external services

### Example

Imagine a shopping application receives:

```text
GET /api/orders/1001
```

The application may need to:

1. Identify the requesting user.
2. Validate the session or token.
3. Check whether the user can access order `1001`.
4. Retrieve the permitted order data.
5. Return an appropriate response.

The application server must enforce access controls. Hiding a button in the frontend is not a substitute for server-side authorization.

---

## 8. Understanding the Database Layer

A database stores and retrieves information used by an application.

Examples include:

- User profiles
- Product information
- Order records
- Transaction records
- Application settings

Common database technologies include MySQL, PostgreSQL, Microsoft SQL Server, and MongoDB.

### Example Request Flow

```text
Browser
   |
   v
Application Server
   |
   | Authorized Data Query
   v
Database
   |
   | Query Result
   v
Application Server
   |
   v
Browser
```

The application typically communicates with the database using a database driver, connection pool, or another supported interface.

### Security Considerations

- Database access should follow least privilege.
- Credentials should be protected.
- Queries should handle user input safely.
- Sensitive data should be protected.
- Database services should not be exposed unnecessarily.
- Access to records should follow application authorization rules.

A database should not be assumed secure simply because it is not directly visible in the browser.

---

## 9. Understanding APIs in Web Applications

API stands for **Application Programming Interface**.

A web API allows software components to communicate through defined interfaces.

For example, a frontend may request product data from an API.

```text
Browser
   |
   | GET /api/products
   v
API / Application
   |
   v
Database
   |
   v
API Response
   |
   v
Browser
```

An API may return JSON such as:

```json
{
  "productId": 101,
  "name": "Laptop",
  "available": true
}
```

This is an illustrative response.

### Why Are APIs Important?

APIs may expose operations to:

- Retrieve information
- Create records
- Update records
- Delete records
- Process transactions

Every sensitive API operation should enforce appropriate authentication, authorization, input validation, and business rules.

An API is not secure merely because it is not directly linked from a webpage.

---

## 10. What Is a Reverse Proxy?

A reverse proxy sits in front of one or more backend servers and forwards client requests to them.

```text
Client
  |
  v
Reverse Proxy
  |
  ├────────> Application Server A
  |
  └────────> Application Server B
```

A reverse proxy may provide:

- Request routing
- TLS termination
- Load distribution
- Access logging
- Request filtering
- Connection management

Some reverse proxies also support security features, but their presence does not guarantee that an application is protected from every attack.

### Security Perspective

A tester may need to understand whether a request passes through a proxy and how the application handles forwarded headers.

Headers such as `X-Forwarded-For` may carry information about the original client address in some deployments. Their values must be trusted only when set or validated through an appropriate trusted proxy configuration.

---

## 11. What Is a Load Balancer?

A load balancer distributes incoming requests across multiple backend systems.

For example:

```text
                 Client
                   |
                   v
             Load Balancer
               /       \
              v         v
          Server A   Server B
```

Load balancing can help improve availability and distribute workload.

### Why Is It Used?

- Handle more requests
- Improve availability
- Distribute traffic
- Support maintenance and scaling

### Security Considerations

The configuration should ensure that:

- Backend systems are not unnecessarily exposed.
- Security controls are consistent across instances.
- Sensitive management interfaces are protected.
- Authentication and authorization do not depend on which server receives the request.

A load balancer is an infrastructure component, not proof that an application is secure.

---

## 12. Understanding Authentication and Authorization in Request Flow

Authentication and authorization are separate security functions.

**Authentication:** Determines or verifies the identity of the requester.

**Authorization:** Determines what the requester is allowed to do.

### Example

A user logs in successfully and requests:

```text
GET /api/profile
```

The application may authenticate the user and return their profile.

The same user then requests:

```text
GET /api/admin/users
```

The application must separately verify whether the user has permission to access administrative information.

### Important Principle

```text
Successful Login
       |
       v
Authenticated User
       |
       v
Authorization Check
       |
       v
Allow or Deny the Operation
```

A valid session does not automatically authorize every action.

---

## 13. Understanding Trust Boundaries

A trust boundary is a point where information crosses between components or security zones with different levels of trust.

Examples include:

- Browser to web server
- Web server to application server
- Application server to database
- Application to an external API
- Public network to an internal service

```text
Untrusted Client
       |
       | Trust Boundary
       v
Public Web Layer
       |
       | Trust Boundary
       v
Application Layer
       |
       | Trust Boundary
       v
Database Layer
```

Trust boundaries are useful when reasoning about security controls.

At each boundary, ask:

- Is the incoming data validated?
- Is the caller authenticated where necessary?
- Is authorization enforced?
- Is communication protected where required?
- Are errors handled safely?
- Is access logged appropriately?

Do not assume that data is trustworthy simply because it comes from another component inside the same organization.

---

## 14. Understanding the Complete Request Flow

Let's combine the concepts.

Imagine a user opens their profile page in a web application.

```text
1. User opens the profile page
             |
             v
2. Browser sends HTTPS request
             |
             v
3. Reverse proxy receives request, if present
             |
             v
4. Web server routes the request
             |
             v
5. Application validates the session
             |
             v
6. Application checks authorization
             |
             v
7. Application retrieves permitted data
             |
             v
8. Application creates a response
             |
             v
9. Browser receives and displays the result
```

Real systems may have different arrangements, and not every request follows every step.

### Security Tester Perspective

If something goes wrong, ask which component should have prevented it.

For example:

- Did the application validate the session?
- Was the user's authorization checked?
- Was the database query appropriately restricted?
- Did the response contain more data than required?

This approach helps connect application behavior to the underlying architecture.

---

## 15. Common Architecture-Related Security Mistakes

Architecture can introduce security risks when components are poorly designed or configured.

| Potential issue | Why it matters |
|---|---|
| Database directly exposed to untrusted networks | Increases unnecessary access paths |
| Missing server-side authorization | May allow access to another user's resources |
| Excessive database privileges | Increases the impact of a compromised application |
| Trusting client-side validation alone | Requests may bypass frontend restrictions |
| Exposed management interfaces | May provide access to sensitive administrative functions |
| Inconsistent security controls across servers | May produce different behavior between instances |
| Misconfigured forwarded headers | May cause incorrect trust decisions about client information |
| Excessive API responses | May disclose information the client does not need |

These are potential weaknesses, not findings that can be confirmed from architecture diagrams alone.

A proper assessment requires authorized testing and evidence.

---

## 16. Think Like a Security Tester

Consider this fictional application:

```text
Browser
   |
   v
Reverse Proxy
   |
   v
Application Server
   |
   ├────> Authentication Service
   |
   └────> Database
```

The application contains a profile page and an administrative API.

Ask yourself:

1. Which component receives the initial request?
2. Which component should validate the user's session?
3. Which component should enforce authorization?
4. Should the browser connect directly to the database?
5. What should happen if a user requests another user's profile?
6. What should happen if an ordinary user requests an administrative API?
7. What information should be included in the response?
8. Which security controls should apply between components?

A good tester does not simply identify components. They understand what each component is responsible for and verify whether the expected controls are enforced.

---

## 17. Day 14 Practical Exercise

**Use your own local application or an authorized training environment.**

### Exercise 1 — Draw an Architecture Diagram

Draw a simple diagram showing:

- Browser
- Web server
- Application server
- Database
- API, if present

If you do not know the real architecture of your lab, label the diagram as a conceptual model rather than claiming it represents the actual implementation.

### Exercise 2 — Inspect Browser Requests

1. Open your training application.
2. Open browser developer tools.
3. Select the Network tab.
4. Reload the page.
5. Select one request.

Record:

- Request URL
- HTTP method
- Status code
- Content type
- Whether the response contains HTML or JSON

Do not publish authentication tokens, cookies, or personal data.

### Exercise 3 — Identify API Communication

If your lab uses an API, identify one API request.

Record:

- Endpoint path
- Request method
- Response status
- Response content type

Do not modify or delete resources unless the exercise explicitly permits it.

### Exercise 4 — Trace a Request

Select a request and explain the likely sequence of components that process it.

Separate observed facts from assumptions. Browser tools show client-side requests and responses, but they generally do not reveal the complete backend architecture.

### Exercise 5 — Review Security Boundaries

For each boundary in your conceptual diagram, write one security question.

Example:

```text
Browser → Application:
Is the request authenticated where required?

Application → Database:
Does the application use appropriately restricted database access?
```

---

## 18. Day 14 Assignment

Complete the following tasks using a local or instructor-approved application.

### Task 1

Explain web application architecture in your own words.

### Task 2

Compare two-tier and three-tier architecture.

### Task 3

Explain the roles of:

- Web server
- Application server
- Database
- API
- Reverse proxy
- Load balancer

### Task 4

Draw a request-flow diagram for a user accessing their profile page.

### Task 5

Explain the difference between authentication and authorization.

### Task 6

Identify three trust boundaries in a typical web application and describe one security consideration for each.

### Task 7

Explain why frontend validation cannot replace server-side validation and authorization.

### Task 8

Write five questions a security tester should ask when reviewing an application's architecture.

**Submission guideline:** Use fictional diagrams or authorized lab information. Do not upload confidential architecture diagrams, internal network details, credentials, or proprietary application information to a public repository.

---

## 19. Day 14 Quick Quiz

### Question 1

What does web application architecture describe?

A. Only the appearance of a webpage  
B. How application components are organized and communicate  
C. Only the database schema  
D. Only the domain name

### Question 2

Which component commonly processes application business logic?

A. Application server  
B. Keyboard  
C. DNS record  
D. Image file

### Question 3

What is the primary purpose of a reverse proxy?

A. Store all application data permanently  
B. Forward and manage requests to backend systems  
C. Replace authentication  
D. Resolve every DNS query

### Question 4

What does a load balancer commonly do?

A. Distributes requests across backend systems  
B. Encrypts every database field automatically  
C. Creates user passwords  
D. Replaces authorization checks

### Question 5

What is a trust boundary?

A. A point where components or zones with different trust levels interact  
B. A type of HTTP status code  
C. A database table  
D. A DNS query

### Question 6

Does a successful login automatically authorize a user to access every API?

A. Yes  
B. No

### Question 7

Why should a database usually avoid unnecessary direct exposure to untrusted networks?

A. To reduce unnecessary access paths  
B. To make the website invisible to users  
C. To remove HTTP headers  
D. To disable all application functions

### Question 8

Can browser developer tools reveal every detail of the backend architecture?

A. Yes  
B. No

---

## 20. Quiz Answers

| Question | Answer | Explanation |
|---|---|---|
| 1 | B | Architecture describes the organization and communication of application components. |
| 2 | A | The application server commonly processes business logic. |
| 3 | B | A reverse proxy forwards and manages requests to backend systems. |
| 4 | A | A load balancer distributes requests across backend systems. |
| 5 | A | A trust boundary separates components or zones with different trust levels. |
| 6 | B | Each sensitive operation still requires appropriate authorization. |
| 7 | A | Restricting unnecessary exposure reduces access paths to the database. |
| 8 | B | Browser tools reveal client-side communication, not the entire backend implementation. |

---

## 21. Key Takeaways

By completing Day 14, you should understand:

- What web application architecture means
- The difference between two-tier and three-tier architecture
- The roles of browsers, web servers, application servers, and databases
- How APIs support application communication
- The purposes of reverse proxies and load balancers
- How authentication and authorization fit into request processing
- What trust boundaries are
- How to trace a request through a conceptual architecture
- Common architecture-related security mistakes
- Why server-side security controls are essential

Remember:

**Understanding how an application is built helps you understand where security controls should exist and how to evaluate their effectiveness.**

---

## Day 14 Completion Checklist

- [ ] I understand web application architecture.
- [ ] I can explain two-tier and three-tier architecture.
- [ ] I understand the roles of web and application servers.
- [ ] I understand the purpose of databases and APIs.
- [ ] I can explain reverse proxies and load balancers.
- [ ] I understand authentication and authorization in request flow.
- [ ] I can identify trust boundaries in a conceptual diagram.
- [ ] I can trace a basic browser request.
- [ ] I understand why backend security controls are essential.
- [ ] I can document architecture observations without confusing assumptions with verified facts.

**Learn responsibly. Test ethically.**
