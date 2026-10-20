# Day 18: Cross-Site Scripting (XSS) Fundamentals & Safe Testing

## 1. Introduction

Modern web applications display information from many sources, including user profiles, search fields, comments, product reviews, and API responses.

Applications must handle this information carefully before displaying it in a browser.

If an application places untrusted content into a webpage unsafely, a browser may interpret that content as executable JavaScript rather than ordinary text. This security weakness is known as **Cross-Site Scripting (XSS)**.

Depending on the context and application controls, XSS can allow an attacker to perform actions in a victim's browser under the security context of the affected website.

In this lesson, we will understand how XSS occurs, learn its main types, practise identifying unsafe rendering behaviour in an authorized lab, and explore prevention techniques.

**Learning objectives:**
- Understand how browsers interpret HTML and JavaScript.
- Learn what XSS is and why it occurs.
- Understand reflected, stored, and DOM-based XSS.
- Recognize common XSS testing locations.
- Learn safe, controlled XSS testing methods.
- Understand how developers prevent XSS.

## 2. What Is Cross-Site Scripting?

Cross-Site Scripting is a web application vulnerability in which untrusted content is handled in a way that allows unintended script execution in a user's browser.

Although its name contains the word “cross-site,” XSS often involves injecting content into a trusted website that the browser then interprets as active content.

For example, imagine a website that displays user comments. If the website inserts a comment into the page without appropriate handling, specially crafted content may be interpreted as HTML or JavaScript instead of plain text.

Possible consequences include:
- Performing actions through the victim's authenticated browser session.
- Reading information available to scripts in the affected page.
- Modifying the content displayed to the user.
- Redirecting users to misleading pages.
- Accessing certain browser-accessible application data.

The actual impact depends on the vulnerability, browser protections, application permissions, and available security controls.

**Important:** XSS does not automatically expose every cookie or compromise the entire server.

## 3. How Does a Browser Process Web Content?

A browser receives HTML, processes its structure, applies styles, and executes permitted JavaScript.

A simplified flow looks like this:

```text
User Input
    |
    v
Web Application
    |
    v
HTML Response or DOM Update
    |
    v
Browser Parses Content
    |
    v
Content Displayed or Script Executed
```

Consider a comment feature.

A user submits a comment, and the application saves it. When another user opens the page, the application retrieves the comment and displays it.

If the comment is safely rendered as text, it should remain text. If it is inserted into an executable HTML context without appropriate protection, the browser may interpret it differently.

The security issue depends on **where and how the untrusted content is inserted**.

## 4. What Are HTML, JavaScript, and the DOM?

Before studying XSS, understand three basic concepts.

### HTML

HTML defines the structure of a webpage.

```html
<p>Welcome to the website.</p>
```

This creates a paragraph.

### JavaScript

JavaScript can make webpages interactive.

```javascript
console.log("Page loaded");
```

This writes a message to the browser's developer console.

### DOM

DOM stands for **Document Object Model**. It represents a webpage as objects that JavaScript can inspect and modify.

For example:

```javascript
document.querySelector("p").textContent =
  "Updated message";
```

This changes the text of the first paragraph.

The DOM is important for XSS because unsafe JavaScript operations can cause untrusted input to be interpreted as HTML or executable content.

## 5. How Can XSS Occur?

XSS commonly occurs when an application takes untrusted input and places it into a browser-rendered context without appropriate handling.

Potential sources include:
- Search terms
- Usernames and profile details
- Comments and reviews
- Message content
- URL parameters or URL fragments
- API responses
- Data retrieved from a database

For example, an application might receive a user's display name and insert it into a webpage.

If the application uses a safe text-rendering method, the name remains text. If it uses an unsafe HTML insertion method without suitable safeguards, the browser may interpret special markup.

**Key principle:** Data that comes from a user, API, database, or URL should not automatically be trusted simply because it has reached the application.

## 6. Types of XSS

The three commonly discussed types of XSS are:

1. Reflected XSS
2. Stored XSS
3. DOM-based XSS

They describe different ways untrusted data can reach an unsafe browser context.

Understanding the distinction helps a security tester identify the relevant data flow and the appropriate remediation.

## 7. Reflected XSS

Reflected XSS occurs when user-controlled input is included in a server response and handled unsafely by the browser.

The input is commonly sent in a request and reflected in the response rather than being persistently stored by the application.

Example flow:

