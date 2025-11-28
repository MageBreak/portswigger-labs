# Lab: User ID controlled by request parameter with password disclosure

## 1. Vulnerability Description
**Type:** IDOR (Insecure Direct Object Reference) / Sensitive Data Exposure
**Severity:** Critical

The application allows authenticated users to view other users' profiles by manipulating the `id` parameter in the URL. Compounding this access control failure, the application follows the insecure practice of pre-filling the user's existing password in the "Update Profile" form. When an attacker requests the administrator's profile page (e.g., `id=administrator`), the server returns the HTML containing the administrator's current password in plaintext within the `value` attribute of the `<input type="password">` field.

## 2. Steps to Reproduce
1.  Log in as a low-privileged user (`wiener`).
2.  Navigate to the "My Account" page (`/my-account?id=wiener`).
3.  Intercept the request in Burp Suite and send it to Repeater.
4.  Change the `id` parameter from `wiener` to `administrator`.
5.  Send the request.
6.  Inspect the response body (HTML). Search for `name="password"`.
7.  Extract the plaintext password from the value attribute:
    ```html
    <input required type="password" name="password" value="ADMIN_PASSWORD_HERE">
    ```
8.  Log out of the current account.
9.  Log in as `administrator` using the extracted password.
10. Navigate to the Admin Panel and delete the user `carlos`.

## 3. Impact
* **Total System Compromise:** An attacker can gain full administrative access to the application, allowing them to modify data, delete users, and potentially execute further attacks.

## 4. Remediation
**Do Not Pre-fill Secrets:**
Password fields should never be pre-filled on the client side. If a user wishes to change their password, they should provide the old password and the new password in blank fields.

**Strict Access Control:**
The application should verify that the session owner matches the `id` requested in the URL.
