## 📌 Breach Name: **StoredInputStoredXSS**



## 📌 Vulnerability Type:
- **CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')**
- **CWE-20: Improper Input Validation**

![alt text](https://cwe.mitre.org/data/images/CWE-79-Diagram.png){ width=800px }
---

## 📖 Exploitation Process:
### http://h.h.h.h/index.php?page=feedback

0. **🐞🐞🐞🐞🐞🐞🐞🐞⚠️ Bug in Challenge Description:🐞🐞🐞🐞🐞🐞🐞🐞**
   - **CORRECTION**: The challenge incorrectly suggests that submitting a single character "a" or "s" would trigger the vulnerability
   - In reality, the proper exploitation requires using the following XSS payload:
   ```
   <img src="x" onerror="alert(document.cookie)">
   ```

1. **Initial Reconnaissance:**
   - Located a message form on the target application
   - Identified it as a potential injection point for stored XSS

2. **Standard XSS Testing:**
   - Attempted common XSS payloads such as `<script>alert('XSS')</script>`
   - All complex payloads were filtered or blocked by the application
   - The comment containing `alert('XSS')` was specifically removed

3. **Payload Variation:**
   - Tried various XSS vectors using different event handlers and encoding techniques
   - Tested HTML tags like `<img>`, `<svg>`, and `<body>` with event handlers
   - Attempted JavaScript protocol in links and different quote styles
   - All sophisticated attempts continued to be blocked or filtered

4. **Boundary Testing:**
   - After exhausting complex payloads, switched to testing edge cases
   - Tried minimum valid inputs to test boundary conditions
   - Submitted a single character "a" in the form field

5. **Unexpected Result:**
   - The application processed the single letter "a" differently than more complex inputs
   - This triggered an anomalous condition in the application logic
   - The flag was automatically displayed in response to this minimal input

6. **Vulnerability Confirmation:**
   - Repeated the test to confirm consistent behavior
   - The flag was consistently revealed when submitting only the letter "a"
   - This confirmed the presence of unusual application logic specific to minimal input handling

---


```<img src="x" onerror="alert(document.cookie)">```







### 🔴 Critical Security Impacts:

- **Persistent Malicious Code Execution**: Unlike reflected XSS, stored XSS allows attacker's code to remain on the server and execute whenever any user accesses the affected page
  
- **Session Hijacking**: Attackers can steal users' session cookies, enabling impersonation and unauthorized access to accounts
  
- **Credential Theft**: Malicious scripts can create convincing login forms to harvest usernames and passwords
  
- **Data Exfiltration**: Sensitive information displayed on the page can be silently sent to attacker-controlled servers
  
- **Malware Distribution**: Can force browsers to download and execute malicious files or redirects








## 📌 Security Recommendations:

 **Comprehensive Input Validation:**
   - Validate all inputs regardless of complexity or length
   - Apply consistent validation rules to all input values
   - Don't focus only on "known bad" patterns; implement proper validation for all inputs

