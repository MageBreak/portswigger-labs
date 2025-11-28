# Lab: Method-based access control can be circumvented

## 1. Vulnerability Description
**Type:** Broken Access Control (Method-Based)
**Severity:** High
**Vulnerable Endpoint:** `/admin-roles`

The application implements access control checks strictly on the HTTP `POST` method for sensitive administrative endpoints. The backend logic, however, processes requests regardless of the HTTP verb used. By capturing a legitimate administrative request and toggling the HTTP method to `GET`, a low-privileged user can bypass the authorization filter while still triggering the state-changing action.

## 2. Steps to Reproduce
1.  **Recon:** Log in as administrator and capture a `POST /admin-roles` request (`action=upgrade&username=carlos`).
2.  **Session Swap:** Log in as a low-privileged user (`wiener`) and obtain a valid session cookie.
3.  **Forgery:** In Burp Repeater, use the low-privileged session cookie and change the target username to `wiener`.
4.  **Bypass:** Right-click and select **"Change request method"** to convert the request to `GET /admin-roles?username=wiener&action=upgrade`.
5.  **Execute:** Send the request. The server returns `200 OK` (bypassing the POST-only security check) and promotes the user.

## 3. Impact
* **Privilege Escalation:** Regular users can promote themselves to administrators.
* **Unauthorized Actions:** Attackers can perform administrative functions by simply changing the HTTP verb.

## 4. Remediation
**Apply Security to All Methods:**
Security constraints must be applied to the URL endpoint globally, regardless of the HTTP method used to access it.
