# Lab: User ID controlled by request parameter, with unpredictable user IDs

## 1. Vulnerability Description
**Type:** IDOR (Insecure Direct Object Reference) / Information Leakage
**Severity:** High

The application uses Globally Unique Identifiers (GUIDs) to identify user accounts. While GUIDs are effective at preventing brute-force enumeration attacks (unlike sequential integers), the application leaks these IDs in public areas of the site. Specifically, the "Blog" section exposes the author's User ID in the URL parameters of the author filter link. Because the "My Account" page relies solely on the `id` parameter to retrieve profile data without verifying session ownership, an attacker can harvest a victim's GUID from a public post and use it to access their private account.

## 2. Steps to Reproduce
1.  **Reconnaissance:** Navigate to the public Blog section of the application.
2.  **ID Harvesting:** Locate a blog post written by the target user (`carlos`) and click the author's name to filter posts.
3.  **Extraction:** Observe the URL in the browser address bar (e.g., `/blogs?userId=d232f9b5-8a95...`) and copy the GUID.
4.  **Session Setup:** Log in with a low-privileged account (`wiener`).
5.  **Exploitation:** Navigate to the "My Account" page (`/my-account?id=my-guid`).
6.  **Parameter Swapping:** Replace the `id` parameter in the URL with the victim's GUID obtained in Step 3.
7.  **Access:** Press Enter. The application loads the victim's profile, revealing their sensitive API key.

## 3. Impact
* **Bypass of Obscurity:** Demonstrates that using complex, random IDs (GUIDs) is insufficient for security if those IDs are exposed anywhere in the application.
* **Confidentiality Violation:** Unauthorized access to PII (Personally Identifiable Information) and API keys of other users.

## 4. Remediation
**Session-Based Authorization:**
The application should never rely on client-supplied parameters (like `id`) to determine which profile to load for the "My Account" view. Instead, the backend should derive the user's identity strictly from their authenticated session token.

**Example (Secure Implementation):**
```java
// Do not read "id" from the URL
// String userId = request.getParameter("id"); 

// Read "id" from the Session
String userId = session.getAttribute("CURRENT_USER_ID");
UserProfile profile = database.getProfile(userId);
