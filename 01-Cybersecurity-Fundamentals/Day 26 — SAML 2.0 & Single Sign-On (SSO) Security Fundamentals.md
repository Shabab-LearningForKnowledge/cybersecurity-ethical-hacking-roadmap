# Day 26 — SAML 2.0 & Single Sign-On (SSO) Security Fundamentals

## 1. Introduction

Have you ever logged into your company's application and then accessed multiple internal applications without entering your password again?

For example:

- Company email
- HR portal
- Employee management system
- Internal ticketing application
- Company learning platform

This functionality is commonly implemented using **Single Sign-On (SSO)**.

One technology used to implement enterprise SSO is **Security Assertion Markup Language (SAML)**.

In this lesson, we will understand how SAML works, how applications exchange authentication information, and what security testers should examine during an SSO assessment.

## 2. What Is Single Sign-On (SSO)?

Single Sign-On allows users to authenticate once through a trusted identity provider and access multiple connected applications without repeatedly entering their credentials.

**Simple example:**

Imagine your company provides access to five internal applications.

Without SSO, you may need separate login credentials for each application.

With SSO:

1. You open the company HR portal.
2. You are redirected to the company's identity provider.
3. You authenticate using your company credentials.
4. The identity provider confirms your identity.
5. You can access the HR portal and other connected applications according to their access rules.

**Important:** SSO does not mean that every application automatically grants the same permissions. Each application must still enforce its own authorization rules.

## 3. What Is SAML 2.0?

SAML stands for **Security Assertion Markup Language**.

It is an XML-based open standard used to exchange authentication and authorization-related information between different security domains.

SAML 2.0 is commonly used in enterprise environments to implement browser-based Single Sign-On.

**Example:**

Your company uses Microsoft Entra ID as its identity provider and an internal HR application as the service provider.

When you log in to the HR application, the identity provider can send a signed SAML response containing an authentication assertion.

The HR application validates that response before creating an application session.

## 4. Important Components of SAML

Before understanding the complete flow, let's understand its main components.

### 1. Identity Provider (IdP)

The Identity Provider authenticates users and generates SAML responses.

Example: An enterprise identity management platform.

### 2. Service Provider (SP)

The Service Provider is the application that trusts the identity provider's authentication information.

Example: Company HR portal.

### 3. User / Browser

The user accesses the service provider through a web browser.

### 4. Assertion Consumer Service (ACS)

The ACS is the endpoint on the service provider that receives the SAML response.

**Simple example:**

- IdP: Authenticates the employee.
- SP: Company HR portal.
- Browser: Carries the SAML response.
- ACS: Receives the response at the HR portal.

## 5. Understanding SAML Authentication Flow

Let's understand how SAML SSO works step by step.

**Example: Employee accessing the HR portal**

1. Employee opens the HR portal.
2. The HR portal redirects the browser to the Identity Provider.
3. The Identity Provider checks whether the employee is authenticated.
4. If required, the employee enters their credentials.
5. The Identity Provider generates a SAML response.
6. The browser sends the response to the Service Provider's ACS endpoint.
7. The Service Provider validates the response and assertion.
8. If validation succeeds, the application creates a session for the employee.

**Simplified flow:**

```text
       User / Browser
              |
              v
       HR Application
        (Service Provider)
              |
              | Redirect to IdP
              v
       Identity Provider
              |
              | User Authentication
              |
              v
       SAML Response Generated
              |
              v
       User / Browser
              |
              | POST SAML Response
              v
       ACS Endpoint
              |
              | Validate SAML Response
              v
       HR Application
              |
              v
       User Session Created
```

The browser carries the SAML response between the IdP and SP. The SP must not blindly trust it.

## 6. What Is a SAML Assertion?

A SAML assertion is an XML document containing statements about a subject, such as an authenticated user.

It may contain:

- User identity information
- Authentication details
- Attributes such as email or department
- Validity conditions
- Audience information

**Simplified example:**

```xml
<saml:Assertion>
    <saml:Issuer>
        https://idp.example.com
    </saml:Issuer>

    <saml:Subject>
        <saml:NameID>
            employee@example.com
        </saml:NameID>
    </saml:Subject>

    <saml:Conditions>
        <!-- Validity conditions -->
    </saml:Conditions>

    <saml:AuthnStatement>
        <!-- Authentication information -->
    </saml:AuthnStatement>
</saml:Assertion>
```

This is only a simplified illustration, not a complete valid SAML assertion.

