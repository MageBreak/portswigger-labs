# Lab: Unprotected admin functionality with unpredictable URL

## 1. Vulnerability Description
**Type:** Security by Obscurity / Information Disclosure
**Severity:** High

The application attempts to restrict access to the administrative interface by hiding it behind an unpredictable URL path (`/admin-yhidcd`) rather than implementing genuine server-side access controls. Furthermore, the "secret" URL is hardcoded within the client-side JavaScript source code (`var adminPanelTag`), which is delivered to all users regardless of their privilege level. An attacker can simply view the page source to discover the URL and access the administrative panel.

## 2. Steps to Reproduce
1.  Navigate to the application homepage.
2.  View the page source (`Ctrl+U` or Right Click -> View Page Source).
3.  Search for the string `admin`.
4.  Locate the JavaScript logic revealing the hidden path:
    ```javascript
    var adminPanelTag = document.createElement('a');
    adminPanelTag.setAttribute('href', '/admin-yhidcd');
    ```
5.  Append `/admin-yhidcd` to the base application URL in the browser.
6.  The application grants full administrative access without further authentication.
7.  Delete the user `carlos` to demonstrate impact.

## 3. Impact
* **Unauthorized Access:** Attackers gain full administrative privileges.
* **Data Loss:** Attackers can delete user accounts or manipulate system data.

## 4. Remediation
**Implement Server-Side Access Control:**
Never rely on secret URLs for security. Use standard authorization checks on the server side.

**Example (Pseudo-code):**
```python
# VULNERABLE: relying on path secrecy
@app.route('/admin-secret-x99')
def admin_panel():
    return render_template('admin.html')

# SECURE: checking session/role
@app.route('/admin')
def admin_panel():
    if not current_user.is_admin:
        abort(403)
    return render_template('admin.html')
