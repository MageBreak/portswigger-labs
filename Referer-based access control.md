# Lab: Referer-based access control

## 1. Vulnerability Description
**Type:** Broken Access Control / Insecure Header Validation
**Severity:** High
**Vulnerable Header:** `Referer`

The application implements a flawed access control mechanism that relies on the HTTP `Referer` header to verify the authorization of administrative requests. The application assumes that any request originating from the administrative dashboard (`/admin`) is legitimate and authorized. Since the `Referer` header is fully controlled by the client browser, an attacker can spoof this header to impersonate a privileged state and execute administrative actions using a low-privileged session.

## 2. Steps to Reproduce
1.  **Reconnaissance:**
    * Log in as an administrator.
    * Perform a privileged action (e.g., upgrade a user) and capture the request in Burp Suite.
    * Observe the header: `Referer: https://<LAB-ID>.web-security-academy.net/admin`.
2.  **Session Acquisition:**
    * Log in as a low-privileged user (`wiener`) and obtain a valid session cookie.
3.  **Exploitation:**
    * Send the captured administrative request to Burp Repeater.
    * Replace the administrator's session cookie with the low-privileged user's cookie.
    * Modify the body payload to target the attacker's account (`username=wiener`).
    * **Crucial Step:** Ensure the `Referer` header is set to the admin URL found in step 1.
4.  **Execution:**
    * Send the forged request. The server verifies the `Referer` header, trusts the request, and performs the privilege escalation.
5.  **Verification:**
    * Refresh the browser session for `wiener` to confirm administrative access.

## 3. Impact
* **Privilege Escalation:** Standard users can escalate their privileges to administrator.
* **Security Bypass:** Demonstrates that client-side headers cannot be trusted for security decisions.

## 4. Remediation
**Server-Side Role Checks:**
Authentication and authorization must be validated against the user's session state on the server. Never rely on HTTP headers (like `Referer`, `Host`, or `X-Forwarded-For`) to determine privilege levels.

**Robust Validation:**
If Referer checks are required for CSRF protection (though Anti-CSRF tokens are preferred), they must be combined with strict session validation.