**Security perspective:** The service provider must validate the assertion's signature, issuer, audience, validity period, and other relevant conditions before accepting it.

## 7. Understanding SAML Request and Response

SAML communication commonly involves two important messages.

### SAML Authentication Request

The Service Provider sends an authentication request to the Identity Provider.

It is commonly called an `AuthnRequest`.

Its purpose is to request user authentication.

### SAML Response

The Identity Provider sends a response after processing the authentication request.

It may contain:

- Authentication information
- User attributes
- A SAML assertion
- Digital signature information

**Remember:**

The authentication request asks the IdP to authenticate the user.

The SAML response carries the result and associated assertion information.

## 8. What Is an Assertion Consumer Service (ACS)?

The Assertion Consumer Service is an endpoint provided by the Service Provider.

It receives SAML responses from the Identity Provider.

Example:

```text
https://hr.example.com/saml/acs
```

When the browser submits a SAML response to this endpoint, the service provider validates it.

**Security testing perspective:**

A tester should understand:

- Which ACS endpoint is configured?
- Is the response accepted only at the intended endpoint?
- Is the response's destination validated?
- Is the assertion intended for the correct service provider?
- Are signatures and validity conditions correctly checked?

A response reaching the ACS endpoint does not mean it is automatically trusted.

## 9. Important SAML Elements and Attributes

During an assessment, you may encounter the following elements.

| Element / Attribute | Purpose |
|---|---|
| `Issuer` | Identifies the issuing identity provider |
| `NameID` | Identifies the subject |
| `Audience` | Identifies the intended recipient or service |
| `Destination` | Identifies the intended response destination |
| `Recipient` | Identifies the intended assertion recipient |
| `InResponseTo` | Correlates a response with its request |
| `Conditions` | Defines assertion validity conditions |
| `NotBefore` | Indicates when the assertion becomes valid |
| `NotOnOrAfter` | Indicates when the assertion expires |
| `AuthnStatement` | Contains authentication-related information |
| `AttributeStatement` | Contains user attributes |

These values must be validated according to the SAML profile and the application's configuration.

## 10. What Is a Digital Signature in SAML?

Digital signatures help verify the integrity and authenticity of SAML messages or assertions.

A SAML response or assertion may contain a digital signature.

The service provider uses trusted signing certificates and appropriate validation rules to verify the signature.

**Simple example:**

Imagine the Identity Provider sends a signed assertion containing:

```text
Username: employee@example.com
Department: IT
Role: Employee
```

If someone modifies the signed content, the signature verification should fail.

**Important:**

- A signature is not encryption.
- Signing helps protect integrity and authenticity.
- Encryption helps protect confidentiality.
- A valid signature alone does not prove that every field is appropriate for the application.

## 11. Common SAML Security Vulnerabilities

Now let's understand common weaknesses that security testers should look for.

### 1. Improper Signature Validation

If the Service Provider does not correctly validate the SAML signature, it may accept untrusted authentication information.

### 2. XML Signature Wrapping

XML Signature Wrapping occurs when an application incorrectly processes signed and unsigned XML elements.

In some implementations, an application may validate one element but use a different, attacker-controlled element for authentication.

### 3. Improper Audience Validation

The assertion should be intended for the correct Service Provider.

If audience validation is missing or incorrect, an assertion may be accepted in an unintended context.

### 4. Assertion Replay

An assertion may be reused if the application does not properly enforce replay protections.

### 5. Improper Time Validation

The Service Provider should check assertion validity conditions such as `NotBefore` and `NotOnOrAfter`.

### 6. Improper Issuer Validation

The Service Provider must trust only the expected identity provider.

### 7. Insecure RelayState Handling

RelayState is used to maintain state or carry information during SAML redirects.

If an application handles it insecurely, it may introduce issues such as open redirects.

### 8. Incorrect Recipient or Destination Validation

The response and assertion should be intended for the correct endpoint and service provider.

## 12. What Is SAML Response Replay?

A SAML assertion is intended to be accepted under specific conditions and within its validity period.

A replay vulnerability may occur when an application accepts the same authentication assertion more than once when it should not.

**Example:**

1. A user successfully authenticates.
2. A SAML response is captured in a controlled test environment.
3. The same response is submitted again.
4. The application accepts it and creates another authenticated session.

This behavior may indicate insufficient replay protection.

**Security testing perspective:**

Applications should enforce appropriate assertion validity checks and replay protections, such as tracking assertion identifiers where required.

Never attempt replay testing against real user sessions or production accounts without explicit authorization.

