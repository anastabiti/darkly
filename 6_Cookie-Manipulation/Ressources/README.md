## 📌 Breach Name: **CookieAdminBreach**


---

## 📌 Vulnerability Type:
- **CWE-287: Improper Authentication**
- **CWE-565: Reliance on Cookies without Validation and Integrity Checking**
- **CWE-784: Reliance on Cookies without Validation and Integrity Checking for Session Authentication**

![CWE-565](https://cwe.mitre.org/data/images/CWE-565-Diagram.png) ![CWE-565](https://cwe.mitre.org/data/images/CWE-287-Diagram.png)


---

## 📖 Exploitation Process:

1. **Cookie Discovery:**
   - Found cookie named `I_am_admin` with value `68934a3e9455fa72420237eb05902327`
   - Identified the value as an MD5 hash

2. **Hash Analysis:**
   - Decoded the MD5 hash to reveal value: `false`
   - Determined the cookie controls admin status via boolean value

3. **Exploitation:**
   - Generated MD5 hash of `true`: `b326b5062b2f0e69046810717534cb09`
   - Modified the cookie value using browser developer tools
   - Refreshed the page to gain admin access and retrieve the flag




---

### 🔴 Critical Security Impacts:

This vulnerability allows attackers to gain unauthorized administrative access by simply modifying a client-side cookie value. Key impacts include:

- Complete administrative access 
- Unauthorized access to sensitive administrative functions and protected resources



---

## 📌 Security Recommendations:

1. **Server-Side Authentication:**
   - Never rely on client-side cookies for authentication
   - Implement secure session management

2. **Secure Cookie Configuration:**
   - Use HttpOnly, Secure, and SameSite flags
   - Implement signed cookies to prevent tampering

3. **Proper Access Control:**
   - Validate all authorization on the server side
   - Use unpredictable session tokens instead of simple values

---
