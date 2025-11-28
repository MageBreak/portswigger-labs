# 🛡️ PortSwigger Web Security Academy - Writeups

**Author:** MageBreak  
**Focus:** Web Application Security & Offensive Operations  
**Total Labs Solved:** 18

This repository documents my journey through the PortSwigger Web Security Academy. Each write-up includes the methodology, payloads used, and key takeaways for future engagements.

---

## 🔓 Access Control (14 Labs)
*Focus: IDOR, Privilege Escalation, and Broken Access Control logic.*

### **Practitioner (Intermediate)**
- [Method-based access control can be circumvented](MBAC-circumvent.md) 
- [URL-based access control can be circumvented](access-bypass.md)
- [Multi-step process with no access control on one step](Multi-step.md)
- [Referer-based access control](Referer-based%20access%20control.md)

### **Apprentice (Basics)**
- [Unprotected admin functionality](Unprotected%20admin%20functionality.md)
- [Unprotected admin functionality with unpredictable URL](unpredictable-URL.md)
- [User role controlled by request parameter](User%20role%20controlled%20by%20request%20parameter.md)
- [User role can be modified in user profile](json-exploit.md)
- [User ID controlled by request parameter](horizontal-access-1.md)
- [User ID controlled by request parameter, with unpredictable user IDs](unpredictable%20user%20IDs.md)
- [User ID controlled by request parameter with data leakage in redirect](data-leakage-id.md)
- [User ID controlled by request parameter with password disclosure](cleartext.md)
- [Insecure direct object references (IDOR)](password-idor.md)

---

## 💉 SQL Injection (4 Labs)
*Focus: Data Exfiltration, Union Attacks, and Database Enumeration.*

- [Retrieving hidden data](retrieving-hidden-data.md)
- [Determining the number of columns with UNION](union-number-of-columns.md)
- [Retrieving Multiple Values In a Single Column](union-multiple-values-single-column.md)
- [Querying the database type and version on Oracle](oracle-version-find.md)

---

## 🚧 Coming Soon / In Progress
- **OS Command Injection**
- **Authentication Vulnerabilities**
- **Server-Side Request Forgery (SSRF)**
