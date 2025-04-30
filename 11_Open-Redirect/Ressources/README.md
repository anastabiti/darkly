# 📄 02-Open-Redirect


---


## 📌 Owasp Vulnerability: 
- **CWE-601: URL Redirection to Untrusted Site ('Open Redirect')**


![CWE-601 Open Redirect Diagram](https://cwe.mitre.org/data/images/CWE-601-Diagram.png)



---
## 📖 Exploitation Process:

1. **Initial Discovery of the Redirection Functionality:**
   - Observed that the application used a redirection mechanism at: 
     `http://10.11.100.193/index.php?page=redirect&site=facebook`
   - This URL was redirecting users to: `https://www.facebook.com/42born2code/`
   - Noticed the `site` parameter was controlling the redirection target

2. **Testing the Parameter Manipulation:**
   - Hypothesized that the `site` parameter might accept other values without proper validation
   - Changed the `site` parameter from "facebook" to a malicious domain to test for open redirect vulnerability

3. **Vulnerability Confirmation:**
   - Successfully redirected to the malicious site, confirming the open redirect vulnerability
   - The application was accepting arbitrary values for the `site` parameter without validation

4. **Flag Discovery:**
   - After confirming the vulnerability, explored possible internal redirects
   - Modified the redirect to point to internal pages that might contain hidden flags
   - Successfully accessed the page containing the flag through the redirect vulnerability

---

### 🔴 Critical Security Impacts:

 Key impacts include:

- Credential theft when users are redirected to convincing clones of legitimate websites
- Damage to organizational reputation when the trusted domain is used as an attack vector

---

## 📌 Security Recommendations:

1. **URL Validation and Sanitization:**
   - Implement strict validation of redirect URLs
   - Always validate and whitelist URL inputs to ensure users cannot redirect to arbitrary or untrusted sites

---