```text
User Sends Input
       |
       v
Application Processes Request
       |
       v
Input Reflected Unsafely
       |
       v
Browser Interprets Response
```

Possible locations include:
- Search result pages
- Error messages
- URL parameters
- Form validation messages

**Example scenario:**

A search page displays the search term in a heading. If the application inserts that term into HTML without appropriate context-specific handling, it may create an XSS risk.

**What should a tester check?**
- Is user input reflected in the response?
- Is the input displayed as text or interpreted as markup?
- Does the application apply appropriate output encoding?
- Does the result depend on a particular HTML or JavaScript context?

Reflection alone does not prove XSS. The tester must establish unsafe interpretation or script execution in the authorized test environment.

## 8. Stored XSS

Stored XSS occurs when an application stores untrusted content and later displays it unsafely to users.

Example flow:

```text
User Submits Content
        |
        v
Application Stores Content
        |
        v
Another User Opens the Page
        |
        v
Content Rendered Unsafely
        |
        v
Potential Script Execution
```

Potential locations include:
- Comments
- Product reviews
- User biographies
- Support tickets
- Discussion forums
- Shared messages

Stored XSS can be particularly serious because a single stored entry may affect multiple users who view the affected page.

The impact depends on who can submit the content, who can view it, and which browser context processes it.

**What should a tester check?**
- Is submitted content saved?
- How is it rendered when viewed later?
- Is the same content shown to other roles?
- Does the application safely encode or sanitize it for the intended context?

Only use test content in an authorized lab or approved test account.

## 9. DOM-Based XSS

DOM-based XSS occurs when client-side JavaScript processes untrusted data and handles it unsafely, potentially causing the browser to interpret that data as executable content.

In some cases, the unsafe processing occurs entirely in the browser and does not require the server to reflect the input in its HTML response.

Common sources of client-side data include:
- `location.search`
- `location.hash`
- `document.referrer`
- Certain browser storage values
- Data received from APIs

Potentially unsafe sinks include:
- `innerHTML`
- `outerHTML`
- `document.write()`
- Certain dynamic code-execution functions

Example of safe text insertion:

```javascript
const message = "Example text";
document.querySelector("#output").textContent =
  message;
```

This inserts the value as text rather than interpreting it as HTML.

Compare that with:

```javascript
const message = getUntrustedInput();
document.querySelector("#output").innerHTML =
  message;
```

The second example uses an HTML-parsing sink. If untrusted input reaches it without appropriate safeguards, an XSS vulnerability may arise.

**Important:** `innerHTML` is not automatically vulnerable in every use. The risk depends on the data supplied and how it is handled.

## 10. Understanding XSS Contexts

Output handling must match the context where the data is inserted.

Common contexts include:

- HTML text
- HTML attribute values
- JavaScript strings
- CSS values
- URLs

A technique suitable for one context may not protect another.

For example, encoding characters for HTML text does not automatically make a value safe inside a JavaScript string or a URL.

**Security principle:** Use context-appropriate output encoding, safe APIs, and framework protections. Avoid placing untrusted data directly into executable contexts.

When HTML formatting is genuinely required, use a well-maintained HTML sanitization library configured for the intended purpose rather than attempting to write a sanitizer from scratch.

## 11. What Is Output Encoding?

Output encoding converts special characters into representations that are treated as data in a particular output context.

For example, in HTML text, the less-than character can be represented as `&lt;`.

Instead of being interpreted as the beginning of an HTML tag, it is displayed as a character.

Example:

```html
<p>&lt;example&gt;</p>
```

The browser displays:

`<example>`

It does not interpret the text between the encoded delimiters as an HTML element.

Output encoding is context-dependent. HTML encoding alone is not a universal defence for every context in which data may appear.

## 12. What Is HTML Sanitization?

HTML sanitization removes or restricts unsafe markup while preserving content that is permitted by a defined policy.

It is useful when an application intentionally allows users to submit formatted content, such as rich-text comments.

For example, an application may allow:
- Paragraphs
- Bold text
- Italic text
- Approved links

But it should reject unsafe elements, attributes, or URL schemes that could create security risks.

Sanitization must be performed using an appropriate, maintained library and a carefully configured allowlist.

**Important distinction:**
- **Output encoding** treats content as data in a specific context.
- **Sanitization** permits selected markup while removing disallowed content.

Choose the approach according to the application's requirements.

## 13. How Do Cookie Flags Relate to XSS?

