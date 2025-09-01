# SQL Injection: Retrieving Hidden Data

## Lab Objective
The goal of this lab is to exploit a SQL injection vulnerability in the product category filter to reveal hidden products.

## Steps Taken
1. Intercepted the request to:
GET /filter?category=Food+%26+Drink
in Burp Suite.
2. Injected a single quote `'` → the application returned an error, confirming SQL injection.
3. Used the payload:
' OR 1=1--
to modify the query so that it returned all rows regardless of category.
4. Verified that hidden products were now visible in the response.

## Payloads Used
- Test for SQLi:
- Exploit:

## Lessons Learned
- Even basic Boolean SQL injection can expose sensitive data.  
- Always parameterize queries to prevent this type of attack.  
- Quick tests like adding `'` are useful for confirming SQL injection vulnerabilities.

---
✅ **Lab Status: Solved**
