# Day 21 — File Upload Vulnerabilities & Path Traversal

## 1. Introduction

Web applications frequently allow users to upload files such as profile pictures, documents, reports, and attachments. They may also let users download files stored on the server.

If these features are implemented incorrectly, attackers may be able to upload dangerous content, access files they should not see, or expose sensitive information.

In this lesson, you will learn two important web application security concepts:

- **File Upload Vulnerabilities:** Weaknesses in how applications accept, validate, store, and serve uploaded files.
- **Path Traversal:** A vulnerability that may allow users to access files outside an intended directory by manipulating a file path.

**Learning objectives:**
- Understand common file upload security weaknesses.
- Learn why file extensions and client-side validation are insufficient.
- Understand path traversal and directory boundaries.
- Test file handling safely in an authorized lab.
- Document findings with evidence and realistic impact.

## 2. How File Uploads Work

A typical file upload follows this process:

1. The user selects a file.
2. The browser sends an HTTP request to the application.
3. The server receives the file.
4. The application validates the file.
5. The server stores the file or sends it to another storage service.
6. The application may provide a link to view or download it.

Example request:

```http
POST /profile/upload HTTP/1.1
Host: lab.example
Content-Type: multipart/form-data; boundary=----Boundary123
```

The request body contains the form fields and file data.

A secure application must validate the uploaded file on the server, not merely trust information supplied by the browser.

## 3. What Is a File Upload Vulnerability?

A file upload vulnerability occurs when an application handles uploaded files in a way that creates a security risk.

Common weaknesses include:

- Accepting file types that should not be allowed.
- Relying only on the filename extension.
- Trusting the browser-provided MIME type.
- Storing uploaded files in an unsafe location.
- Allowing uploaded content to execute as server-side code.
- Using predictable file names that enable unauthorized access.
- Failing to restrict file size.
- Allowing access to another user's uploaded files.
- Serving uploaded HTML or other active content unsafely.

**Example:**

A profile-picture feature is designed to accept image files, but the server accepts any file renamed with a `.jpg` extension.

Changing a filename does not change the actual file content. If the server trusts the extension alone, its validation may be insufficient.

This weakness requires further testing to determine its real impact.

## 4. Why File Extensions Are Not Enough

A file extension is the suffix in a filename, such as `.jpg`, `.png`, or `.pdf`.

Examples:

```text
profile.jpg
report.pdf
document.txt
```

An extension can provide a useful clue about a file's intended format, but it does not prove what the file contains.

For example, a file may be renamed from:

```text
sample.txt
```

to:

```text
sample.jpg
```

The content remains the same.

A secure upload process should use multiple appropriate checks, such as:

- An allowlist of permitted file types.
- Server-side content validation.
- File signature or format checks where appropriate.
- Safe storage and retrieval rules.
- Limits on file size and processing resources.

No single validation technique guarantees safety for every file format.

## 5. Understanding MIME Types

A MIME type describes the type of content represented by a file or HTTP message.

Examples include:

| File format | Common MIME type |
|---|---|
| JPEG image | `image/jpeg` |
| PNG image | `image/png` |
| PDF document | `application/pdf` |
| Plain text | `text/plain` |
| HTML document | `text/html` |

An upload request may contain a part like this:

```http
Content-Disposition: form-data; name="file"; filename="photo.jpg"
Content-Type: image/jpeg
```

Do not assume the declared MIME type is trustworthy. A client can often change the filename and the MIME type it sends.

The server should independently validate the content according to the formats the application supports.

## 6. Client-Side vs Server-Side Validation

**Client-side validation** happens in the browser, often using JavaScript or HTML form attributes.

Example:

```html
<input type="file" accept="image/png,image/jpeg">
```

This helps guide users toward selecting permitted files, but it is not a security boundary. A user can bypass browser restrictions and send requests through other tools.

**Server-side validation** happens on the server after receiving the request.

A secure application should enforce file type, size, authorization, and storage rules on the server.

**Key principle:** Client-side validation improves usability; server-side validation enforces security.

## 7. Secure File Storage

Where and how files are stored can significantly affect the risk.

Potentially unsafe practices include:

- Saving uploaded files directly into an executable web directory.
- Allowing the server to execute uploaded scripts.
- Using user-supplied filenames as storage paths.
- Exposing private uploads through public URLs.
- Failing to separate one user's files from another user's files.

Safer practices include:

- Storing uploads outside the web root when appropriate.
- Using generated server-side filenames.
- Restricting permissions.
- Preventing uploaded content from being executed.
- Enforcing authorization when users retrieve files.
- Applying file size limits and safe processing controls.

The correct design depends on the application's functionality and deployment architecture.

## 8. What Is Path Traversal?

Path traversal is a vulnerability in which an application allows a user-controlled file path to access a location outside the intended directory.

It is also called **directory traversal**.

Imagine an application stores reports in:

```text
/app/reports/
```

A normal request might ask for:

```text
/app/reports/monthly.pdf
```

A path traversal weakness may allow a manipulated path to reference files outside that directory.

The commonly recognized traversal sequence is:

```text
../
```

