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
| **Low** | Missing Security Headers (CSP, X-Content-Type) | A05:2021 - Misconfiguration | Global |

## 📁 Repository Structure
- `/report/` - Contains the final PDF Vulnerability Assessment Report.
- `/screenshots/` - Contains proof-of-concept visual evidence for all identified vulnerabilities.

*This repository serves as the official submission for Task 1 of the Future Interns Cyber Security Program.*
