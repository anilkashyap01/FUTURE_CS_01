# 🛡️ Vulnerability Assessment & Penetration Test Report: OWASP Juice Shop

**Target:** `http://localhost:3000` (OWASP Juice Shop)  
**Author:** Anil Kashyap  
**Assessment Type:** Web Application Penetration Testing & Passive Security Audit  
**Date:** August 24, 2026

## 📋 Executive Summary
A comprehensive security assessment was conducted against the target application to identify vulnerabilities aligning with the OWASP Top 10 framework. The assessment revealed critical flaws in authentication, access control, and input sanitization, allowing unauthenticated attackers to hijack accounts, execute arbitrary scripts, and access sensitive internal files.

## 🛠️ Methodology & Tooling
- **Reconnaissance & Enumeration:** Nmap (Service Discovery), FFUF & Gobuster (Directory Brute-forcing)
- **Passive Analysis:** OWASP ZAP (Security Header Audit)
- **Vulnerability Exploitation:** Burp Suite Professional (Traffic Interception, Parameter Tampering, Intruder Brute-forcing)
- **Manual Assessment:** Browser Developer Tools (DOM Inspection, Session Storage Manipulation)

## 🚨 Key Findings & Risk Classification

| Severity | Vulnerability | OWASP Category | Affected Endpoint |
| :---: | :--- | :--- | :--- |
| **High** | SQL Injection (Auth Bypass) | A03:2021 - Injection | `/#/login` |
| **High** | Broken Authentication | A07:2021 - Auth Failures | `/#/forgot-password` |
| **Medium** | Insecure Direct Object Reference | A01:2021 - Access Control | `/rest/basket/` |
| **Medium** | DOM-based XSS | A03:2021 - Injection | `/#/search?q=` |
| **Medium** | Sensitive Directory Exposure | A01:2021 - Access Control | `/ftp/` |
| **Low** | Missing Security Headers | A05:2021 - Misconfiguration | Global |

---

## 🛠️ Vulnerability Findings & Remediations

### 1. SQL Injection (Authentication Bypass)
* **Severity:** High
* **Description:** The login endpoint (`/#/login`) fails to sanitize user input, allowing attackers to inject SQL commands (e.g., `' OR 1=1 --`) to manipulate the backend query and bypass authentication entirely.
* **Remediation:** 
  * Replace concatenated SQL strings with **Parameterized Queries** (Prepared Statements).
  * Utilize an Object-Relational Mapper (ORM) with built-in parameter binding to enforce strict separation between code and data.

### 2. Broken Authentication (Insecure Password Reset)
* **Severity:** High
* **Description:** The password reset mechanism relies on predictable or easily brute-forced security questions, allowing unauthorized account takeover (e.g., bypassing Bender's account via brute force).
* **Remediation:** 
  * Deprecate the use of security questions for account recovery.
  * Implement time-limited, cryptographically secure password reset tokens delivered directly to the user's verified email.
  * Enforce Multi-Factor Authentication (MFA) for all account changes.

### 3. Insecure Direct Object Reference (IDOR)
* **Severity:** Medium
* **Description:** The application tracks user shopping baskets via a predictable integer (`bid`) stored in client-side Session Storage. Modifying this integer allows an attacker to view and manipulate other users' baskets.
* **Remediation:** 
  * Implement strict **server-side access controls**. 
  * Validate that the currently authenticated session (`req.user.id`) possesses explicit ownership rights over the requested object/basket before returning data.

### 4. DOM-based Cross-Site Scripting (XSS)
* **Severity:** Medium
* **Description:** The search functionality (`/#/search?q=`) reflects user input directly into the Document Object Model (DOM) without sanitization, permitting the execution of arbitrary JavaScript.
* **Remediation:** 
  * Implement **context-aware output encoding** before reflecting user input in the browser.
  * Avoid unsafe JavaScript sinks like `innerHTML` and utilize safe alternatives such as `textContent`.
  * Enforce a strict `Content-Security-Policy` (CSP) to restrict script execution sources.

### 5. Sensitive Directory Exposure
* **Severity:** Medium
* **Description:** The web server exposes an internal directory (`/ftp/`), allowing unauthenticated attackers to browse and download sensitive internal files, including backups and configuration data.
* **Remediation:** 
  * **Disable directory listing** (e.g., `Options -Indexes` in Apache or equivalent in Node/Express).
  * Relocate backup files, source code, and sensitive documentation outside of the public web root.

### 6. Missing Security Headers
* **Severity:** Low
* **Description:** Passive scanning via OWASP ZAP revealed the absence of modern defensive HTTP headers, leaving the application vulnerable to clickjacking and MIME-sniffing.
* **Remediation:** Configure the web server to enforce the following headers globally:
  * `X-Content-Type-Options: nosniff`
  * `X-Frame-Options: DENY` (or `SAMEORIGIN`)
  * `Content-Security-Policy: default-src 'self'`

---

## 📈 Professional Self-Analysis & Improvement Review

### What Went Well:
* **Methodological Approach:** We didn't just fire automated tools blindly. We identified the "Soft 404" trap during the FFUF scan and adjusted our parameters (`-fs 9393`) to find the actual hidden directories (`/ftp`).
* **Evidence Gathering:** We captured clear, indisputable proof of concepts (PoCs) for both the Burp Intruder attack (identifying the 200 OK status) and the DOM XSS execution in the browser.
* **Clear Classification:** We mapped the findings directly to business risk (High/Medium/Low) rather than just listing technical bugs.

### Areas for Professional Improvement:
* **Tool Output Management:** In a real-world scenario, dumping unformatted, raw terminal output (like the initial massive FFUF log) into a report is bad practice. We must always filter and present only the actionable data to the client.
* **Passive Scanning Integration:** We initially relied heavily on manual exploitation. In a professional engagement, running the passive crawler (ZAP/Burp Spider) first while manually browsing the site ensures we capture all baseline misconfigurations (like missing headers) before active attacks begin.
* **Remediation Specificity:** While we provided good general advice (e.g., "Use parameterized queries"), a top-tier report includes specific code snippets or framework configurations relevant to the client's tech stack (e.g., specifically addressing Angular DOM sanitization functions for the XSS finding).

---

## 📁 Repository Structure
- `/report/` - Contains the final PDF Vulnerability Assessment Report.
- `/screenshots/` - Contains proof-of-concept visual evidence for all identified vulnerabilities.

*This repository serves as the official submission for Task 1 of the Future Interns Cyber Security Program.*