It means “move to the parent directory” in many filesystem path contexts.

For example:

```text
reports/../reports/monthly.pdf
```

The path resolves back into the reports directory. Traversal sequences become a security problem when they allow access beyond the intended boundary.

**Important:** Seeing `../` in a request does not, by itself, prove a vulnerability. You must establish what resource the application actually accesses.

## 9. How Path Traversal Happens

Consider this simplified application logic:

```python
filename = request.args.get("file")
path = "/app/reports/" + filename

return open(path, "rb").read()
```

The application joins a user-controlled value to a directory path without demonstrating that the final resolved path stays inside the intended directory.

This design can be unsafe because the user-controlled value may contain path components that change the resulting location.

A safer design should map an authorized document identifier to a server-controlled file location rather than accepting an unrestricted filesystem path.

The example is simplified for learning; real applications may have additional framework and operating-system behavior.

## 10. Path Traversal vs Broken Access Control

These vulnerabilities are related but different.

| Concept | Main issue |
|---|---|
| Path Traversal | A file path escapes its intended directory boundary |
| Broken Access Control | A user can perform an action or access a resource without sufficient permission |
| IDOR | A user manipulates an object reference to access another object without authorization |
| File Upload Vulnerability | File acceptance, storage, processing, or retrieval creates a security weakness |

A path traversal flaw can also cause broken access to files, but not every access-control issue involves filesystem paths.

Similarly, changing a document ID and receiving another user's document may be an IDOR or broader access-control issue rather than path traversal.

## 11. Safe File Upload Testing

When assessing an authorized application, begin with normal behavior.

Check:

- Which file types are intended to be accepted?
- Does the server reject unsupported formats?
- Are file size limits enforced?
- Does the application rely on the filename extension alone?
- Does the server generate its own storage filename?
- Can uploaded content execute?
- Are private files protected by authorization?
- Can users access files belonging to other accounts?

Use harmless test files in your own lab.

Do not upload malware, destructive payloads, or executable content to production systems. Testing executable uploads requires explicit authorization and a controlled environment.

## 12. Safe Path Traversal Testing

Begin by understanding the normal file access workflow.

For example, an application may provide a request like:

```http
GET /download?file=report.pdf HTTP/1.1
```

In an authorized lab, examine whether the application restricts file access to the intended storage location.

A safe testing approach is to:

1. Identify the relevant download or file-viewing endpoint.
2. Understand how the application identifies a file.
3. Use a controlled lab with test files and known directory boundaries.
4. Check whether invalid or out-of-directory references are rejected.
5. Confirm the response using harmless test data.
6. Record the result without exposing unrelated sensitive files.

Avoid testing traversal sequences against real system files or other users' data unless the assessment explicitly authorizes that activity and provides a controlled procedure.

## 13. Common Security Controls

### For file uploads

- Use a strict allowlist of required file formats.
- Validate content on the server.
- Enforce file size and resource limits.
- Generate storage names rather than trusting user-provided paths.
- Store files in a location where they cannot execute as server-side code.
- Apply appropriate access controls to uploads and downloads.
- Safely process or scan files when the threat model requires it.

### For path traversal

- Avoid accepting arbitrary filesystem paths from users.
- Use server-controlled identifiers mapped to approved files.
- Canonicalize and resolve paths before checking their permitted location.
- Verify that the final resolved path remains within the approved directory.
- Enforce authorization independently of path validation.
- Handle invalid paths safely without revealing internal filesystem details.

String filtering alone is not a reliable solution. For example, blocking one traversal string may fail to account for encoding, path normalization, or platform-specific behavior.

## 14. Practical Lab: File Upload Review

**Objective:** Assess a file upload feature in your own application or an authorized training lab.

### Task A: Establish normal behavior

1. Upload a valid, harmless file of an allowed type.
2. Record the response.
3. Determine how the application identifies the uploaded file.
4. Check whether the file can be downloaded or viewed.
5. Confirm whether authentication is required.

### Task B: Check validation

Using harmless test files, check whether:

- An unsupported extension is rejected.
- A file exceeding the documented size limit is rejected.
- The server performs validation independently of the browser.
- Error messages disclose internal paths or implementation details.

Do not assume that a rejection or acceptance alone proves the entire feature is secure.

### Task C: Record observations

| Test | Expected secure behavior | Actual result |
|---|---|---|
| Valid permitted file | Accepted according to requirements | Record result |
| Unsupported format | Rejected safely | Record result |
| Oversized file | Rejected or handled within defined limits | Record result |
| Unauthorized download | Access denied | Record result |
| Error handling | No unnecessary sensitive details | Record result |

## 15. Practical Lab: Path Traversal Review

**Objective:** Understand file path boundaries using a deliberately vulnerable local application or training lab.

1. Identify an endpoint that displays or downloads a file.
2. Determine the intended directory or document access mechanism.
3. Review how the application handles invalid file references.
4. Use the lab's designated test files to check whether access stays within the permitted directory.
5. Record whether unauthorized access was actually demonstrated.
6. Verify that the application denies access to resources outside the approved boundary.

