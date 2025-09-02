# SQLi: UNION attack, retrieving multiple values in a single column

**Lab:** SQL injection UNION attack, retrieving multiple values in a single column (Practitioner)  
**Goal:** Extract all usernames and passwords from the `users` table and log in as `administrator`.

---

## 🛠️ Steps Taken

1. **Confirmed column count**  
   Tested with:
' UNION SELECT NULL,NULL--
→ Query worked, confirming **2 columns** in the underlying query.

2. **Tested data extraction**  
A direct:
' UNION SELECT username,password FROM users--
→ Caused errors (mismatched columns).

3. **Concatenated values into a single column**  
Used string concatenation with a delimiter:
' UNION SELECT NULL, username||'~'||password FROM users--
→ Successfully returned output in the application response.

4. **Identified credentials**  
Found the `administrator` account and its corresponding password in the results.

5. **Logged in**  
Used those credentials to log in as `administrator`, completing the lab.

---

## 🎯 Lessons Learned

- UNION queries must match **column count and datatype** with the original query.  
- Concatenation (`||`) helps when multiple values need to fit into a single column.  
- Adding a delimiter (like `~`) makes parsing results easier.  
- SQLi isn’t just about injection — it’s also about **formatting the output properly**.

---