## 13. SAML HTTP Bindings

SAML uses different HTTP bindings to transport messages.

Two commonly encountered bindings are:

### HTTP Redirect Binding

SAML messages are transmitted through HTTP redirects, generally using URL query parameters.

### HTTP POST Binding

SAML messages are transmitted through an HTTP POST request, commonly using a browser form submission.

**Example of a simplified POST request:**

```http
POST /saml/acs HTTP/1.1
Host: hr.example.com
Content-Type: application/x-www-form-urlencoded

SAMLResponse=BASE64_ENCODED_VALUE
```

The `SAMLResponse` value is commonly Base64-encoded XML.

**Important:** Base64 is encoding, not encryption. Decoding a SAML response does not require breaking encryption.

## 14. SAML vs OAuth 2.0 vs OpenID Connect

You have already studied OAuth 2.0 and OpenID Connect in Day 25.

Now let's compare all three technologies.

| Feature | SAML 2.0 | OAuth 2.0 | OpenID Connect |
|---|---|---|---|
| Main purpose | Federated identity / SSO | Authorization | Authentication |
| Common format | XML | Token-based framework | JSON / JWT commonly |
| Common use | Enterprise SSO | API access | Modern application login |
| Identity assertion | SAML assertion | Not defined by OAuth itself | ID Token |
| Typical environment | Enterprise applications | APIs and delegated access | Web and mobile login |

**Remember:**

- SAML is commonly used for enterprise Single Sign-On.
- OAuth 2.0 is primarily an authorization framework.
- OpenID Connect adds an authentication layer to OAuth 2.0.

They solve related but different problems.

## 15. How to Identify SAML in Burp Suite

Burp Suite can help you inspect SAML communication during an authorized security assessment.

Look for:

- `/saml`
- `/sso`
- `/acs`
- `SAMLRequest`
- `SAMLResponse`
- `RelayState`

**Basic approach:**

1. Open Burp Suite.
2. Configure your browser to use Burp Proxy.
3. Access an authorized SAML-enabled test application.
4. Start the login process.
5. Capture the HTTP requests and responses.
6. Identify the SAML authentication request.
7. Identify the SAML response.
8. Decode the Base64-encoded message where applicable.
9. Inspect the XML structure.
10. Review the relevant security validations.

You may also use a SAML inspection extension such as SAML Raider in a controlled lab.

**Important:** Decoding a SAML response is only an inspection step. It does not prove that the application has a vulnerability.

## 16. Practical Lab: Inspecting a SAML Authentication Flow

**Objective:** Understand how a SAML-enabled application exchanges authentication information.

Use a dedicated SAML training environment or an application that you own or are explicitly authorized to test.

### Step 1: Start Burp Suite

- Open Burp Suite.
- Create a temporary project.
- Launch the configured browser.

### Step 2: Access the SAML Lab

Open your authorized SAML training application.

### Step 3: Start Login

Click the SSO or login option.

Observe the browser redirects in Burp Proxy.

### Step 4: Identify SAML Parameters

Look for:

```text
SAMLRequest
SAMLResponse
RelayState
```

### Step 5: Inspect the SAML Response

If the response is Base64-encoded, decode it in your local lab environment.

Identify:

- Issuer
- NameID
- Audience
- Destination
- Recipient
- Conditions
- Authentication statement
- Signature information

### Step 6: Document Your Observations

| Item | Observation |
|---|---|
| Identity Provider | Record the configured IdP |
| Service Provider | Record the application |
| ACS Endpoint | Record the response endpoint |
| Issuer | Identify the issuer |
| Audience | Identify the intended audience |
| Validity Conditions | Review time restrictions |
| Signature | Identify signature information |

Do not modify or replay authentication messages unless the lab explicitly permits that activity.

## 17. SAML Security Testing Checklist

Use this checklist during an authorized assessment.

- [ ] Identify the Identity Provider.
- [ ] Identify the Service Provider.
- [ ] Identify the ACS endpoint.
- [ ] Review SAML request and response handling.
- [ ] Verify signature validation.
- [ ] Review trusted signing certificates.
- [ ] Verify issuer validation.
- [ ] Verify audience validation.
- [ ] Review destination and recipient validation.
- [ ] Check assertion expiration and validity conditions.
- [ ] Review assertion replay protection.
- [ ] Check RelayState handling.
- [ ] Review XML parsing and signature processing.
- [ ] Verify that user attributes and roles are handled securely.
- [ ] Document evidence and business impact.

