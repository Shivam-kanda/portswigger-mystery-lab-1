# SQL Injection – Penetration Testing Report

## 1. Executive Summary

A SQL Injection vulnerability was identified in the application's product-category filtering functionality.

The vulnerable parameter allowed manipulation of the underlying SQL query. Through systematic testing, the number of returned columns was identified, UNION-based SQL Injection was confirmed, and the backend database was fingerprinted as PostgreSQL.

Further database enumeration identified a users table containing authentication-related columns. The resulting information demonstrated that the vulnerability could be used to obtain authentication credentials and access the administrator account.

**Severity:** High  
**Vulnerability:** SQL Injection  
**Attack Type:** UNION-based SQL Injection  
**Database:** PostgreSQL  
**Result:** Lab successfully solved

---

## 2. Lab Objective

The objective of the assessment was to identify and exploit a SQL Injection vulnerability in the application's product-category filtering functionality.

The investigation focused on:

- Identifying whether the parameter was vulnerable to SQL Injection.
- Determining the number of columns returned by the original query.
- Identifying columns capable of containing string data.
- Fingerprinting the backend database.
- Enumerating database tables.
- Identifying relevant columns within the discovered table.
- Retrieving authentication-related information.
- Validating the security impact through administrator authentication.

---

## 3. Initial Reconnaissance

The product-category filtering functionality was tested by modifying the `category` parameter.

An invalid input was initially supplied to observe the application's error behavior.

A database error was observed when the parameter was manipulated, indicating that user-controlled input was being incorporated into a SQL query.

A comment sequence was then tested to determine whether the remainder of the original query could be neutralized.

The application responded normally when the SQL comment syntax was successfully applied.

### Finding

The observed behavior indicated that the `category` parameter was potentially vulnerable to SQL Injection.

---

## 4. Determining the Number of Columns

The next step was to determine how many columns were returned by the original SQL query.

This was tested by incrementally modifying the `ORDER BY` clause.

The observed results indicated:

- `ORDER BY 1` — successful
- `ORDER BY 2` — successful
- `ORDER BY 3` — error
- `ORDER BY 4` — error

Therefore, the original query was determined to return **2 columns**.

---

## 5. UNION-Based SQL Injection Testing

After determining that the query returned two columns, UNION-based testing was performed.

The following type of payload was used to verify the column structure:

```sql
' UNION SELECT NULL,NULL--
``` 
The request was processed successfully.

String values were then supplied to determine which columns could contain string data:
```sql
' UNION SELECT 'a',NULL--
```
and
```sql
' UNION SELECT 'a','a'--
```
The results confirmed that the relevant columns could accept string data.

**Finding**

The application was confirmed to be vulnerable to UNION-based SQL Injection.

## 6. Database Fingerprinting

Database-specific queries were tested to identify the backend database management system.

The following database identification functions were considered:

```sql
' UNION SELECT version(),NULL--
```
The application returned information identifying the database as:

**PostgreSQL 12.22**

This established that the backend database was **PostgreSQL**.

## 7. Database Table Enumeration

After identifying PostgreSQL, database metadata was queried through the 'information_schema' views.

The following query was used to identify table names:
```sql
' UNION SELECT table_name,NULL
FROM information_schema.tables--
```
The results revealed a relevant table:

**users_gxthht**

**Finding**

A users table was identified within the application's database.

## 8. Column Enumeration

The columns belonging to the discovered users table were then identified using database metadata.

The following query was used:
```sql
' UNION SELECT column_name,NULL
FROM information_schema.columns
WHERE table_name = 'users_gxthht'--
```
The relevant authentication-related columns identified were:
- 'username_litoxe'
- 'password_sbkfha'

## 9. Data Retrieval

The identified username and password fields were queried from the discovered users table.

The following query structure was used:

```sql
' UNION SELECT username_litoxe,password_sbkfha
FROM users_gxthht--
```
The response revealed an administrator account and its corresponding authentication information.

**Sensitive authentication credentials have intentionally been omitted from this public report.**

## 10. Authentication and Impact Validation

The recovered authentication information was used to test the application's login functionality.

Authentication to the administrator account was successful.

This demonstrated that the SQL Injection vulnerability could be used not only to read database information, but also to obtain authentication credentials and gain unauthorized access to a privileged account.

**Security Impact**
Successful exploitation could allow an attacker to:

- Read information from the application's database.
- Enumerate database structure.
- Access authentication-related information.
- Obtain user credentials.
- Compromise privileged accounts.
- Potentially access additional application functionality available to administrators.

## 11. Evidence of Successful Exploitation

The PortSwigger Web Security Academy lab was successfully completed.

The final lab-completion screen confirms that the vulnerability was successfully exploited and the lab objective was achieved.

Sensitive credentials and session information should not be included in screenshots or public documentation.

Final Lab Status

SOLVED

## 12. Vulnerability Classification

Vulnerability: SQL Injection
Injection Type: UNION-based SQL Injection
Affected Functionality: Product-category filtering
Affected Parameter: 'category'
Database: PostgreSQL
Severity: High

## 13. Root Cause

The root cause was insufficient protection of user-controlled input before it was incorporated into the application's SQL query.

The application did not adequately separate user input from SQL syntax, allowing an attacker to manipulate the structure of the underlying SQL query.

## 14. Remediation


The application should implement the following security controls.

**Parameterized Queries**

Use prepared statements and parameterized queries instead of dynamically constructing SQL queries using user input.

**Input Validation**

Validate user-supplied parameters against expected values and use allowlists where appropriate.

**Least Privilege**

The application's database account should have only the permissions required for normal application functionality.

**Secure Password Storage**

Passwords should never be stored in plaintext. Passwords should be stored using a strong, modern password-hashing algorithm.

**Error Handling**

Detailed database errors should not be exposed to users because they can reveal information about the application's internal database structure.

**Security Testing**

Regular security testing should be performed to identify and remediate SQL Injection and other application vulnerabilities.

## 15. Conclusion

The assessment successfully identified and exploited a UNION-based SQL Injection vulnerability in the application's product-category filtering functionality.

The investigation progressed from initial reconnaissance to column enumeration, UNION-based SQL Injection, database fingerprinting, table enumeration, column enumeration, and data retrieval.

The extracted authentication information allowed successful administrator authentication, demonstrating the significant security impact of the vulnerability.

The issue can be effectively mitigated through parameterized SQL queries, proper input handling, least-privilege database permissions, secure password storage, and appropriate error handling.

Lab Status: SOLVED

### Evidence



<img width="1882" height="697" alt="Screenshot 2026-09-01 083706" src="https://github.com/user-attachments/assets/7a01f059-b6f2-48e6-937b-38678e8e674a" />






