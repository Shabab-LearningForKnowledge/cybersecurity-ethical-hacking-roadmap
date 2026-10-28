# Day 24 — JWT Security & Common Authentication Vulnerabilities

## 1. Introduction

Modern web applications and APIs often use tokens to authenticate users and manage access to protected resources.

One widely used token format is **JSON Web Token (JWT)**.

JWTs can carry information about a user or session and may be used by an application to make authentication and authorization decisions. If tokens are implemented or validated incorrectly, attackers may be able to bypass security controls, impersonate users, or access protected resources.

In this lesson, you will learn how JWTs work, how to inspect them, which common implementation mistakes can introduce security risks, and how to test JWT-based authentication safely in an authorized lab.

**Learning objectives:**
- Understand JWT structure and purpose.
- Learn the difference between encoding, signing, and encryption.
- Inspect JWTs using safe testing tools.
- Recognize common JWT implementation weaknesses.
- Understand token expiration, signature validation, and key management.
- Document authentication findings with clear evidence.

## 2. What Is a JWT?

JWT stands for **JSON Web Token**. It is a compact format for representing claims that can be transmitted between parties.

A JWT is often used in authentication systems and APIs.

For example, after a user successfully signs in, an application may issue a token. The client then sends that token when requesting protected resources.

```text
User
  |
  | Login credentials
  v
Authentication Service
  |
  | Issues token
  v
Client Application
  |
  | API request + token
  v
API Server
  |
  | Validates token and permissions
  v
Protected Resource
```

A JWT does not automatically make an application secure. The application must validate the token correctly and enforce authorization for every protected operation.

## 3. Understanding JWT Structure

A typical signed JWT contains three parts separated by periods:

```text
HEADER.PAYLOAD.SIGNATURE
```

Example of a **fictional, nonfunctional token structure**:

```text
eyJhbGciOiJIUzI1NiJ9
.
eyJzdWIiOiIxMjMiLCJyb2xlIjoidXNlciJ9
.
EXAMPLE_SIGNATURE
```

The three parts are:

1. **Header:** Describes the token type and signing algorithm.
2. **Payload:** Contains claims, such as a subject identifier and expiration time.
3. **Signature:** Allows the receiving application to verify the token's integrity and authenticity, when correctly implemented.

The example is illustrative and is not a valid signed token.

Important: JWTs can be signed, encrypted, or used in other defined forms. The common three-part structure above describes a signed JWT, often called a JWS.

## 4. JWT Header

The header contains metadata about the token.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Here:

- `alg` identifies the signing algorithm.
- `typ` indicates the token type.

Common algorithms include:

- `HS256`: HMAC using SHA-256 and a shared secret.
- `RS256`: RSA signature using SHA-256.
- `ES256`: ECDSA signature using SHA-256.

The server must enforce an appropriate algorithm policy. It should not blindly trust an algorithm choice supplied by an untrusted token.

Different algorithms have different key requirements. For example, HMAC uses a shared secret, while RSA and ECDSA signatures use asymmetric key pairs.

## 5. JWT Payload and Claims

The payload contains claims—statements about a subject or token.

Example:

```json
{
  "sub": "user-123",
  "role": "user",
  "iss": "https://auth.example.test",
  "aud": "orders-api",
  "iat": 1790000000,
  "exp": 1790003600
}
```

Common claims include:

| Claim | Purpose |
|---|---|
| `sub` | Subject, often a user identifier |
| `iss` | Issuer of the token |
| `aud` | Intended audience |
| `iat` | Time the token was issued |
| `exp` | Token expiration time |
| `nbf` | Time before which the token must not be accepted |
| `jti` | Token identifier |

Applications may also define custom claims, such as a role or tenant identifier.

**Security principle:** A claim in a token is not automatically trustworthy merely because it appears in the payload. The application must validate the token and use claims only according to its security design.

## 6. Encoding Is Not Encryption

One of the most common JWT misunderstandings is believing that the payload is secret.

JWT header and payload sections are commonly Base64URL-encoded. Encoding changes how data is represented; it does not encrypt the data.

For example, a payload may contain:

```json
{
  "sub": "user-123",
  "role": "user"
}
```

