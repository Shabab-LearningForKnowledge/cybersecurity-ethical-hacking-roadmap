# Day 25: OAuth 2.0 & OpenID Connect (OIDC) Security Fundamentals

## 1. Introduction

Modern applications often allow users to log in using external services such as Google, Microsoft, or other identity providers.

For example:

- Login with Google
- Login with Microsoft
- Login with GitHub

Have you ever wondered how an application allows you to log in using another platform without directly sharing your password with that application?

This is where **OAuth 2.0 and OpenID Connect (OIDC)** come into the picture.

In this lesson, we will understand how these technologies work and how security testers can identify common implementation weaknesses.

## 2. What Is OAuth 2.0?

OAuth 2.0 is an authorization framework that allows an application to access specific resources on behalf of a user without requiring the user's password.

**Simple example:**

Imagine you are using a photo-printing application and want to select photos from your Google Drive.

Instead of sharing your Google password with the photo-printing application, OAuth 2.0 allows you to grant limited access to your Google Drive.

The application receives an access token that can be used to access permitted resources.

**Remember:** OAuth 2.0 is primarily about authorization, not user authentication.

## 3. What Is OpenID Connect (OIDC)?

OpenID Connect is an authentication layer built on top of OAuth 2.0.

It helps applications verify the identity of a user.

For example:

- OAuth 2.0 helps an application obtain permission to access an API.
- OpenID Connect helps an application identify who has logged in.

OIDC introduces an important token called the **ID Token**, which contains information about the authenticated user.

## 4. OAuth 2.0 vs OpenID Connect

| Feature | OAuth 2.0 | OpenID Connect |
|---|---|---|
| Main purpose | Authorization | Authentication |
| Identifies the user | Not inherently | Yes |
| Access Token | Yes | Yes |
| ID Token | Not defined by OAuth itself | Yes |
| Common usage | API access | User login |

**Example:**

When an application requests permission to read your Google Drive files, it uses OAuth authorization.

When an application needs to verify your identity through Google Sign-In, it uses OpenID Connect.

## 5. Important Components of OAuth 2.0

Before understanding the complete authentication flow, let's understand its main components.

1. **Resource Owner:** The user who owns the data.
2. **Client:** The application requesting access.
3. **Authorization Server:** The server that authenticates the user and issues authorization tokens.
4. **Resource Server:** The server hosting the protected resources or APIs.

**Example:**

Suppose you use a third-party application to access your cloud storage.

- Resource Owner: You
- Client: Third-party application
- Authorization Server: Identity provider
- Resource Server: Cloud storage API

## 6. Understanding OAuth 2.0 Authorization Code Flow

The Authorization Code Flow is commonly used by web applications.

Let's understand it step by step.

**Example: Login using an identity provider**

1. User clicks the Login button.
2. The application redirects the user to the authorization server.
3. The user authenticates and provides consent where required.
4. The authorization server redirects the user back with an authorization code.
5. The application exchanges the code for tokens through the token endpoint.
6. The application receives an access token and, in an OIDC flow, an ID token.
7. The application uses the access token to access permitted resources.

**Simplified flow:**

```text
User
  |
  v
Client Application
  |
  | Authorization Request
  v
Authorization Server
  |
  | User Authentication & Consent
  |
  v
Authorization Code
  |
  v
Client Application
  |
  | Token Exchange
  v
Authorization Server
  |
  | Access Token + ID Token (OIDC)
  v
Client Application
  |
  v
Protected API / Resource Server
```

The authorization code is exchanged for tokens. It is not itself an access token.

## 7. What Is an Authorization Code?

An authorization code is a temporary code returned by the authorization server after a successful authorization request.

The client exchanges this code at the token endpoint to obtain tokens.

Important points:

- It is temporary.
- It should be used only once.
- It is bound to the client and redirect URI.
- It should not be exposed unnecessarily.
- PKCE provides additional protection against authorization code interception.

**Security testing perspective:**

A tester should verify whether the authorization code is properly protected and whether the application validates the expected parameters during token exchange.

## 8. Understanding Access Tokens and ID Tokens

