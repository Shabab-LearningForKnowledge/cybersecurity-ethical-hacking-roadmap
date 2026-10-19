# Day 17: SQL Injection (SQLi) Fundamentals & Safe Testing

## 1. Introduction

Web applications frequently communicate with databases to store and retrieve information such as user accounts, product details, orders, and customer records.

For example, when a user logs in to a website, the application may query a database to find the account and verify the submitted credentials.

But what happens if an application handles user input incorrectly while constructing a database query?

In some situations, specially crafted input may change the meaning of the intended query. This security weakness is known as **SQL Injection (SQLi)**.

In this lesson, we will learn how SQL queries work, why SQL injection occurs, how to recognize potential indicators, and how to test safely in an authorized lab.

**Learning objectives:**
- Understand databases, SQL, and application queries.
- Learn what SQL injection is and why it occurs.
- Understand the difference between safe and unsafe query construction.
- Recognize common SQL injection categories.
- Learn a structured approach to authorized SQLi testing.
- Understand prevention techniques and reporting practices.

## 2. What Is a Database?

A database is an organized collection of information that applications can store, retrieve, and manage.

Consider an online shopping application. Its database might contain tables such as:

- `users` — account information
- `products` — product details
- `orders` — customer orders
- `payments` — payment transaction records

A simplified `users` table might look like this:

| user_id | username | role |
|---|---|---|
| 101 | alice | customer |
| 102 | bob | customer |
| 103 | admin | administrator |

This is illustrative data, not information from a real application.

When a user logs in or views an order, the application may query the relevant database table to retrieve information.

**Security consideration:** Database access should be restricted, and the application should only retrieve or modify information that the requesting user is authorized to access.

## 3. What Is SQL?

SQL stands for **Structured Query Language**. It is used with relational databases to retrieve, insert, update, and delete data, as well as to manage database structures and permissions.

Examples of SQL operations include:

- `SELECT` — retrieve data
- `INSERT` — add data
- `UPDATE` — modify data
- `DELETE` — remove data

Example:

```sql
SELECT username, role
FROM users
WHERE user_id = 101;
```

This query requests the username and role of the user whose ID is `101`.

Another example:

```sql
SELECT product_name, price
FROM products;
```

This requests product names and prices from the `products` table.

Understanding basic SQL helps a security tester recognize how unsafe input handling may affect database queries.

## 4. How Does a Web Application Communicate With a Database?

A simplified application flow looks like this:

```text
User's Browser
      |
      | HTTP Request
      v
Web Application
      |
      | Application Query
      v
Database
      |
      | Query Result
      v
Web Application
      |
      | HTTP Response
      v
User's Browser
```

For example, a user searches for a product.

1. The browser sends the search term to the application.
2. The application processes the request.
3. The application queries the database.
4. The database returns matching records.
5. The application sends the results to the browser.

If the application constructs database queries unsafely, untrusted input may affect the query's intended meaning.

The important point is that **the browser does not normally execute the database query itself; the backend application interacts with the database**.

## 5. What Is SQL Injection?

SQL injection is a vulnerability that occurs when untrusted input is incorporated into an SQL statement in a way that allows the input to alter the statement's intended structure or meaning.

Potential consequences depend on the database, application permissions, query design, and other controls. They may include:

- Unauthorized access to information
- Authentication logic being bypassed
- Unauthorized modification or deletion of records
- Exposure of sensitive database information
- Further compromise when additional weaknesses are present

SQL injection does not automatically provide complete database or server access. The actual impact must be established through appropriate testing.

SQL injection is included in the **Injection** category of the OWASP Top 10:2025.

## 6. How Can SQL Injection Occur?

Consider an application that builds a query by combining a fixed SQL statement with user-controlled text.

An unsafe conceptual example:

```python
username = get_user_input()

query = (
    "SELECT * FROM users WHERE username = '"
    + username
    + "'"
)
```

The application directly combines the username with SQL syntax.

If the input contains characters that change how the database interprets the statement, the resulting query may not behave as the developer intended.

The underlying problem is not simply that the user entered unusual characters. The problem is that **untrusted input was allowed to influence the structure of the SQL statement**.

The preferred approach is to separate the query structure from the supplied data.

## 7. Safe vs. Unsafe SQL Query Construction

### Unsafe approach

```python
query = (
    "SELECT * FROM users WHERE username = '"
    + username
    + "'"
)
```

