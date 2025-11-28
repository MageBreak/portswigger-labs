# Lab: User role controlled by request parameter

## 1. Vulnerability Description
**Type:** Broken Access Control / Insecure State Management
**Severity:** High

The application determines the user's privilege level using an insecure mechanism: a client-side cookie named `Admin`. Upon login, the server sets this cookie to `false`. Because cookies are stored on the client side and submitted with every request, an attacker can modify this value to `true`. The server blindly trusts this cookie without verifying the claim against a secure backend session or database.

## 2. Steps to Reproduce
1.  Log in with standard credentials (`wiener:peter`).
2.  Intercept the login response or any subsequent request using Burp Suite.
3.  Locate the cookie header: `Cookie: session=...; Admin=false`.
4.  Modify the cookie value to `Admin=true`.
5.  Forward the request.
6.  Navigate to the `/admin` panel. The server now recognizes the user as an administrator based on the forged cookie.
7.  Delete the user `carlos`.

## 3. Impact
* **Privilege Escalation:** Instant escalation from standard user to administrator.
* **Trust Boundary Violation:** The application mistakenly trusts client-side input for authorization decisions.

## 4. Remediation
**Server-Side Session Management:**
Privilege levels should never be stored in client-side cookies or tokens that are not cryptographically signed. Store roles in the backend session or use signed JWTs (JSON Web Tokens) that cannot be tampered with.