These two tokens serve different purposes.

### Access Token

An access token is used to access protected APIs or resources.

Example:

```http
GET /api/profile HTTP/1.1
Host: example.com
Authorization: Bearer ACCESS_TOKEN
```

The resource server validates the token according to its token format and security configuration.

### ID Token

An ID token is used by the client application to obtain information about the authenticated user.

In OIDC, an ID token is generally a JWT.

Example claims:

```json
{
  "iss": "https://identity.example.com",
  "sub": "user123",
  "aud": "client123",
  "exp": 1790000000,
  "iat": 1789996400
}
```

Important: An ID token should not automatically be used as an access token to call APIs.

## 9. What Is PKCE?

PKCE stands for **Proof Key for Code Exchange**.

It adds protection to the OAuth Authorization Code Flow.

It is especially important for public clients such as mobile applications and browser-based applications.

PKCE uses two important values:

- `code_verifier`: A randomly generated secret value kept by the client.
- `code_challenge`: A transformed version of the verifier sent in the authorization request.

**Simplified process:**

```text
Client generates code_verifier
          |
          v
Creates code_challenge
          |
          v
Sends code_challenge in authorization request
          |
          v
Receives authorization code
          |
          v
Sends authorization code + code_verifier
          |
          v
Authorization Server validates PKCE
          |
          v
Token exchange succeeds
```

If an attacker intercepts the authorization code, they should not be able to exchange it without the corresponding verifier.

## 10. Important OAuth 2.0 Parameters

You may observe these parameters during OAuth testing.

| Parameter | Purpose |
|---|---|
| `client_id` | Identifies the application |
| `redirect_uri` | Destination after authorization |
| `response_type` | Specifies the requested response type |
| `scope` | Defines requested permissions |
| `state` | Helps protect against CSRF and correlates the request |
| `code_challenge` | PKCE challenge |
| `code_challenge_method` | Method used to generate the challenge |
| `nonce` | Helps protect OIDC authentication responses against replay |

Example authorization request:

```http
GET /authorize?
response_type=code&
client_id=client123&
redirect_uri=https%3A%2F%2Fapp.example.com%2Fcallback&
scope=openid%20profile&
state=random_state&
nonce=random_nonce&
code_challenge=challenge_value&
code_challenge_method=S256
```

This is a simplified example. Actual requests may contain additional parameters.

## 11. What Are OAuth Scopes?

Scopes define the permissions requested by a client application.

For example:

```text
openid
profile
email
read:files
write:files
```

In OIDC:

- `openid` is required to initiate an OpenID Connect authentication request.
- `profile` requests access to standard profile information.
- `email` requests access to email-related claims.

**Security testing perspective:**

A tester should verify whether the application requests only the permissions it actually needs.

Excessive permissions can increase the impact of token compromise.

## 12. Common OAuth and OIDC Security Vulnerabilities

Now let's understand some common security weaknesses.

### 1. Missing or Weak State Validation

The `state` parameter helps protect the authorization flow against CSRF attacks.

If the application does not properly validate it, an attacker may be able to interfere with the authorization flow.

### 2. Insecure Redirect URI Validation

The authorization server must validate redirect URIs correctly.

Weak validation may allow authorization responses to be redirected to an unauthorized destination.

### 3. Missing PKCE Protection

Without appropriate PKCE protection, authorization codes may be more vulnerable to interception attacks, particularly for public clients.

### 4. Authorization Code Leakage

Authorization codes should not be exposed through logs, browser history, referrer information, or other unintended channels.

### 5. Token Leakage

Access tokens and ID tokens are sensitive credentials.

If exposed, an attacker may be able to misuse them within their permitted scope and validity.

### 6. Improper Scope Validation

An application may request or receive more permissions than required.

### 7. Improper ID Token Validation

OIDC clients must validate important ID token properties, including:

- Signature
- Issuer (`iss`)
- Audience (`aud`)
- Expiration (`exp`)
- Nonce, where applicable

Decoding a JWT without validating it does not establish that it is trustworthy.

## 13. OAuth 2.0 Grant Types

OAuth 2.0 supports different authorization grant types.

