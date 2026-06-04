# Complete OWASP Top 10 (2025) Notes

*(Good enough for interviews, bug bounty, TryHackMe, hackathons, and revision before exams.)*

According to the official 2025 release, the Top 10 are: Broken Access Control, Security Misconfiguration, Software Supply Chain Failures, Cryptographic Failures, Injection, Insecure Design, Authentication Failures, Software or Data Integrity Failures, Security Logging & Alerting Failures, and Mishandling of Exceptional Conditions. ([OWASP Foundation][1])

---

# What is OWASP?

**OWASP (Open Worldwide Application Security Project)** is a non-profit organization that publishes the most important web application security risks.

The Top 10 is NOT a list of every vulnerability.
It is an awareness document that helps developers and security engineers focus on the biggest risks.

---

# A01: Broken Access Control

## Definition

The application fails to properly restrict what authenticated or unauthenticated users can do.

Simply:

> "Can I access something I shouldn't?"

This remains the #1 web security risk. SSRF has also been merged into this category in 2025. ([OWASP Foundation][2])

---

## Common Vulnerabilities

* IDOR (Insecure Direct Object Reference)
* Privilege Escalation
* Directory Traversal
* Force Browsing
* Missing Authorization Checks
* SSRF (Server Side Request Forgery)

---

## Example

User profile URL:

```
/profile?id=1001
```

Attacker changes it to:

```
/profile?id=1002
```

If another user's profile loads:

✅ Broken Access Control.

---

## Real-world Example

A normal employee accesses:

```
/admin/dashboard
```

because frontend hid the button but backend never checked permissions.

---

## Prevention

* Always verify authorization on server-side.
* Apply least privilege.
* Deny by default.
* Use RBAC (Role-Based Access Control).
* Never trust hidden fields or frontend checks.

---

# A02: Security Misconfiguration

## Definition

System is deployed with insecure settings.

Think:

> "The software itself is fine, but the configuration is terrible."

Moved from #5 to #2 in 2025 due to increasing reliance on configurations. ([OWASP Foundation][2])

---

## Common Examples

* Default passwords
* Debug mode enabled
* Directory listing enabled
* Open cloud storage buckets
* Exposed admin panels
* Unnecessary services running
* Incorrect CORS settings

---

## Example

```
DEBUG=True
```

Error page leaks:

* database path
* framework version
* API keys
* stack traces

---

## Real Attack

Public AWS S3 bucket:

```
https://bucket.amazonaws.com/customer_data.csv
```

Anyone downloads millions of records.

---

## Prevention

* Disable debug mode.
* Remove default credentials.
* Harden servers.
* Regular configuration audits.
* Infrastructure as Code validation.
* Automated security scanning.

---

# A03: Software Supply Chain Failures

## Definition

Trusting insecure dependencies, packages, build systems, or CI/CD pipelines.

Expanded from "Vulnerable and Outdated Components" into a broader supply-chain category. ([OWASP Foundation][2])

---

## Examples

* Malicious npm package
* Vulnerable Python library
* Compromised GitHub Action
* Backdoored Docker image
* Poisoned CI/CD pipeline

---

## Famous Examples

### SolarWinds

Attackers compromised build infrastructure.

Thousands of organizations installed malicious updates.

---

### Log4Shell

```
log4j
```

One logging library exposed millions of systems.

---

## Prevention

* Software Bill of Materials (SBOM)
* Dependency scanning
* Signed packages
* Verify update integrity
* Minimal dependencies
* Secure CI/CD pipelines

---

# A04: Cryptographic Failures

## Definition

Sensitive information is not properly protected.

Previously called "Sensitive Data Exposure."

---

## Common Examples

* HTTP instead of HTTPS
* Weak hashing (MD5, SHA1)
* Plaintext passwords
* Hardcoded API keys
* Weak encryption algorithms

---

## Bad

```
password = "admin123"
```

stored directly in database.

---

## Good

```
bcrypt(password)
```

with unique salt.

---

## Common Algorithms

### Good

* AES-256
* bcrypt
* Argon2
* scrypt
* SHA-256 (for integrity)

### Bad

* MD5
* SHA1
* DES
* RC4

---

## Prevention

* Use TLS everywhere.
* Encrypt sensitive data.
* Store passwords using bcrypt/Argon2.
* Rotate keys regularly.
* Never invent your own crypto.

---

# A05: Injection

## Definition

User input becomes executable commands.

---

## Types

### SQL Injection

```
SELECT * FROM users
WHERE username='$user'
AND password='$pass'
```

Attacker enters:

```
' OR 1=1 --
```

Query becomes true.

---

### Command Injection

```
ping $ip
```

Attacker:

```
127.0.0.1 && whoami
```

---

### Cross Site Scripting (XSS)

Stored:

```
<script>alert(document.cookie)</script>
```

Victims execute attacker's JavaScript.

---

## Prevention

* Prepared statements
* Parameterized queries
* Input validation
* Output encoding
* ORM frameworks

---

# A06: Insecure Design

## Definition

Security was never considered during application design.

OWASP differentiates insecure design from insecure implementation. A perfectly coded application can still be insecure if the architecture itself is flawed. ([OWASP Foundation][3])

---

## Example

Password reset:

```
Forgot Password
↓
Enter Email
↓
Reset Password
```

No email verification.

Anyone can reset anyone else's password.

