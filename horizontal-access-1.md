# Lab: User ID controlled by request parameter

## 1. Vulnerability Description
**Type:** Horizontal Privilege Escalation (IDOR)
**Severity:** High

The application suffers from an Insecure Direct Object Reference (IDOR) vulnerability on the "My Account" page. The application relies solely on the `id` GET parameter to identify which user profile to render. It fails to validate that the session cookie of the requester matches the `id` parameter requested. This allows any authenticated user to access the account details (including sensitive API keys) of any other user by simply guessing their username.

## 2. Steps to Reproduce
1.  Navigate to the login page and authenticate as a low-privileged user (`wiener:peter`).
2.  Observe the URL upon successful login: `https://<LAB-ID>.web-security-academy.net/my-account?id=wiener`.
3.  Modify the `id` parameter in the URL from `wiener` to the victim's username, `carlos`.
4.  Send the request.
5.  The application responds with the account page of the user `carlos`, revealing their API key.

## 3. Impact
* **Confidentiality Violation:** An attacker can view PII (Personally Identifiable Information) and API keys of all other users.
* **Account Takeover:** If the page allows password changes or email updates without re-authentication, this could lead to full account takeover.

## 4. Remediation
**Access Control Implementation:**
Instead of trusting the client-side `id` parameter, the server should derive the user identity directly from the session token or authentication cookie.

**Example (Pseudo-code Fix):**
```python
# VULNERABLE:
# user = query_db(request.GET['id'])

# SECURE:
# user_id_from_session = session['user_id']
# user = query_db(user_id_from_session)
