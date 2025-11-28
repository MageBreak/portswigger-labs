# Lab: URL-based access control can be circumvented

## 1. Vulnerability Description
**Type:** Broken Access Control (Header-Based Bypass)
**Severity:** High
**Vulnerable Header:** `X-Original-URL`

The application utilizes a front-end security mechanism (likely a reverse proxy or framework filter) that restricts access based on the URL path in the request line. However, the backend application supports non-standard headers like `X-Original-URL` to override the requested path. This creates a discrepancy where the front-end allows a request to a public page (e.g., `/`), but the backend processes it as a request to a restricted page (e.g., `/admin`), bypassing the access controls entirely.

## 2. Steps to Reproduce
1.  **Recon:** Intercept a request to a public page (`GET /`) and send it to Repeater.
2.  **Probe:** Add the header `X-Original-URL: /nonexistent`. Observe a `404 Not Found` response, confirming the server processes the header.
3.  **Bypass:** Modify the request to target the admin panel:
    * **Request Line:** `GET / HTTP/1.1` (Allowed)
    * **Header:** `X-Original-URL: /admin`
    * **Result:** The server returns the Admin Panel HTML (`200 OK`).
4.  **Exploit:** Extract the delete link for the user `carlos` from the response HTML (`/admin/delete?username=carlos`).
5.  **Execution:** Send a final request to perform the privileged action:
    ```http
    GET / HTTP/1.1
    Host: <LAB-ID>.web-security-academy.net
    X-Original-URL: /admin/delete?username=carlos
    ```
6.  The user `carlos` is deleted.

## 3. Impact
* **Access Control Bypass:** Attackers can access any protected URL by "tunneling" it through a public URL.
* **Unauthorized Actions:** Administrative functions (delete users, config changes) are exposed to unauthenticated users.

## 4. Remediation
**Disable Override Headers:**
Configure the web server or framework to ignore or strip headers like `X-Original-URL`, `X-Rewrite-URL`, and `X-Forwarded-Prefix` from external requests.

**Unify Access Control:**
Ensure that the component performing access control (the proxy) and the component processing the request (the app) use the same method to determine the URL.