---

## Other Examples

* No rate limiting
* No anti-automation controls
* Business logic flaws
* Unlimited money transfer API
* No fraud detection

---

## Prevention

* Threat modeling
* Secure SDLC
* Abuse case analysis
* Security requirements before coding
* Defense in depth

---

# A07: Authentication Failures

## Definition

Application fails to properly verify identity.

---

## Examples

* Weak passwords
* Credential stuffing
* Session fixation
* Predictable session IDs
* Missing MFA

---

## Credential Stuffing

Attacker uses leaked passwords:

```
john@gmail.com : password123
```

against thousands of websites.

Works because users reuse passwords.

---

## Session Fixation

Attacker sends victim:

```
session=ABC123
```

Victim logs in.

Attacker reuses same session.

---

## Prevention

* MFA
* Strong password policy
* Secure session handling
* Password hashing
* Login throttling

---

# A08: Software or Data Integrity Failures

## Definition

Application trusts software or data without verifying integrity.

---

## Examples

* Unsafe deserialization
* Unsigned updates
* Trusting client-side data
* Auto-executing downloaded code

---

## Example

Application downloads:

```
update.zip
```

and executes it without verifying signature.

Attacker swaps file.

Remote code execution achieved.

---

## Unsafe Deserialization

User sends serialized object:

```
{"role":"admin"}
```

Server blindly trusts it.

User becomes admin.

---

## Prevention

* Verify digital signatures.
* Validate integrity hashes.
* Avoid native deserialization.
* Never trust client data.

---

# A09: Security Logging & Alerting Failures

## Definition

Attacks happen, but nobody notices.

OWASP emphasizes that logging without alerting provides little value. ([OWASP Foundation][2])

---

## Missing Logs

* Failed logins
* Admin actions
* Privilege escalation
* SQL Injection attempts
* Password resets

---

## Example

Attacker tries:

```
10000 login attempts
```

No logs.
No alerts.
No lockout.

Security team never knows.

---

## Prevention

* Centralized logging
* SIEM integration
* Alert suspicious behavior
* Protect logs from modification
* Regular monitoring

---

# A10: Mishandling of Exceptional Conditions

## Definition

Application behaves insecurely during errors or unexpected situations.

New category introduced in 2025. ([OWASP Foundation][2])

---

## Examples

* Fail open
* Improper exception handling
* Race conditions
* Integer overflow
* Infinite loops
* Resource exhaustion

---

## Bad Example

Payment gateway unavailable.

Instead of:

```
Payment Failed
```

Application returns:

```
Order Confirmed
```

because developer forgot error handling.

---

## Fail Open Example

```
if(auth_server_down)
    allow_access();
```

Should instead:

```
deny_access();
```

---

## Prevention

* Fail securely.
* Handle exceptions properly.
* Rate limiting.
* Resource limits.
* Thorough edge-case testing.

---

# TryHackMe Mapping

## Room 1: IAAA Failures

| OWASP | Topic                   |
| ----- | ----------------------- |
| A01   | Broken Access Control   |
| A07   | Authentication Failures |
| A09   | Logging & Alerting      |

---

## Room 2: Application Design Flaws

| OWASP | Topic                              |
| ----- | ---------------------------------- |
| A02   | Security Misconfiguration          |
| A03   | Software Supply Chain Failures     |
| A04   | Cryptographic Failures             |
| A06   | Insecure Design                    |
| A10   | Mishandling Exceptional Conditions |

---

## Room 3: Insecure Data Handling

Typically focuses heavily on:

* A04 Cryptographic Failures
* A05 Injection
* A08 Software/Data Integrity Failures

along with practical handling of sensitive information and trust boundaries.

---

# Very Short Interview Revision

| ID  | Remember As                         |
| --- | ----------------------------------- |
| A01 | Can I access something I shouldn't? |
| A02 | Bad configuration                   |
| A03 | Bad dependency / supply chain       |
| A04 | Weak or missing encryption          |
| A05 | User input becomes code             |
| A06 | Security never designed in          |
| A07 | Broken login/authentication         |
| A08 | Trusting unverified software/data   |
| A09 | No logs, no alerts                  |
| A10 | Errors handled insecurely           |

---

# Bug Bounty Checklist

When testing a web app, mentally go through:

```
□ Can I access another user's data? (A01)
□ Is anything misconfigured? (A02)
□ Are libraries outdated? (A03)
□ Is sensitive data encrypted? (A04)
□ Can I inject SQL/XSS/commands? (A05)
□ Is there flawed business logic? (A06)
□ Can authentication be bypassed? (A07)
□ Can I tamper with trusted data? (A08)
□ Are attacks logged or rate-limited? (A09)
□ What happens if something breaks? (A10)
```

For someone doing cybersecurity projects, SOC work, or preparing for pentesting, memorizing those ten questions alone will cover a surprisingly large percentage of real-world web application findings.

[1]: https://owasp.org/Top10/2025/en/?utm_source=chatgpt.com "OWASP Top 10:2025"
[2]: https://owasp.org/Top10/2025/0x00_2025-Introduction/?utm_source=chatgpt.com "Introduction - OWASP Top 10:2025"
[3]: https://owasp.org/Top10/2025/A06_2025-Insecure_Design/?utm_source=chatgpt.com "A06 Insecure Design - OWASP Top 10:2025"