Here, the application builds the query by concatenating strings.

### Safer approach

```python
cursor.execute(
    "SELECT * FROM users WHERE username = ?",
    (username,)
)
```

This example uses parameter binding with a database interface that supports `?` placeholders.

Placeholder syntax varies by database driver. Always use the parameterization method supported by the chosen library.

With parameterized queries, the SQL statement and the user-supplied value are handled separately. This prevents the value from being interpreted as SQL syntax in that parameter position.

**Key lesson:** Prefer parameterized queries over manually escaping input or concatenating user-supplied values into SQL statements.

## 8. Common Types of SQL Injection

SQL injection can appear in different forms. Understanding these categories helps testers recognize the evidence they may encounter.

### 1. In-band SQL Injection

The application provides useful results or database errors through the same communication channel used to submit the input.

Two commonly discussed forms are:

- **Error-based SQLi:** Database errors reveal information that may help identify unsafe query handling.
- **UNION-based SQLi:** A query is manipulated to combine results from different database queries, where the database and query context permit it.

### 2. Blind or Inferential SQL Injection

The application does not directly display the query result, so a tester assesses differences in observable behaviour.

Common forms include:

- **Boolean-based:** Responses differ depending on whether a condition is true or false.
- **Time-based:** Response timing may vary when a database evaluates a condition or performs a delay.

Timing differences alone do not prove SQL injection. Network latency, application load, and other factors can produce delays.

### 3. Out-of-Band SQL Injection

In certain environments, the database may trigger a separate network interaction that can provide evidence of injection.

This depends on database capabilities, configuration, network access, and security controls.

Out-of-band testing should only be performed when explicitly permitted and when the test infrastructure is controlled.

## 9. Where Should a Tester Look for Potential SQL Injection?

A tester can review application features that send user-controlled data to backend processing.

Examples include:

- Login forms
- Search fields
- Product filters
- URL query parameters
- Record identifiers
- Request body parameters
- Sorting and filtering options
- API endpoints that query database records

Example URL:

```http
GET /products?category=books
```

Here, `category` is a query parameter.

A tester may ask how the application processes this value and whether it is handled safely.

However, the presence of a parameter does not mean the endpoint is vulnerable. It only identifies a place where input handling can be assessed.

## 10. What Are SQL Error Messages?

SQL errors can occur when a database receives a statement it cannot process.

During testing, a response that contains a database-specific error may indicate that user input is reaching database processing in an unexpected way.

Potentially sensitive information in errors can include:

- Database product or version details
- Table or column names
- Query fragments
- Internal application paths
- Driver or framework information

A database error is **not sufficient by itself** to confirm SQL injection. It may arise from ordinary validation failures, application bugs, or other conditions.

Applications should return appropriate error messages to users while retaining necessary diagnostic details in protected logs.

## 11. Authentication-Related SQL Injection

Some applications use database queries to locate accounts or verify login information.

If such queries are constructed unsafely, SQL injection may affect authentication logic.

For example, an application might use a query that looks up an account by username and then validates the password.

The secure design should:
- Parameterize database queries.
- Verify credentials using an appropriate password-hashing mechanism.
- Apply consistent authentication controls.
- Avoid revealing unnecessary database details in login errors.
- Enforce authorization after authentication.

**Important:** SQL injection and weak password handling are different issues, although both may affect login security.

During an authorized assessment, use designated test accounts and safe test cases. Do not attempt to access real users' accounts.

## 12. SQL Injection in APIs

APIs often accept structured input in JSON, URL parameters, or request bodies.

Example request:

```http
GET /api/products?category=books
```

Example JSON request:

```json
{
  "productId": 101
}
```

A backend may use these values to retrieve database records.

During an authorized API assessment, examine whether:
- Inputs are handled using parameterized database operations.
- Unexpected values are rejected or safely processed.
- Error responses reveal sensitive internal details.
- The server enforces access permissions independently.
- Data types and business rules are validated.

JSON does not prevent SQL injection. The risk depends on how the backend processes the supplied values.

## 13. SQL Injection vs. Cross-Site Scripting (XSS)

Both are injection-related vulnerabilities, but they affect different interpreters and contexts.