Cookies used for authentication often contain session identifiers, which should be protected.

Two important attributes are:

**HttpOnly:** Prevents JavaScript from directly reading the cookie through browser APIs such as `document.cookie`.

**Secure:** Restricts cookie transmission to HTTPS connections.

These attributes provide useful protection, but they do not eliminate XSS.

For example, even if JavaScript cannot directly read an HttpOnly session cookie, injected JavaScript may still be able to perform certain actions through the affected page using the victim's authenticated browser session.

Other controls, including output handling, Content Security Policy, appropriate authorization, and secure session design, remain important.

## 14. What Is Content Security Policy (CSP)?

Content Security Policy is a browser security mechanism that lets an application define restrictions on the sources and types of content that may be loaded or executed.

A CSP is commonly delivered through an HTTP response header.

Illustrative example:

```http
Content-Security-Policy: default-src 'self'; object-src 'none'; base-uri 'self'
```

This policy sets a default source restriction, disallows object content, and restricts the document base URL.

A production policy must be designed and tested for the application's actual requirements. The example is not a complete universal policy.

CSP can reduce the impact of certain XSS vulnerabilities, but it should be treated as an additional layer of protection rather than a replacement for safe output handling.

## 15. XSS vs. SQL Injection

Both are injection-related vulnerabilities, but they affect different processing contexts.

| SQL Injection | Cross-Site Scripting |
|---|---|
| Targets SQL query interpretation | Targets browser interpretation of web content |
| Commonly involves backend database processing | Commonly involves HTML, JavaScript, or DOM processing |
| Can affect database confidentiality and integrity | Can affect users and actions within the browser's security context |
| Primary defence: parameterized queries | Primary defence: context-appropriate output handling and safe DOM APIs |

A tester must identify the component interpreting the input before deciding which vulnerability category may apply.

## 16. Practical Exercise: Safe XSS Testing in a Local Lab

**Environment:** Use an intentionally vulnerable training application such as OWASP Juice Shop running in your own environment, or an approved PortSwigger Web Security Academy lab.

Resources:
- OWASP Juice Shop: https://owasp.org/www-project-juice-shop/
- PortSwigger XSS learning materials: https://portswigger.net/web-security/cross-site-scripting

Do not test public websites without explicit authorization.

### Part A: Understand normal rendering

1. Open the authorized lab application.
2. Locate a search, comment, or other input feature provided for testing.
3. Submit ordinary text.
4. Observe how the application displays it.
5. Open browser Developer Tools and inspect the relevant request or DOM element.
6. Record whether the content appears as text or as HTML markup.

### Part B: Review a safe DOM example

In a local HTML file, create a simple page with a text input and an output area. Compare how the output behaves when your own test string is inserted using `textContent` versus when it is processed using `innerHTML`.

Use harmless text containing characters such as angle brackets to observe the difference between text insertion and HTML parsing. Do not use this exercise against another website.

### Part C: Record your findings

Document:
- Input source
- Output location
- Whether the content is reflected or stored
- The browser context
- Observed behaviour
- Potential security concern
- Recommended safe handling method

Do not claim that XSS is confirmed unless the behaviour and security impact are demonstrated in the authorized lab.

## 17. Common Beginner Mistakes

**Mistake 1: Assuming every reflected input is XSS**

Reflection does not prove that the browser interprets the input as executable content.

**Mistake 2: Treating every angle bracket as a vulnerability**

Characters such as `<` and `>` are common in ordinary data. Their significance depends on the rendering context.

**Mistake 3: Using HTML encoding in every context**

Different contexts require different handling. HTML text encoding is not automatically sufficient for JavaScript, CSS, or URL contexts.

**Mistake 4: Assuming HttpOnly completely prevents XSS impact**

HttpOnly limits direct cookie reading but does not prevent every action that injected JavaScript may perform in a user's browser.

**Mistake 5: Relying only on CSP**

CSP is a useful additional security layer, not a replacement for safe rendering.

**Mistake 6: Reporting without validation**

A suspicious string, reflected parameter, or scanner alert should be investigated before it is reported as a confirmed vulnerability.

## 18. How to Document an XSS Finding

Use a structured report format.

**Title:** Potential Cross-Site Scripting in Search Feature

**Affected component:** Record the actual page, parameter, or DOM operation.

**XSS type:** Reflected, stored, or DOM-based, if confirmed.