Someone who possesses a typical signed JWT can often decode and read its payload without knowing the signing key.

Therefore:

- Do not place passwords in JWT claims.
- Do not include API secrets or private keys.
- Avoid exposing sensitive personal information unnecessarily.
- Use encryption where confidentiality is required, with a properly designed and validated encryption mechanism.

A signed token provides integrity and authenticity when correctly verified; signing alone does not provide confidentiality.

## 7. How JWT Authentication Works

A common JWT-based authentication flow works as follows:

1. The user submits login credentials over a protected connection.
2. The authentication service verifies the credentials.
3. The service issues a token according to its authentication policy.
4. The client stores the token using an appropriate storage strategy.
5. The client sends the token to the API.
6. The API validates the token.
7. The API checks authorization before returning protected information or performing an operation.

Example request:

```http
GET /api/profile HTTP/1.1
Host: api.example.test
Authorization: Bearer <ACCESS_TOKEN>
```

The placeholder is not a real credential.

A secure API should validate the token's signature, permitted algorithm, issuer, audience, time-based claims, and other relevant requirements before relying on its claims.

## 8. Inspecting a JWT Safely

A JWT can be inspected using an offline decoder or an approved tool such as Burp Suite.

A safe inspection process is:

1. Capture a token from your own authorized test account.
2. Keep the token private.
3. Inspect its header and payload.
4. Identify the claims and their purposes.
5. Check whether sensitive information is unnecessarily included.
6. Review expiration and issuer information.
7. Do not treat successful decoding as proof that the token is valid.