| SQL Injection | Cross-Site Scripting (XSS) |
|---|---|
| Affects SQL query interpretation | Affects browser-side interpretation of untrusted content |
| Often involves backend database queries | Often involves content rendered in a user's browser |
| Can expose or alter database information | Can allow unwanted script execution in a browser context |
| Common prevention includes parameterized queries | Common prevention includes context-appropriate output encoding and safe rendering practices |

Neither vulnerability should be confirmed from input appearance alone. The tester must understand the relevant execution context and verify the security impact.

## 14. How to Test for SQL Injection Safely

Use a structured testing process instead of trying random payloads.

### Step 1: Confirm the scope

Verify that the application and endpoints are explicitly authorized for testing.

### Step 2: Understand normal behaviour

Submit ordinary, valid inputs and record the expected responses.

### Step 3: Identify input points

Observe query parameters, form fields, request bodies, and API inputs.

### Step 4: Use controlled test cases

Within the approved lab, use benign test inputs designed to help identify unexpected database-related behaviour.

### Step 5: Compare responses

Look for reproducible changes in status codes, response content, error messages, or timing.

### Step 6: Validate the evidence

Distinguish database-related behaviour from generic application errors or network delays.

### Step 7: Document the result

Record the affected endpoint, input location, expected behaviour, observed behaviour, evidence, and possible impact.

### Step 8: Recommend remediation

Explain how the issue can be corrected, such as by using parameterized queries and appropriate error handling.

Avoid destructive queries, data extraction, modification of real records, or testing outside the approved scope.

## 15. Practical Exercise: SQL Injection Concepts in a Local Lab

For this exercise, use an intentionally vulnerable application in a local or otherwise explicitly authorized training environment.

Suitable learning resources include:

- OWASP Juice Shop: https://owasp.org/www-project-juice-shop/
- PortSwigger Web Security Academy: https://portswigger.net/web-security/sql-injection

### Exercise A: Understand the normal request

1. Start your authorized lab.
2. Open the application in a browser.
3. Identify a search or login feature intended for testing.
4. Open Developer Tools and select the Network panel.
5. Submit an ordinary input.
6. Record the request method, endpoint, response status, and visible result.

### Exercise B: Observe input handling

1. Use only the test cases provided by your chosen training lab.
2. Submit a controlled test input within the lab's instructions.
3. Compare the response with the normal request.
4. Record any repeatable differences.
5. Do not assume that a different response proves SQL injection.
6. Do not retrieve, modify, or delete unrelated data.

### Exercise C: Review the secure implementation

Compare unsafe string concatenation with parameterized queries using the examples in Section 7.

Write a short explanation of why parameter binding helps prevent user input from changing the SQL statement's structure.

**Expected outcome:** You should understand the testing process and the prevention principle without needing to exploit a real application.

## 16. SQL Injection Prevention Techniques

Developers can reduce SQL injection risk through multiple practices.

### Use parameterized queries

Separate SQL syntax from user-supplied values. This is one of the primary controls against SQL injection.

### Avoid unsafe dynamic query construction

Do not concatenate untrusted input into SQL statements. For dynamic identifiers such as column names or sort directions, use strict allowlists because such elements generally cannot be handled as ordinary bound parameters.

### Apply least privilege

The application's database account should have only the permissions required for its responsibilities.

For example, an application account that only needs to retrieve product information should not automatically receive permission to delete database tables.

### Validate input

Check that supplied values meet expected data types, lengths, formats, and business rules.

Validation is useful, but it does not replace parameterized queries.

### Handle errors safely

Avoid exposing detailed database errors to ordinary users. Keep necessary diagnostic information in access-controlled logs.

### Use secure database libraries and frameworks

Prefer supported, well-maintained database interfaces and use their safe query mechanisms correctly.

### Test and review code

Use code review, security testing, and dependency maintenance to identify unsafe query patterns before deployment.

## 17. Understanding False Positives

A false positive occurs when a test or security tool reports a possible vulnerability that is not confirmed by the available evidence.

For SQL injection, possible misleading indicators include:
- A generic HTTP 500 response
- A validation error
- A response that changes because of normal application logic
- Slow responses caused by network latency
- An error unrelated to database query execution

A security tester should:
1. Repeat the observation safely.
2. Compare it with normal application behaviour.
3. Check whether the result is consistent.
4. Examine the available evidence.
5. Report the issue as unconfirmed if the evidence is insufficient.

**Professional rule:** Never label an issue as confirmed SQL injection merely because a scanner or test input produces an error.

## 18. How to Write a SQL Injection Finding