| Grant Type | Purpose |
|---|---|
| Authorization Code | Common flow for user authorization |
| Authorization Code + PKCE | Protects authorization code exchange |
| Client Credentials | Machine-to-machine authorization |
| Device Authorization | Devices with limited input capabilities |

**Important:**

The Resource Owner Password Credentials grant (password grant) is not recommended for modern applications and is omitted from OAuth 2.1.

The Implicit grant is also no longer recommended for modern applications.

## 14. OAuth 2.0 vs Traditional Session-Based Authentication

| Feature | Session-Based Authentication | OAuth/OIDC |
|---|---|---|
| Authentication | Usually handled by the application | OIDC provides an identity layer |
| Session management | Server-side session commonly used | Depends on implementation |
| Tokens | Session cookie | Access token and possibly ID token |
| External identity provider | Not required | Commonly supported |
| API integration | Possible | Commonly used |

Neither approach is automatically more secure. Security depends on correct implementation and configuration.

## 15. How to Identify OAuth and OIDC in Burp Suite

Burp Suite can help you observe and analyze OAuth/OIDC communication.

Look for:

- `/authorize`
- `/token`
- `/callback`
- `client_id`
- `redirect_uri`
- `response_type`
- `scope`
- `state`
- `nonce`
- `code_challenge`
- `code_verifier`

**Basic testing approach:**

1. Open an authorized test application.
2. Configure your browser to use Burp Suite.
3. Start the login process.
4. Capture the authorization request.
5. Observe the redirect URI and requested scopes.
6. Review the callback request.
7. Observe how the application handles the authorization code.
8. Review token handling and validation where authorized.

Do not share real access tokens, refresh tokens, authorization codes, or session cookies in screenshots or public reports.

## 16. Practical Lab: Understanding an OAuth Authorization Request

For this exercise, use a dedicated test application or an authorized OAuth/OIDC training environment.

**Objective:** Understand how OAuth parameters are exchanged during login.

### Step 1: Start Burp Suite

- Open Burp Suite.
- Start a temporary project.
- Launch the configured browser.

### Step 2: Access the Authorized Lab

Open the OAuth/OIDC lab provided by your training platform.

### Step 3: Start Login

Click the login option and observe the browser request in Burp Proxy.

### Step 4: Identify Parameters

Locate the following parameters if present:

```text
client_id
redirect_uri
response_type
scope
state
nonce
code_challenge
code_challenge_method
```

### Step 5: Observe the Callback

After successful authorization, inspect the redirect to the application.

Identify whether an authorization code is returned.

### Step 6: Document Your Observations

| Parameter | Observed? | Purpose |
|---|---|---|
| client_id | Yes/No | Identifies the client |
| redirect_uri | Yes/No | Callback destination |
| state | Yes/No | Request correlation and CSRF protection |
| scope | Yes/No | Requested permissions |
| nonce | Yes/No | OIDC request protection |
| code_challenge | Yes/No | PKCE protection |

Do not assume that a missing parameter automatically proves a vulnerability. Assess the applicable flow and security requirements first.

## 17. Security Testing Checklist

Use this checklist during an authorized OAuth/OIDC assessment.

- [ ] Identify the OAuth/OIDC implementation.
- [ ] Identify the authorization and token endpoints.
- [ ] Review redirect URI validation.
- [ ] Check state generation and validation.
- [ ] Review PKCE implementation where applicable.
- [ ] Check requested scopes.
- [ ] Review authorization code handling.
- [ ] Check access token storage and exposure.
- [ ] Review ID token validation.
- [ ] Check issuer and audience validation.
- [ ] Review token expiration and refresh behavior.
- [ ] Verify that access tokens cannot be used beyond their intended permissions.
- [ ] Document findings with supporting evidence.

## 18. How to Report OAuth/OIDC Vulnerabilities

A security finding should clearly explain the weakness, its impact, and the evidence.

**Example report format:**

**Finding Title:** Improper Redirect URI Validation

**Description:**  
The application does not properly validate the redirect URI during the OAuth authorization process.

