# networkwalks-B083-week4-penetration-testing-report
Absolutely — paste this directly into your GitHub README.md or report file:

# Mediroza General Hospital — Penetration Testing Report

**Project:** Networkwalks Week 4 — Batch B083  
**Assessment Type:** Black-Box Web Application Penetration Test  
**Target:** `https://medirozahospital.com`  
**Duration:** 5 Days  
**Tester:** Ssemakula Arthur  
**Status:** Internship / Authorized Lab Assessment

> **Confidentiality Notice:** This report is intended for the authorized internship/lab environment. Do not publish patient records, passwords, session tokens, private employee information, or other confidential evidence to a public GitHub repository.

---

## 1. Executive Summary

A black-box penetration test was conducted against the authorized Mediroza General Hospital web application as part of the Networkwalks Week 4 internship project.

The assessment focused on external reconnaissance, web-service enumeration, authentication analysis, user-input handling, and SQL injection assessment.

The following observations were verified during testing:

- `medirozahospital.com` resolved to `199.188.201.16`.
- TCP port `80` (HTTP) was reachable.
- TCP port `443` (HTTPS) was reachable.
- The web server identified itself as **LiteSpeed**.
- The application exposed a patient login endpoint at `/patient/login.php`.
- The login endpoint accepted a POST request containing username and password parameters.
- A `PHPSESSID` session cookie was observed during authentication testing.
- The captured login response returned `HTTP/2 200 OK`.
- No obvious CSRF token was observed in the captured login POST request.
- The absence of an observed CSRF token was recorded as an observation and was **not automatically classified as a vulnerability**.
- SQL injection testing was initiated as part of the authorized assessment.

Further findings should only be marked as confirmed when reproducible evidence demonstrates the vulnerability.

---

# 2. Scope and Methodology

## 2.1 Scope

| Item | Details |
|---|---|
| Client | Mediroza General Hospital |
| Target | `https://medirozahospital.com` |
| Assessment Type | Black-Box Penetration Test |
| Environment | Controlled Educational Internship |
| Authorization | Authorized internship/lab assessment |
| Testing Restrictions | Target domain only; no social engineering; no denial-of-service testing |

## 2.2 Methodology

The assessment followed these stages:

1. Reconnaissance
2. Web-service enumeration
3. Authentication analysis
4. User-input analysis
5. SQL injection assessment
6. Restricted-resource assessment
7. PDF encryption analysis
8. Investigation of sensitive information exposure
9. Evidence collection
10. Risk assessment
11. Remediation recommendations
12. Final reporting

---

# 3. Tools Used

The following tools were used or planned during the assessment:

- Nmap
- Dig
- Curl
- Burp Suite
- Firefox
- SQLMap
- ExifTool
- PDFInfo
- QPDF
- PDFCrack
- Linux command-line utilities

Only tools actually used during the assessment should be listed as completed tools.

---

# 4. Reconnaissance

## 4.1 DNS Enumeration

The following commands were used:


dig medirozahospital.com
dig www.medirozahospital.com
dig MX medirozahospital.com
dig NS medirozahospital.com

The target resolved to:

199.188.201.16
Observation

The DNS resolution confirmed the target's public IP address.

5. Web Service Enumeration
5.1 HTTPS Connectivity

The following command was used:

curl -I https://medirozahospital.com

Observed response:

HTTP/2 200
content-type: text/html
last-modified: Fri, 04 Sep 2026 02:03:12 GMT
accept-ranges: bytes
content-length: 5445
server: LiteSpeed
x-turbo-charged-by: LiteSpeed
Observation

The HTTPS service successfully returned an HTTP 200 OK response.

The web server identified itself as:

LiteSpeed
5.2 Port Enumeration

The following Nmap command was used:

nmap -Pn -p 80,443 199.188.201.16

Observed result:

PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https
Observation

Both HTTP and HTTPS services were reachable.

The -Pn option was used because ICMP ping showed packet loss. The successful HTTP and HTTPS connections demonstrated that the web service itself remained reachable.

6. Authentication Analysis
6.1 Login Endpoint

During web application testing, the following login endpoint was identified:

/patient/login.php

The captured authentication request used:

POST /patient/login.php HTTP/2

