## 📌 Breach Name: **RobotsAdminBreach**


---

## 📌 Vulnerability Type:
- **CWE-548: Exposure of Information Through Directory Listing**
- **CWE-328: Use of Weak Hash**
- **CWE-916: Use of Password Hash With Insufficient Computational Effort**
![CWE-548](https://cwe.mitre.org/data/images/CWE-548-Diagram.png)
---

## 📖 Exploitation Process:



### Tools Used:
- **Browser**: To manually access and analyze the `robots.txt` file.
- **OWASP ZAP Proxy**: Assisted in automated exploration of  robots.txt and hidden directories.

---
1. **Discovery through robots.txt:**
   - Accessed the robots.txt file at `http://h.h.h.h/robots.txt`
   - Found two disallowed directories:
     ```
     User-agent: *
     Disallow: /whatever
     Disallow: /.hidden
     ```

2. **Exploration of Disallowed Directories:**
   - Navigated to the `/whatever` directory
   - Found directory listing enabled, revealing the contents:
     ```
     Index of /whatever/
     ../
     htpasswd                                           29-Jun-2021 18:09                  38
     ```

3. **Credential Discovery:**
   - Accessed the htpasswd file
   - Found administrator credentials stored in an insecure format:
     ```
     root:437394baff5aa33daa618be47b75cb49
     ```
   - Identified that the password was hashed using MD5 encryption

4. **Password Cracking:**
   - Recognized the hash as MD5 format
   - Successfully cracked the MD5 hash to reveal the plaintext password: `qwerty123@`

5. **Administrator Access:**
   - Located the admin login page at `/admin`
   - Successfully authenticated using the discovered credentials:
     - Username: `root`
     - Password: `qwerty123@`
   - After login, gained access to restricted content containing the flag

---

### 🔴 Critical Security Impacts:

Key impacts include:

- Full administrative control
- Complete compromise of all data and functionality accessible to administrator accounts

## 📌 Security Recommendations:

1. **Secure robots.txt Configuration:**
   - Don't list sensitive directories in robots.txt as it serves as a roadmap for attackers
   - Avoid disclosing the existence of admin interfaces or security-related paths

2. **Secure Credential Storage:**
   - Never store passwords using weak hashing algorithms like MD5
   - Implement proper password hashing using modern algorithms (bcrypt, Argon2, etc.) with appropriate salting
   - Use strong, complex passwords that resist common cracking techniques

3. **Directory Listing:**
   - Disable directory listing on all web servers to prevent information disclosure
   - Implement proper .htaccess or equivalent configurations

4. **Access Control:**
   - Implement proper authentication mechanisms with multi-factor authentication for admin areas
   - Use IP restrictions or other additional security layers for sensitive administrative functions
   - Implement proper session management with secure cookies and timeouts

5. **Regular Security Audits:**
   - Regularly scan for misconfigured services and unauthorized access points
   - Monitor access logs for unusual patterns or brute force attempts

