
## 📌 Breach Name: **User-Agent & Referer Header Based Access Control Bypass**

---

## 📌 Vulnerability Type:
- **CWE-602: Client-Side Enforcement of Server-Side Security**
- **CWE-807: Reliance on Untrusted Inputs in a Security Decision**
- **CWE-345: Insufficient Verification of Data Authenticity**

---

## 📖 Exploit Method:
### 🛠️ Command:

```bash
curl -e "https://www.nsa.gov/" \
     -A "ft_bornToSec" \
     "http://10.11.100.193/index.php?page=b7e44c7a40c5f80139f0a50f3650fb2bd8d00b0d24667c4c2ca32c88e13b758f" \
     | grep flag
```

This reveals the hidden flag within the response body.

1️⃣  -e "https://www.nsa.gov/"
👉 This sets the Referer header in the HTTP request.

Some web apps try to limit access to certain pages based on where the request "came from".

In this challenge, the source code hinted:

<!--You must come from : "https://www.nsa.gov/".-->
So, this flag bypasses that weak check by faking the referer.

2️⃣  -A "ft_bornToSec"
👉 This sets the User-Agent header.

In the source code:
<!--Let's use this browser : "ft_bornToSec". It will help you a lot.-->

###   🛠️ Browser Method (Using Tamper Dev):
* Intercept the HTTP request to the target page using the Tamper Dev extension.

* Modify the Referer header to https://www.nsa.gov/.

* Modify the User-Agent header to ft_bornToSec.

* Forward the modified request to the server.

### Result:
Bypassing access control and revealing the hidden flag.

### 🔴 Critical Security Impacts:

This User-Agent & Referer Header Based Access Control Bypass vulnerability allows attackers to gain unauthorized access to protected resources by simply modifying HTTP request headers. Key impacts include:

- Complete bypass of access controls with minimal technical effort using standard HTTP tools
- Unauthorized access to sensitive or restricted content intended only for specific users or systems


## 📌 Security Recommendations:

1. **Avoid Header-Based Access Controls:**
   - Do not rely on Referer or User-Agent headers for access control
   - These headers can be easily spoofed by an attacker using tools like curl, Burp Suite, or browser extensions
   - Headers should only be used for informational purposes, not security decisions

2. **Implement Proper Authentication:**
   - Use proper authentication and authorization mechanisms (e.g., session tokens, API keys, OAuth, etc.)
   - Verify user identity through secure, tamper-proof methods
   - Implement multi-factor authentication for sensitive resources

3. **Secure Coding Practices:**
   - Avoid exposing hidden routes via source comments — treat client-side code as fully visible
   - Remove debugging comments before production deployment
   - Conduct regular code reviews to identify security weaknesses

4. **Server-Side Validation:**
   - Implement server-side access controls that check for valid, authenticated, and authorized sessions
   - All security decisions must be made on the server, never on the client
   - Apply the principle of least privilege to all resources