**Description:** Explain how untrusted content reaches an unsafe browser context.

**Preconditions:** Describe the user role or interaction needed to reproduce the issue.

**Steps to reproduce:** Include concise, authorized, repeatable steps.

**Expected behaviour:** User-controlled content should be handled safely for its output context.

**Observed behaviour:** Record the actual behaviour and relevant evidence.

**Impact:** Explain the realistic effects supported by the evidence.

**Recommendation:** Use safe DOM APIs, context-appropriate output encoding, appropriate HTML sanitization where needed, and a suitable CSP as an additional control.

**Evidence:** Include sanitized screenshots or relevant request and response excerpts. Remove credentials, session cookies, and personal data.

**Severity:** Base the assessment on the confirmed impact, affected users, execution context, and other relevant factors.

This is a reporting template, not a claim that a real vulnerability has been found.

## 19. Assignment

Prepare a short report titled **Cross-Site Scripting Fundamentals and Prevention**.

Include:

1. A definition of XSS.
2. An explanation of how a browser processes HTML and JavaScript.
3. The differences between reflected, stored, and DOM-based XSS.
4. Three application features where output handling could be assessed.
5. An explanation of output encoding and HTML sanitization.
6. The role of HttpOnly and Secure cookie attributes.
7. The purpose of Content Security Policy.
8. Your observations from a local or explicitly authorized lab.
9. Two possible false positives or alternative explanations.
10. Four practical recommendations for preventing XSS.

If you do not confirm a vulnerability, clearly document the result as an observation rather than inventing a finding.

## 20. Knowledge Check

**Q1. What is Cross-Site Scripting?**

A. A weakness involving unsafe browser interpretation of untrusted content  
B. A method for increasing database storage  
C. A network routing protocol  
D. A type of password hashing  

**Q2. Which type of XSS involves content stored by an application and rendered unsafely later?**

A. Reflected XSS  
B. Stored XSS  
C. DNS spoofing  
D. SQL injection  

**Q3. Which JavaScript property is generally preferred for inserting plain text into an element?**

A. `innerHTML`  
B. `textContent`  
C. `document.write()`  
D. `eval()`  

**Q4. What does HttpOnly do?**

A. Blocks all JavaScript execution  
B. Prevents direct JavaScript access to the cookie  
C. Encrypts every page  
D. Automatically fixes XSS  

**Q5. What is the purpose of output encoding?**

A. To make untrusted data safe for the intended output context  
B. To disable authentication  
C. To replace the database  
D. To increase network bandwidth  

**Q6. What is the role of CSP?**

A. To provide additional browser-enforced content restrictions  
B. To guarantee that XSS is impossible  
C. To replace all input validation  
D. To encrypt database tables  

**Q7. Does reflected input automatically confirm XSS?**

A. Yes  
B. No  

### Answers

1. A — Unsafe browser interpretation of untrusted content.
2. B — Stored XSS.
3. B — `textContent`.
4. B — It prevents direct JavaScript access to the cookie.
5. A — It helps ensure content is interpreted safely in its intended context.
6. A — CSP provides additional browser-enforced restrictions.
7. B — The tester must establish unsafe interpretation and relevant impact.

## 21. Key Takeaways

After completing Day 18, you should be able to explain:

- XSS occurs when untrusted content is handled unsafely in a browser context.
- Reflected, stored, and DOM-based XSS describe different data flows.
- The DOM allows JavaScript to inspect and modify a webpage.
- `textContent` is generally preferable to `innerHTML` for plain text.
- Output encoding must match the intended context.
- HTML sanitization is useful when an application must allow selected markup.
- HttpOnly, Secure, and CSP provide useful protections but do not replace safe application design.
- A reflected value or scanner alert does not automatically prove XSS.
- Testing should be controlled, authorized, reproducible, and supported by evidence.

### Completion Checklist

- [ ] I can explain XSS in simple terms.
- [ ] I understand how HTML, JavaScript, and the DOM relate to XSS.
- [ ] I can distinguish reflected, stored, and DOM-based XSS.
- [ ] I understand output encoding and HTML sanitization.
- [ ] I know why `textContent` is generally safer for plain text.
- [ ] I understand the security benefits and limitations of cookie flags and CSP.
- [ ] I can record observations from an authorized lab.
- [ ] I can recommend suitable XSS prevention techniques.
- [ ] I understand the importance of validating findings before reporting them.

**Learn responsibly. Test ethically.**