The request contained parameters similar to:

username=[REDACTED]
password=[REDACTED]

A PHP session cookie was also observed:

PHPSESSID=[REDACTED]

Sensitive credentials and session values have been redacted.

6.2 Authentication Response

The captured response returned:

HTTP/2 200 OK
X-Powered-By: PHP/8.2.33
Server: LiteSpeed

The response returned the Patient Portal login page.

A 200 OK response alone does not demonstrate successful authentication. Authentication status must be determined by the application's resulting state, redirect behavior, session changes, and access to the authorized restricted area.

7. CSRF Token Observation

The captured login POST request was inspected for common CSRF parameters such as:

csrf
csrf_token
token
nonce

No obvious CSRF token was observed in the captured POST body.

Assessment

This is recorded as an observation, not a confirmed vulnerability.

The absence of a visible CSRF token does not by itself prove that the application is vulnerable to CSRF.

8. Burp Suite Analysis

Burp Suite was used to intercept and inspect HTTP requests generated by the application.

The following requests were observed:

GET /index.html
GET /patient/login.php
POST /patient/login.php

The login POST request was sent to Burp Repeater for controlled analysis.

Authentication flow
Firefox
    ↓
Burp Proxy
    ↓
POST /patient/login.php
    ↓
Mediroza Web Server
    ↓
HTTP Response

Repeater was used to resend and compare authorized test requests.

9. SQL Injection Assessment
9.1 Objective

SQL injection testing was conducted to determine whether user-controlled parameters in the authentication endpoint could influence backend database queries.

The primary endpoint assessed was:

POST /patient/login.php

The main parameters identified were:

username
password
9.2 Manual Input Testing

Controlled test inputs were considered for the username parameter.

Example test:

test'

Boolean comparison tests included:

test' AND '1'='1

and:

test' AND '1'='2

The responses were intended to be compared using:

HTTP status
Response length
Response content
Redirect behavior
Error messages
Session behavior

A difference between two responses does not automatically prove SQL injection.

9.3 SQLMap Testing

A captured POST request can be saved as:

login-request.txt

A conservative SQLMap assessment can then be performed with:

sqlmap -r login-request.txt -p username --batch --level=1 --risk=1

The password parameter can be tested separately where authorized:

sqlmap -r login-request.txt -p password --batch --level=1 --risk=1
Current Status

SQL Injection: Not confirmed in this report.

SQL injection should only be reported as confirmed if reproducible evidence demonstrates that the tested parameter is actually injectable.

10. Milestone 1 — Restricted Resources

The project requires identification of three confidential patient PDF laboratory reports and evidence of authorized access.

Current Evidence Status
Requirement	Status
Identify restricted area	To be documented
Demonstrate authorized access	To be documented
PDF 1	To be documented
PDF 2	To be documented
PDF 3	To be documented
Evidence Required
Screenshot of restricted-area access
Evidence for PDF 1
Evidence for PDF 2
Evidence for PDF 3

Important: Actual patient PDFs and medical information should not be uploaded to a public GitHub repository.

11. Milestone 2 — PDF Encryption Analysis

For each authorized PDF, the following commands can be used:

file report1.pdf
pdfinfo report1.pdf
exiftool report1.pdf
qpdf --show-encryption report1.pdf
Results
File	Encryption	Method	Recovery Result	Evidence
PDF 1	TBD	TBD	TBD	Screenshot
PDF 2	TBD	TBD	TBD	Screenshot
PDF 3	TBD	TBD	TBD	Screenshot

Only update these fields after the actual files have been analyzed.

12. Milestone 3 — Critical Data Exposure

The project requires investigation of a critical server-side exposure involving:

Hospital employee salary information
Hospital shareholder information

The investigation should include analysis of retrieved files, file properties, filenames, metadata, and other clues discovered during the authorized assessment.

Current Status

Critical exposure: Not yet confirmed in this report.

Once reproduced, document:

Affected endpoint/resource
Discovery method
Access method
Evidence
Type of information exposed
Security impact
Recommended remediation

Do not publish actual salary information, shareholder information, or other confidential data on GitHub.

