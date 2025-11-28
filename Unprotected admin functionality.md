# Lab: Unprotected admin functionality

## 1. Vulnerability Description
**Type:** Security by Obscurity / Information Leakage
**Severity:** High

The application attempts to restrict access to the administrative panel by placing it at an unlinked URL path, assuming attackers cannot find it. However, the application discloses this sensitive path in the `robots.txt` file, which is publicly accessible. This file is intended to instruct web crawlers (like Google) on which pages to ignore, but it serves as a roadmap for attackers to discover hidden directories.

## 2. Steps to Reproduce
1.  Navigate to the application root URL.
2.  Append `/robots.txt` to the URL (e.g., `https://YOUR-LAB-ID.web-security-academy.net/robots.txt`).
3.  Observe the Disallow directive revealing the administrative path: `Disallow: /administrator-panel`.
4.  Navigate to this disclosed path (`/administrator-panel`).
5.  The application grants full administrative access without further authentication.
6.  Delete the user `carlos` to demonstrate impact.

## 3. Impact
* **Unauthorized Access:** Attackers can easily discover and access the administrative interface.
* **System Compromise:** Full control over user accounts and system configuration.

## 4. Remediation
**Server-Side Access Control:**
Do not rely on hiding paths. Implement strict authentication and authorization checks (e.g., checking session roles) for every sensitive endpoint.

**Clean robots.txt:**
Do not include sensitive paths in `robots.txt`. If a page should not be crawled, it should be protected by login, making the `robots.txt` entry unnecessary.
