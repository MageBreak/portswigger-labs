# Lab: Blind OS command injection with output redirection

## 1. Vulnerability Description
**Type:** Blind OS Command Injection (Out-of-Band via Local File)  
**Severity:** High  
**Vulnerable Endpoint:** `/feedback/submit`  

The application executes a background shell command containing user-supplied feedback details. While the application does not return command output in its response, it shares a file system with a publicly accessible directory used for static assets (e.g., `/var/www/images/`). An attacker can use the shell's output redirection operator (`>`) to write the results of an injected command into a file in that directory. Because the directory is served by the web server, the attacker can then retrieve the command output via a standard HTTP `GET` request.

## 2. Steps to Reproduce
1.  **Identify Target:** Note the writable, public directory provided by the application environment: `/var/www/images/`.
2.  **Intercept:** Capture the `POST /feedback/submit` request in Burp Suite and send it to Repeater.
3.  **Redirect Output:** Inject a command into the vulnerable parameter (e.g., `email`) using a shell separator and the redirection operator:  
    `& whoami > /var/www/images/output.txt &`  
    *(Ensure spaces are encoded as `+` and the `>` as `%3e`)*.
4.  **Execute:** Send the request. The server returns a generic `200 OK`, but the command has executed on the backend.
5.  **Fetch Output:** Locate an image-loading endpoint on the site (e.g., `GET /image?filename=...`). 
6.  **Exfiltrate:** Request the created file by swapping the filename: `GET /image?filename=output.txt`. The server will return the result of the `whoami` command in the response body.

## 3. Impact
* **Information Disclosure:** Attackers can read sensitive system files, environment variables, or database credentials.
* **Persistent Access:** If the directory has execution permissions, this can lead to the placement of a permanent web shell.

## 4. Remediation
**Principle of Least Privilege:**
The web application process should never have write permissions to directories that are served publicly to the web. Configure strict file system permissions so that the web root is read-only for the application user, and only specific, non-executable directories are writable if absolutely necessary for business logic.