13. Findings Summary
ID	Finding	Severity	Status
MED-01	Exposed HTTP/HTTPS services	Informational	Confirmed
MED-02	Patient authentication endpoint identified	Informational	Confirmed
MED-03	CSRF token not observed in login request	TBD	Observation
MED-04	SQL Injection	TBD	Not Confirmed
MED-05	Restricted-resource exposure	TBD	To Be Documented
MED-06	PDF encryption weakness	TBD	To Be Documented
MED-07	Sensitive employee/shareholder information exposure	TBD	To Be Documented
14. Risk Rating

Final severity should be assigned only after assessing:

Exploitability
Authentication requirements
Confidentiality impact
Integrity impact
Availability impact
Scope of affected resources
Sensitivity of exposed information

Possible ratings:

Critical
High
Medium
Low
Informational

Each rating should include evidence-based justification.

15. Recommendations
15.1 Authentication
Implement secure authentication controls.
Regenerate session identifiers after successful authentication.
Use appropriate Secure, HttpOnly, and SameSite cookie attributes.
Avoid unnecessarily detailed authentication error messages.
Implement appropriate account/session protections.
15.2 SQL Injection Prevention
Use parameterized/prepared SQL statements.
Avoid constructing SQL queries using string concatenation.
Implement server-side input validation.
Use least-privilege database accounts.
Avoid exposing database errors to users.
15.3 Access Control
Enforce authorization checks on every protected resource.
Verify that an authenticated user has permission to access each requested object.
Do not rely solely on hidden URLs or client-side restrictions.
15.4 Sensitive Documents
Store confidential documents outside publicly accessible directories where possible.
Require authorization before serving documents.
Avoid predictable document locations.
Apply appropriate encryption and key-management controls.
15.5 Sensitive Information
Restrict employee salary information to authorized personnel.
Restrict shareholder information appropriately.
Remove unnecessary sensitive information from web-accessible locations.
Monitor access to sensitive resources.
Review server and application permissions.
16. Evidence Structure

Recommended evidence structure:

evidence/
├── 01-dns.png
├── 02-nmap.png
├── 03-login-request.png
├── 04-login-response.png
├── 05-restricted-area.png
├── 06-pdf1.png
├── 07-pdf2.png
├── 08-pdf3.png
├── 09-pdf-analysis.png
└── 10-critical-exposure.png
Public GitHub Safety

Before uploading this report or screenshots, remove:

Passwords
PHP session IDs
CSRF/session tokens
Patient names
Medical information
Employee salary information
Shareholder personal information
Confidential PDFs
Other sensitive client information
17. Key Commands Used
DNS Enumeration
dig medirozahospital.com
dig www.medirozahospital.com
dig MX medirozahospital.com
dig NS medirozahospital.com
HTTP Testing
curl -I https://medirozahospital.com
Port Enumeration
nmap -Pn -p 80,443 199.188.201.16
SQL Injection Assessment
sqlmap -r login-request.txt -p username --batch --level=1 --risk=1
PDF Analysis
pdfinfo report.pdf
exiftool report.pdf
qpdf --show-encryption report.pdf
18. Conclusion

The authorized assessment successfully established the external web presence of the Mediroza application and identified its patient authentication endpoint.

Initial reconnaissance confirmed:

Target: medirozahospital.com
IP: 199.188.201.16
HTTP: 80/tcp open
HTTPS: 443/tcp open
Web Server: LiteSpeed
Application: PHP-based patient portal
Login Endpoint: /patient/login.php

Authentication traffic was successfully captured and analyzed using Burp Suite. The login request was examined for credentials, session information, CSRF controls, and authentication behavior.

SQL injection testing was also initiated as part of the authorized assessment. At this stage, SQL injection has not been classified as a confirmed vulnerability without reproducible technical evidence.

Further project milestones should be updated with verified evidence from the authorized environment before being marked as complete.

19. Disclaimer

This penetration test was conducted as part of an authorized cybersecurity internship/laboratory exercise.

All testing was intended for the authorized target only.

No denial-of-service testing or social engineering was performed.

Confidential client information should remain protected and should not be published in public repositories.

Tester: Ssemakula Arthur
Project: Networkwalks Week 4 — Batch B083
Assessment: Mediroza General Hospital Web Application Penetration Test
