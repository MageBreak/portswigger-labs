# Lab: Multi-step process with no access control on one step

## 1. Vulnerability Description
**Type:** Broken Access Control / Business Logic Flaw
**Severity:** High

The application implements an administrative function (user role promotion) as a multi-step process. Step 1 requests the change, and Step 2 confirms it. The application fails to verify the user's permissions in Step 2, assuming that any request reaching Step 2 must have originated from a successful Step 1. By capturing the HTTP request for the confirmation step and replaying it with a low-privileged session token, an attacker can bypass the initial access control check and execute unauthorized administrative actions.

## 2. Steps to Reproduce
1.  **Blueprint Capture:**
    * Log in as an administrator.
    * Navigate to the Admin Panel and initiate a user upgrade/downgrade.
    * Capture the "Step 2" confirmation request in Burp Suite (`POST /admin-roles`).
    * Note the body parameters: `action=upgrade&confirmed=true&username=carlos`.
2.  **Session Acquisition:**
    * Log in as a low-privileged user (`wiener`) and copy the session cookie.
3.  **Exploitation:**
    * Send the captured confirmation request to Burp Repeater.
    * Replace the administrator's session cookie with the low-privileged user's cookie.
    * Modify the body parameters to target the attacker's account: `username=wiener`.
    * Ensure the action is set to `action=upgrade`.
4.  **Execution:**
    * Send the request. The server processes the confirmation without checking if the user is authorized to perform upgrades.
    * Receive a `302 Found` response.
5.  **Verification:**
    * Refresh the browser session for `wiener`. The Admin Panel is now accessible.

## 3. Impact
* **Privilege Escalation:** Standard users can promote themselves to administrators.
* **Access Control Bypass:** Critical security checks are rendered ineffective by skipping the specific steps where they are enforced.

## 4. Remediation
**Validate at Every Step:**
Do not assume that a user arriving at Step 2 has successfully passed the security checks of Step 1. Every state-changing request must independently validate the user's permissions.

**Session-Based State:**
Use server-side session variables to track the flow. For example, Step 2 should only accept input if `session.pending_action` was set by a successful Step 1.
