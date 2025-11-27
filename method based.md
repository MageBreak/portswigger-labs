# Lab: Method-based access control can be circumvented

## 1. Vulnerability Description
**Type:** Broken Access Control (Method-Based)
**Severity:** High

The application implements access control checks strictly on the HTTP `POST` method for sensitive administrative endpoints (specifically `/admin-roles`). The backend application logic, however, processes requests regardless of the HTTP verb used. By capturing a legitimate administrative request and toggling the HTTP method to `GET` (or `HEAD`/`PUT`), a low-privileged user can bypass the authorization filter while still triggering the state-changing action (privilege escalation).

## 2. Steps to Reproduce
1.  Log in as an administrator to identify the target endpoint: `POST /admin-roles`.
2.  Capture the request: `action=upgrade&username=target_user`.
3.  Log out and log in as a low-privileged user (`wiener`).
4.  Copy the low-privileged session cookie.
5.  In Burp Repeater, reconstruct the admin request using the low-privileged session cookie and the target username `wiener`.
6.  Right-click and select **"Change request method"**. The request converts to:
    `GET /admin-roles?action=upgrade&username=wiener HTTP/1.1`
7.  Send the request. The server returns `200 OK` (bypassing the POST-only check).
8.  Refresh the page to confirm `wiener` now has administrative privileges.

## 3. Impact
* **Privilege Escalation:** Regular users can promote themselves to administrators.
* **Unauthorized Actions:** Attackers can perform any administrative function (delete users, change settings) by simply changing the HTTP verb.

## 4. Remediation
**Apply Security to All Methods:**
Security constraints should be applied to the *URL endpoint* regardless of the HTTP method.

**Spring Security Example (Java):**
Avoid method-specific matching for security boundaries unless absolutely necessary.
```xml
<intercept-url pattern="/admin-roles" access="hasRole('ADMIN')" method="POST" />

<intercept-url pattern="/admin-roles" access="hasRole('ADMIN')" />
