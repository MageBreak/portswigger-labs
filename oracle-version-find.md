# SQL Injection: Querying the Database Type and Version on Oracle

## Lab Objective
The goal of this lab is to exploit a SQL injection vulnerability in the product category filter to retrieve the database version string. The target database is Oracle, requiring specific syntax (`FROM` clauses) compared to other SQL dialects.

## Steps Taken
1. Intercepted the request to:
`GET /filter?category=Accessories`
in Burp Suite.
2. Determined the number of columns using a `UNION SELECT` attack.
   - *Crucial Step:* Since this is Oracle, I had to include `FROM dual` in every injected query.
   - Verified that the query returns 2 columns.
3. Identified text-compatible columns by injecting a dummy string `'abc'` into both positions.
   - Confirmed that both columns accept text data.
4. Retrieved the version string by selecting the `banner` column from the `v$version` table.
   - The output (Oracle Database 11g...) was displayed in the product list, solving the lab.

## Payloads Used
- **Column Enumeration (Oracle):**
`' UNION SELECT NULL, NULL FROM dual--`
- **Data Type Confirmation:**
`' UNION SELECT 'abc', NULL FROM dual--`
- **Final Exploit (Version Extraction):**
`' UNION SELECT banner, NULL FROM v$version--`

## Lessons Learned
- **Oracle Syntax Requirement:** Unlike MySQL or PostgreSQL, Oracle requires every `SELECT` statement to have a `FROM` clause. We must use the built-in dummy table `dual` (e.g., `SELECT 'abc' FROM dual`).
- **Version Tables:** Oracle stores version information in the `v$version` view, specifically in the `banner` column.
- **UNION Attacks:** The injected query must match the column count and data types of the original query to function correctly.

---
✅ **Lab Status: Solved**