Do not attempt to read sensitive operating-system files or access unrelated users' data. Keep the exercise limited to test resources provided by the lab.

## 16. Reporting a Vulnerability

A useful security report explains what happened, why it matters, and how to address it.

Use this structure:

**Title:** Unrestricted File Upload Validation

**Affected endpoint:** Record the authorized test endpoint.

**Description:** Explain which file validation control was insufficient and how you verified the behavior.

**Evidence:** Include the harmless test file, relevant request and response, and the observed result. Remove credentials, tokens, and sensitive data.

**Impact:** Describe the demonstrated security consequence. Do not claim remote code execution or sensitive data exposure unless the evidence supports it.

**Recommendation:** Recommend server-side format validation, safe storage, execution restrictions, and appropriate access controls.

**Severity:** Assign severity according to validated impact, exploitability, and the applicable assessment methodology.

For path traversal, use the same structure but explain the intended directory boundary and the unauthorized access demonstrated in the lab.

## 17. Common Mistakes to Avoid

- Assuming that a `.jpg` extension guarantees an image.
- Trusting the MIME type supplied by the browser.
- Treating every upload feature as vulnerable.
- Assuming every `../` sequence results in file access.
- Confusing path traversal with IDOR or other access-control issues.
- Reporting code execution without demonstrating it.
- Ignoring authorization checks on file downloads.
- Relying only on client-side validation.
- Filtering traversal strings without checking the final resolved path.
- Testing file access beyond the approved scope.

Professional security testing requires evidence, careful validation, and accurate impact assessment.

## 18. Knowledge Check

**Q1. What is a file upload vulnerability?**

A. A weakness in how an application accepts, stores, processes, or serves files  
B. A type of network cable  
C. A DNS record  
D. A browser theme  

**Answer:** A

**Q2. Why is client-side file validation insufficient?**

A. Browsers cannot upload files  
B. Client-side restrictions can be bypassed  
C. Every file is executable  
D. HTTP does not support uploads  

**Answer:** B

**Q3. What does `../` commonly represent in a filesystem path?**

A. The current user's password  
B. The parent directory  
C. An HTTPS connection  
D. A database table  

**Answer:** B

**Q4. Does changing a file extension change its actual content?**

A. Always  
B. No  
C. Only on Linux  
D. Only for images  

**Answer:** B

**Q5. What is an important path traversal defense?**

A. Trusting every filename  
B. Hiding the upload button  
C. Ensuring the resolved file path remains within the permitted directory  
D. Disabling all HTTP responses  

**Answer:** C

## 19. Mini Assessment Challenge

Imagine a web application accepts profile pictures and allows users to download documents.

During an authorized test, you observe the following:

- The browser restricts uploads to image extensions.
- The server accepts an unsupported file type when tested through a direct request.
- The document download feature uses a user-supplied filename.
- The application returns an error when an invalid document reference is submitted.

Answer these questions:

1. Which observations deserve further investigation?
2. Why is browser-side extension validation insufficient?
3. Does the unsupported upload automatically prove remote code execution?
4. Does a user-supplied filename automatically prove path traversal?
5. What additional evidence would you need to validate the security impact?
6. What controls would you recommend?

**Suggested answers:**

1. The unsupported upload and user-controlled document filename both warrant investigation.
2. A direct request can bypass browser-side restrictions, so the server must enforce validation.
3. No. Execution and impact must be demonstrated separately.
4. No. The application may safely map or validate the filename.
5. Evidence of server-side acceptance, unsafe storage or processing, access beyond the permitted boundary, or another concrete consequence.
6. Apply server-side validation, safe storage, controlled file identifiers, canonical path checks, execution restrictions, and authorization.

## 20. Key Takeaways

- File upload features require careful validation and secure storage.
- Extensions and client-supplied MIME types are not sufficient proof of file type.
- Path traversal occurs when file access escapes its intended directory boundary.
- Secure path handling requires checking the resolved path and enforcing authorization.
- File upload, path traversal, IDOR, and broken access control are related but distinct concepts.
- A suspected weakness must be validated before its impact and severity are reported.
- Testing must remain within owned systems or explicitly authorized labs.

## 21. Further Learning Resources

- [OWASP File Upload Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html)
- [OWASP Path Traversal](https://owasp.org/www-community/attacks/Path_Traversal)
- [PortSwigger Web Security Academy — Directory Traversal](https://portswigger.net/web-security/file-path-traversal)
- [PortSwigger Web Security Academy — File Upload Vulnerabilities](https://portswigger.net/web-security/file-upload)

Use these resources to deepen your understanding and practice in deliberately vulnerable training environments.

## Completion Checklist

- [ ] I understand how file upload features work.
- [ ] I can explain why file extensions and browser validation are insufficient.
- [ ] I understand the difference between file upload vulnerabilities and path traversal.
- [ ] I can describe how to test file handling safely in an authorized lab.
- [ ] I understand the importance of server-side validation, safe storage, and authorization.
- [ ] I can document a security observation without exaggerating its impact.
- [ ] I completed the exercises using only my own system or an authorized training lab.

**Learn responsibly. Test ethically.**
