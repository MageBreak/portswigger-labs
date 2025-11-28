# Lab: User ID controlled by request parameter with data leakage in redirect

## 1. Vulnerability Description
**Type:** Information Leakage via Redirection / Broken Access Control
**Severity:** High

The application implements an access control check that correctly identifies unauthorized access attempts and issues an HTTP `302 Found` redirect to the login page. However, due to improper server-side logic, the application writes the sensitive page content (the victim's profile and API key) into the response body *before* or *during* the generation of the redirect header. While standard browsers follow the redirect immediately and do not display this content, an attacker using a proxy (like Burp Suite) can inspect the HTTP response body and recover the sensitive data.

## 2. Steps to Reproduce
1.  Log in with a low-privileged account (`wiener`).
2.  Navigate to the "My Account" page (`/my-account?id=wiener`) and capture the request in Burp Proxy.
3.  Send the request to Burp Repeater.
4.  Modify the `id` parameter to the target user: `id=carlos`.
5.  Send the request.
6.  Observe that the server returns a `302 Found` status code.
7.  **Crucial Step:** Do not follow the redirect. Instead, inspect the HTTP response body (Raw or Render view).
8.  The response body contains the full HTML of the victim's account page, including their API key, despite the redirect header being present.

## 3. Impact
* **Confidentiality Violation:** Sensitive user data (API keys, PII) is fully exposed to attackers who inspect raw HTTP responses, completely bypassing the intended access control.

## 4. Remediation
**Terminate Execution Early:**
The server must stop processing the request immediately after deciding to redirect. No further content should be written to the response body.

**Code Example (Pseudo-code Fix):**
```python
# VULNERABLE
if not user.is_authorized:
    response.status = 302
    response.headers['Location'] = '/login'
    # The code continues running and renders the profile below...
    return render_template('profile.html', user=target_user)

# SECURE
if not user.is_authorized:
    return redirect('/login') # Function immediately stops execution