## 18. How to Report SAML Vulnerabilities

A security report should clearly explain the issue and its impact.

**Example report:**

**Finding Title:** Improper SAML Assertion Validation

**Description:**

The application does not correctly validate the intended audience of the received SAML assertion.

**Impact:**

Depending on the trust relationship and implementation, an improperly validated assertion may be accepted by an unintended service provider, potentially resulting in unauthorized authentication.

**Evidence:**

- Captured SAML response from the authorized test environment.
- Relevant assertion audience.
- Observed application validation behavior.

**Recommendation:**

- Validate the expected audience.
- Validate issuer and recipient information.
- Verify signatures using trusted certificates.
- Enforce assertion validity conditions.
- Follow the applicable SAML security profiles.

**Severity:** Determine based on the actual exploitability and business impact. Do not assign severity based only on the vulnerability title.

## 19. Assignment

Complete the following activities using an authorized SAML training environment.

1. Explain Single Sign-On in your own words.
2. Explain what SAML 2.0 is.
3. Identify the difference between an Identity Provider and a Service Provider.
4. Draw the SAML authentication flow.
5. Explain the purpose of a SAML assertion.
6. Identify the purpose of the ACS endpoint.
7. Explain the difference between a SAML request and response.
8. Explain why digital signatures are important.
9. Identify at least five common SAML security weaknesses.
10. Explain the difference between SAML, OAuth 2.0, and OpenID Connect.
11. Capture and document a SAML authentication flow using Burp Suite.

## 20. Knowledge Check

**Q1. What does SAML stand for?**

A. Secure Application Management Language  
B. Security Assertion Markup Language  
C. System Authentication Management Layer  
D. Security Access Monitoring Language  

**Answer:** B. Security Assertion Markup Language

**Q2. What is the primary purpose of SAML in enterprise applications?**

A. Network scanning  
B. Database encryption  
C. Federated identity and Single Sign-On  
D. File compression  

**Answer:** C. Federated identity and Single Sign-On

**Q3. Which component authenticates the user and generates SAML responses?**

A. Service Provider  
B. Identity Provider  
C. Database Server  
D. Web Browser  

**Answer:** B. Identity Provider

**Q4. What is the purpose of the ACS endpoint?**

A. To scan ports  
B. To receive SAML responses  
C. To encrypt databases  
D. To manage DNS records  

**Answer:** B. To receive SAML responses

**Q5. What format is commonly used for SAML assertions?**

A. XML  
B. CSV  
C. Plain text only  
D. YAML  

**Answer:** A. XML

**Q6. What does the Audience element help identify?**

A. The user's password  
B. The intended Service Provider  
C. The user's IP address  
D. The browser version  

**Answer:** B. The intended Service Provider

**Q7. What is XML Signature Wrapping?**

A. A network routing technique  
B. An XML signature-processing weakness  
C. A password hashing algorithm  
D. A DNS configuration issue  

**Answer:** B. An XML signature-processing weakness

**Q8. Is Base64 encoding the same as encryption?**

A. Yes  
B. No  

**Answer:** B. No

## 21. Key Takeaways

After completing Day 26, you should understand:

- What Single Sign-On is.
- What SAML 2.0 is.
- The difference between Identity Provider and Service Provider.
- How SAML authentication works.
- What SAML assertions contain.
- The purpose of ACS endpoints.
- Important SAML attributes and elements.
- Digital signatures and their importance.
- Common SAML security vulnerabilities.
- HTTP Redirect and POST bindings.
- The differences between SAML, OAuth 2.0, and OpenID Connect.
- How to inspect SAML communication using Burp Suite.

### Further Learning Resources

1. OASIS SAML 2.0 Technical Overview: https://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html
2. OASIS SAML 2.0 Core Specification: https://docs.oasis-open.org/security/saml/v2.0/
3. OWASP Web Security Testing Guide: https://owasp.org/www-project-web-security-testing-guide/
4. PortSwigger Web Security Academy: https://portswigger.net/web-security

---

### Day 26 Completion Checklist

- [ ] I understand Single Sign-On.
- [ ] I understand SAML 2.0.
- [ ] I can explain the IdP and SP.
- [ ] I understand the SAML authentication flow.
- [ ] I understand SAML assertions.
- [ ] I understand digital signature validation.
- [ ] I can identify common SAML security weaknesses.
- [ ] I have inspected a SAML flow using Burp Suite.
- [ ] I have completed the assignment and knowledge check.

**Learn responsibly. Test ethically.**