**Impact:**  
Depending on the implementation, this weakness may allow authorization responses or sensitive authorization codes to be redirected to an unauthorized destination.

**Evidence:**
- Authorization request details.
- Configured redirect URI.
- Validation behavior observed in the authorized lab.

**Recommendation:**
- Use exact registered redirect URI matching.
- Avoid wildcard-based redirect URI validation.
- Validate redirect URIs on the authorization server.
- Apply PKCE where appropriate.

**Severity:** Determine based on actual exploitability, affected data, and business impact. Do not assign severity based only on the vulnerability name.

## 19. Assignment

Complete the following activities using an authorized OAuth/OIDC lab.

1. Explain OAuth 2.0 in your own words.
2. Explain the difference between OAuth 2.0 and OpenID Connect.
3. Identify the four main OAuth components.
4. Draw the Authorization Code Flow.
5. Explain the purpose of PKCE.
6. Identify the difference between an access token and an ID token.
7. Identify at least five important OAuth parameters.
8. Explain why redirect URI validation is important.
9. Explain why state validation is required.
10. Prepare a basic OAuth/OIDC security assessment checklist.

## 20. Knowledge Check

**Q1. What is the primary purpose of OAuth 2.0?**

A. Encryption  
B. Authorization  
C. File compression  
D. Network scanning  

**Answer:** B. Authorization

**Q2. What does OpenID Connect provide?**

A. Authentication  
B. Port scanning  
C. Database backup  
D. File transfer  

**Answer:** A. Authentication

**Q3. What is the purpose of PKCE?**

A. Encrypting the database  
B. Protecting the authorization code exchange  
C. Scanning open ports  
D. Generating passwords  

**Answer:** B. Protecting the authorization code exchange

**Q4. Which parameter helps protect the OAuth authorization flow against CSRF?**

A. client_id  
B. scope  
C. state  
D. response_type  

**Answer:** C. state

**Q5. Which token is used to access protected APIs?**

A. Access Token  
B. ID Token  
C. Authorization Code  
D. State Parameter  

**Answer:** A. Access Token

**Q6. Which OIDC claim identifies the token's intended audience?**

A. exp  
B. aud  
C. iat  
D. sub  

**Answer:** B. aud

**Q7. What does the `openid` scope indicate?**

A. Request for an OIDC authentication flow  
B. Request for network access  
C. Request for database access  
D. Request for file upload  

**Answer:** A. Request for an OIDC authentication flow

**Q8. What is a security risk of weak redirect URI validation?**

A. Faster login  
B. Unauthorized redirection of authorization responses  
C. Better encryption  
D. Improved session management  

**Answer:** B. Unauthorized redirection of authorization responses

## 21. Key Takeaways

After completing Day 25, you should understand:

- OAuth 2.0 and its purpose.
- OpenID Connect and its relationship with OAuth.
- The four main OAuth components.
- Authorization Code Flow.
- Access Tokens and ID Tokens.
- PKCE and its importance.
- OAuth scopes and important parameters.
- Common OAuth/OIDC security weaknesses.
- How to observe OAuth communication using Burp Suite.
- How to document findings during a security assessment.

### Further Learning Resources

1. OAuth 2.0 Framework (RFC 6749): https://www.rfc-editor.org/rfc/rfc6749
2. OpenID Connect Core: https://openid.net/specs/openid-connect-core-1_0.html
3. OAuth 2.0 Security Best Current Practice: https://www.rfc-editor.org/rfc/rfc9700
4. OWASP OAuth 2.0 Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/OAuth2_Cheat_Sheet.html
5. PortSwigger Web Security Academy – OAuth Authentication: https://portswigger.net/web-security/oauth

---

### Day 25 Completion Checklist

- [ ] I understand OAuth 2.0.
- [ ] I understand OpenID Connect.
- [ ] I can explain Authorization Code Flow.
- [ ] I understand access tokens and ID tokens.
- [ ] I understand PKCE.
- [ ] I can identify common OAuth/OIDC security weaknesses.
- [ ] I have completed the practical lab.
- [ ] I have completed the assignment and knowledge check.

**Learn responsibly. Test ethically.**
