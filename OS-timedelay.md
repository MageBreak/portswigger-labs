# Lab: Blind OS command injection with time delays

## 1. Vulnerability Description
**Type:** Blind OS Command Injection (Time-Based)  
**Severity:** High  
**Vulnerable Endpoint:** `/feedback/submit`  

The application executes a background shell command containing user-supplied feedback details. The output of this command is not returned in the HTTP response, and system errors are suppressed. However, the application process is synchronous; it waits for the shell command to complete before sending the final HTTP response. By injecting commands that force a measurable pause in execution, an attacker can confirm the vulnerability's existence through response latency.

## 2. Steps to Reproduce
1.  **Recon:** Submit a feedback form and capture the `POST /feedback/submit` request in Burp Suite.
2.  **Test Parameters:** Send the request to Repeater and iterate through form fields (`name`, `email`, `subject`, `message`) to find the injection point.
3.  **Inject Delay:** Use a shell connector like `&` (encoded as `%26`) followed by a time-delay command such as `sleep 10` or `ping -c 10 127.0.0.1`.
4.  **Confirm:** Observe the **"Response Received"** timer in the bottom right of Burp Repeater. If the response takes approximately 10 seconds longer than a baseline request, the parameter is vulnerable.

## 3. Impact
* **Vulnerability Confirmation:** Proves that Remote Code Execution (RCE) is possible even when the server provides no direct feedback or output.
* **Denial of Service (DoS):** Attackers can exhaust server resources and thread pools by forcing multiple long-running sleep cycles.

## 4. Remediation
**Asynchronous Execution:** Process sensitive background tasks (like sending emails) in a separate queue that is decoupled from the user's HTTP request/response cycle. Additionally, avoid concatenating user input into shell commands; use safer alternatives like internal APIs or strictly validate input against a whitelist of permitted characters.