A useful report communicates what was tested and why the observed behaviour matters.

Use the following structure:

**Title:** Potential SQL Injection in Product Search

**Affected endpoint:** Record the actual endpoint from the authorized lab.

**Description:** Explain how user-controlled input appears to affect database query processing.

**Preconditions:** Specify whether authentication or a particular role is required.

**Steps to reproduce:** Provide the minimum safe, authorized steps needed to reproduce the observed behaviour.

**Expected result:** The application should handle input safely without allowing it to alter SQL query structure.

**Observed result:** Document the actual, repeatable response. Do not invent evidence.

**Impact:** Explain the realistic consequences supported by the evidence.

**Recommendation:** Use parameterized queries, safe handling of dynamic SQL elements, least-privilege database accounts, and appropriate error handling.

**Evidence:** Include sanitized requests and responses. Remove credentials, cookies, tokens, and sensitive data.

**Severity:** Assign severity only after considering the confirmed impact, exploitability, required privileges, and application context.

This is a reporting template, not a claim that a real vulnerability has been identified.

## 19. Assignment

Create a short report titled **SQL Injection Fundamentals and Secure Query Handling**.

Include:

1. A definition of SQL injection.
2. A diagram showing the browser, application, and database.
3. An explanation of unsafe query concatenation.
4. An explanation of parameterized queries.
5. The difference between in-band, blind, and out-of-band SQL injection.
6. Three application features where input handling could be assessed.
7. A summary of observations from an authorized training lab.
8. Two possible false positives or alternative explanations.
9. Four recommended prevention measures.
10. A brief conclusion explaining why SQL injection testing must be evidence-based.

If your lab does not demonstrate a vulnerability, report that accurately. Do not fabricate a finding or assume the application is vulnerable.

## 20. Knowledge Check

**Q1. What does SQL stand for?**

A. Secure Query Link  
B. Structured Query Language  
C. System Quality Layer  
D. Server Query Logic  

**Q2. What is the core cause of SQL injection?**

A. Every database uses passwords  
B. Untrusted input can alter the intended structure of a SQL statement  
C. A website uses HTTPS  
D. A browser stores cookies  

**Q3. Which is a primary defence against SQL injection?**

A. Parameterized queries  
B. Hiding the search box  
C. Changing the website's theme  
D. Removing all database tables  

**Q4. Does every database error prove SQL injection?**

A. Yes  
B. No  

**Q5. Which category of the OWASP Top 10:2025 includes SQL injection?**

A. Injection  
B. Security Misconfiguration  
C. Authentication Failures  
D. Security Logging and Alerting Failures  

**Q6. Why should an application database account use least privilege?**

A. To increase the number of HTTP requests  
B. To limit potential damage if the account is misused  
C. To remove the need for input validation  
D. To make all queries public  

**Q7. Does JSON input automatically prevent SQL injection?**

A. Yes  
B. No  

### Answers

1. B — Structured Query Language.
2. B — Untrusted input can alter the intended SQL statement.
3. A — Parameterized queries.
4. B — An error needs investigation and validation.
5. A — Injection.
6. B — Least privilege limits the potential impact of misuse.
7. B — The backend's handling of the input determines the risk.

## 21. Key Takeaways

After completing Day 17, you should be able to explain:

- SQL is used to query and manage relational database information.
- Web applications commonly process user input before querying databases.
- SQL injection occurs when untrusted input can alter the intended SQL statement.
- Injection can take different forms, including in-band, blind, and out-of-band SQL injection.
- A database error or unusual response does not automatically confirm SQL injection.
- Parameterized queries are a primary prevention measure.
- Input validation, least privilege, safe error handling, and code review provide additional protection.
- Security testing must be authorized, controlled, reproducible, and evidence-based.
- Vulnerability reports should describe confirmed observations and realistic impact.

### Completion Checklist

- [ ] I can explain SQL and basic database queries.
- [ ] I understand how a web application communicates with a database.
- [ ] I can explain SQL injection in simple language.
- [ ] I understand unsafe query concatenation and parameterized queries.
- [ ] I can distinguish the main SQL injection categories.
- [ ] I understand why errors and response differences require validation.
- [ ] I can identify appropriate prevention techniques.
- [ ] I can document test observations without inventing findings.
- [ ] I understand the importance of testing only authorized environments.

**Learn responsibly. Test ethically.**
