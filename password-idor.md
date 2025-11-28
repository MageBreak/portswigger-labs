# Lab: Insecure Direct Object References (IDOR)

## 1. Vulnerability Description
**Type:** IDOR (Insecure Direct Object Reference) / Broken Access Control
**Severity:** High

The application stores user chat transcripts as static files on the server using a predictable, sequential naming convention (e.g., `1.txt`, `2.txt`, `3.txt`). The server fails to validate that the user requesting the file is the owner of the chat session. An attacker can exploit this by modifying the filename integer in the download URL to access and download chat logs belonging to other users.

## 2. Steps to Reproduce
1.  Log in and initiate a "Live Chat" session.
2.  Click **View transcript** to download the log.
3.  Observe the download URL (e.g., `/download-transcript/3.txt`).
4.  Intercept the request in Burp Suite Repeater.
5.  Decrement the filename number (e.g., change `3.txt` to `1.txt`).
6.  Send the request. The server returns the chat log of a different user.
7.  Inspect the unauthorized log to find the victim's plaintext password.
8.  Use the stolen credentials to log in as the victim (`carlos`).

## 3. Impact
* **Confidentiality Violation:** Unauthorized access to private user communications.
* **Credential Theft:** Exposure of sensitive data (passwords) leading to full account takeover.

## 4. Remediation
**Indirect Object References:**
Avoid using sequential IDs for direct file access. Use a session-based mapping or cryptographically random UUIDs (e.g., `download?id=7f8a...`) that cannot be guessed.

**Access Control Enforcement:**
Implement strict ownership checks at the controller level before serving any static resource associated with a user account.
