# Lab: OS command injection, simple case

## 1. Vulnerability Description
**Type:** OS Command Injection (In-band)  
**Severity:** Critical  
**Vulnerable Endpoint:** `/stock/products`  

The application passes user-supplied product and store IDs directly to a shell command meant to query stock levels. Because the input is not sanitized, an attacker can use shell metacharacters to terminate the intended command and execute arbitrary operating system commands. Since the application returns the raw output of the command in its HTTP response, the vulnerability is considered "in-band".

## 2. Steps to Reproduce
1.  **Intercept:** Capture a request to check stock levels (e.g., `POST /stock/check`) using Burp Suite.
2.  **Breakout:** Identify the parameter being passed to the shell (e.g., `storeID`).
3.  **Inject:** Append a shell command separator (like `&`, `;`, or `|`) followed by a standard Linux command such as `whoami`.
4.  **Analyze:** Send the request and observe the command output (the system username) reflected directly in the server's response.

## 3. Impact
* **Full System Compromise:** An attacker can gain complete control over the web server.
* **Data Breach:** Unauthorized access to sensitive files, environment variables, and system credentials.

## 4. Remediation
**Input Sanitization & Parameterization:** Avoid calling OS commands directly from application code. Use built-in APIs for the required functionality. If shell execution is unavoidable, strictly validate input against a whitelist or use parameterized functions to prevent metacharacter interpretation.