You can use the official JWT debugger at [jwt.io](https://jwt.io/) to understand token structure. Do not paste real production tokens, session credentials, or sensitive customer data into public online tools.

For sensitive tokens, prefer an offline tool in your own test environment.

## 9. JWT Signature Validation

A signature helps the receiving application detect changes to a signed token.

If a token's payload is changed without generating a valid signature, a correctly implemented server should reject it.

For example, consider a token whose payload contains:

```json
{
  "sub": "user-123",
  "role": "user"
}
```

If someone changes the role to `administrator`, the original signature will no longer validate for the modified token.

However, this protection works only when the server correctly verifies the signature and refuses tokens that fail validation.

A secure implementation should not trust modified claims or accept an invalid signature simply because the token is well-formed.

## 10. Algorithm Confusion and Weak Algorithm Policies

JWT implementations can become vulnerable when the server does not strictly enforce its expected signing algorithm.

Potential weaknesses include:

- Accepting algorithms that should not be permitted.
- Failing to verify the signature.
- Confusing symmetric and asymmetric key handling.
- Trusting token-supplied algorithm metadata without applying server-side policy.

A secure implementation should:

- Allow only explicitly configured algorithms.
- Use the correct key type for each algorithm.
- Reject unsigned tokens when signatures are required.
- Validate signatures before trusting claims.
- Use a well-maintained JWT library and secure configuration.

Do not assume that a token is vulnerable simply because its header contains a particular algorithm name. The server's actual validation behavior determines the risk.

## 11. Weak Signing Secrets

When a JWT uses a symmetric algorithm such as HS256, the parties validating and signing the token share a secret.

If that secret is weak, predictable, or exposed, an attacker may be able to forge tokens.

Risky practices include:

- Using common words as signing secrets.
- Hardcoding secrets in public repositories.
- Reusing secrets across unrelated environments.
- Sharing secrets with unauthorized parties.
- Failing to rotate compromised secrets.

Safer practices include generating high-entropy secrets, storing them in an appropriate secrets-management system, limiting access, and rotating them when necessary.

For asymmetric algorithms, protect the private signing key and distribute verification keys securely.

**Testing boundary:** Secret-strength testing should be explicitly authorized and use test tokens and test keys. Do not attempt to recover secrets from real production tokens without approval.

## 12. Token Expiration and Lifetime

Tokens may contain an `exp` claim that specifies when they expire.

Example:

```json
{
  "sub": "user-123",
  "exp": 1790003600
}
```

A properly implemented server should reject an expired token, subject to a narrowly defined clock-skew policy.

Security questions include:

- Is the expiration claim present when required?
- Does the server enforce expiration?
- Is the token lifetime appropriate for the use case?
- Are clock-skew allowances reasonable?
- Can a revoked or compromised token still be used when it should no longer be accepted?

Long-lived tokens increase the potential impact of theft. Short-lived access tokens can reduce that exposure, but the overall design must also consider refresh tokens, revocation, and session management.

## 13. Issuer and Audience Validation

Two important JWT claims are `iss` and `aud`.

**Issuer (`iss`)** identifies the system that issued the token.

**Audience (`aud`)** identifies the intended recipient or recipients.

Example:

```json
{
  "iss": "https://auth.example.test",
  "aud": "orders-api",
  "sub": "user-123"
}
```

An API intended to accept tokens for `orders-api` should not automatically accept a token intended for a different service.

A secure implementation should validate the issuer and audience according to its configuration, as well as the signature and time-based claims.

This helps reduce the risk of tokens issued for one purpose being incorrectly accepted in another context.

## 14. JWT Storage and Token Theft

A correctly signed token can still be stolen.

Potential sources of token exposure include:

- Cross-Site Scripting that accesses tokens available to JavaScript.
- Accidental logging of authorization headers.
- Insecure storage in client applications.
- Exposure through browser extensions or compromised devices.
- Incorrect handling of tokens in URLs.
- Leaked tokens in screenshots, reports, or source code.

Security practices include:

- Avoid placing bearer tokens in URLs.
- Use HTTPS.
- Redact tokens from logs and reports.
- Apply appropriate browser storage and cookie protections.
- Limit token lifetime and permissions.
- Protect refresh tokens more strictly than short-lived access tokens.

Storing a token in an `HttpOnly` cookie can prevent JavaScript from directly reading that cookie, but it does not eliminate all XSS or CSRF risks. Storage choices should match the application's architecture and threat model.

## 15. JWT vs Session-Based Authentication

JWT authentication and session-based authentication are different approaches.

| Feature | JWT-based approach | Session-based approach |
|---|---|---|
| Common credential | Token | Session identifier |
| Server state | Can be reduced, but may still be needed | Typically stores session state server-side |
| Revocation | Requires deliberate design | Often straightforward through server-side session invalidation |
| Client handling | Depends on token storage strategy | Commonly uses a session cookie |
| Main security requirement | Correct token validation and authorization | Secure session handling and authorization |

Neither approach is automatically more secure in every situation.

A JWT is not always stateless in practice, and a session-based design can be secure when implemented correctly.

Choose the approach based on the application's requirements, threat model, and operational needs.

## 16. Practical Lab: JWT Inspection and Validation

**Objective:** Learn how a JWT is structured and how an API validates it.

Use a local training application or a deliberately vulnerable lab that provides test accounts and tokens.

### Task A: Inspect the token

1. Sign in with a test account.
2. Capture a JWT used by the lab.
3. Inspect the header and payload offline.
4. Record the algorithm, subject, issuer, audience, and expiration claims when present.
5. Do not share the token outside the lab.

### Task B: Test normal behavior

Send a request to a protected endpoint using the valid test token.

Record:

- The HTTP method and endpoint.
- The response status code.
- Whether the expected test resource is returned.

### Task C: Test invalid-token handling

Use the lab's designated test procedure to check how the API responds to:

- A missing token.
- A malformed token.
- An expired test token, if provided.
- A token with an invalid signature, if supported by the lab.

A secure implementation should reject tokens that fail its required validation checks.

### Task D: Record the results

| Test | Expected secure behavior | Actual result |
|---|---|---|
| Valid test token | Access follows the user's permissions | Record result |
| Missing token | Protected endpoint denies access | Record result |
| Malformed token | Token is rejected safely | Record result |
| Invalid signature | Token is rejected | Record result |
| Expired token | Token is rejected when expiration is enforced | Record result |

Do not test forged administrator tokens against a real system. Use only the lab's designated accounts, tokens, and procedures.

## 17. Reporting JWT Security Findings

A good report should describe the observed validation failure rather than merely listing a suspicious token claim.

Example template:

**Title:** API Accepts an Invalid JWT Signature

**Affected endpoint:** Record the authorized test endpoint.

**Description:** Explain which required token-validation control failed.

**Preconditions:** Record the account, role, and authentication conditions needed to reproduce the behavior.

**Evidence:** Include a sanitized request and response from the lab. Never include live production tokens or signing secrets.

**Impact:** Describe the unauthorized access or operation actually demonstrated. Do not claim account takeover solely because a token was decoded or modified.

**Recommendation:** Enforce an explicit algorithm policy, validate signatures and required claims, protect signing keys, and apply appropriate authorization checks.

**Severity:** Determine severity from the demonstrated impact, required privileges, exploitability, and the applicable assessment methodology.

## 18. Common JWT Security Mistakes

- Assuming Base64URL encoding provides encryption.
- Trusting claims before validating the signature.
- Accepting unsigned tokens when signed tokens are required.
- Using weak or exposed signing secrets.
- Failing to enforce token expiration.
- Ignoring issuer and audience validation.
- Storing sensitive information in readable token claims.
- Assuming JWTs cannot be revoked or invalidated.
- Exposing tokens in URLs, logs, screenshots, or reports.
- Treating a decoded or modified token as proof of a vulnerability without testing server behavior.

## 19. Mini Assessment Challenge

Imagine an API uses JWTs to authenticate users.

During an authorized lab assessment, you observe:

- The token payload contains a `role` claim.
- The payload can be decoded without a secret.
- The token contains an expiration claim.
- A malformed token receives a `401 Unauthorized` response.
- You have not yet tested how the API handles a modified signature.

Answer these questions:

1. Does the ability to decode the payload prove a vulnerability?
2. Does the presence of an `exp` claim prove the server enforces expiration?
3. What does a `401 Unauthorized` response to a malformed token suggest?
4. How would you safely verify signature validation in the lab?
5. Why should authorization still be checked after validating the token?
6. Which sensitive information should never be included in a JWT payload unnecessarily?

**Suggested answers:**

1. No. Decoding is expected for a typical signed JWT and is not proof of a vulnerability.
2. No. The server must actually enforce the expiration claim.
3. It is consistent with the server rejecting the malformed token, but it does not prove that every validation check is correct.
4. Follow the lab's designated procedure using a test token with an invalid signature and verify that access is denied.
5. A valid token establishes a trusted identity or claims under the token policy; it does not automatically grant permission to every resource or action.
6. Passwords, private keys, signing secrets, access credentials, and unnecessary sensitive personal information.

## 20. Key Takeaways

- JWTs are a common format for carrying claims between systems.
- A typical signed JWT contains a header, payload, and signature.
- Base64URL encoding is not encryption.
- Correct signature verification, algorithm restrictions, and claim validation are essential.
- Token expiration, issuer, and audience must be enforced when required by the design.
- A valid token does not replace object-level or function-level authorization.
- Token theft and weak signing-secret management can undermine an otherwise sound authentication design.
- Findings must be based on demonstrated server behavior and realistic impact.

## 21. Further Learning Resources

- [RFC 7519 — JSON Web Token (JWT)](https://www.rfc-editor.org/rfc/rfc7519)
- [OWASP JSON Web Token Cheat Sheet for Java](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html)
- [PortSwigger Web Security Academy — Authentication](https://portswigger.net/web-security/authentication)
- [JWT Introduction and Debugger](https://jwt.io/introduction)

Use these resources to deepen your understanding and practise only with your own test tokens or authorized training labs.

## Completion Checklist

- [ ] I understand the three parts of a typical signed JWT.
- [ ] I can explain the difference between encoding, signing, and encryption.
- [ ] I understand the purpose of signature verification and algorithm restrictions.
- [ ] I can explain token expiration, issuer, and audience validation.
- [ ] I understand why token storage and signing-secret protection matter.
- [ ] I can inspect a test token without exposing real credentials.
- [ ] I can test invalid-token handling in an authorized lab.
- [ ] I can document a JWT security finding using evidence.
- [ ] I completed the exercises using only my own system or an authorized lab.

**Learn responsibly. Test ethically.**
