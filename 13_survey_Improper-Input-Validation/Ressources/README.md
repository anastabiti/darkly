## 📌 Breach Name: **Improper-Input-Validation**



## 📌 Vulnerability Type:
- **CWE-20: Improper Input Validation**
- **CWE-345: Insufficient Verification of Data Authenticity**


![alt text](https://cwe.mitre.org/data/images/CWE-20-Diagram.png)
---

## 📖 Exploitation Process:

1. **Discovery of the Vulnerable Form:**
   - Located a survey form with a dropdown selection limited to values 1-10
   - Noticed the form submits automatically when a value is selected (due to onchange event handler)

2. **Source Code Analysis:**
   ```html
   <select name="valeur" onchange="javascript:this.form.submit();">
       <option value="1">1</option>
       <option value="2">2</option>
       <option value="3">3</option>
       <option value="4">4</option>
       <option value="5">5</option>
       <option value="6">6</option>
       <option value="7">7</option>
       <option value="8">8</option>
       <option value="9">9</option>
       <option value="10">10</option>
   </select>
   ```
3. **Vulnerability Exploitation:**
   - Used browser developer tools (Inspect Element) to modify the HTML
   - Added a new option with a higher value outside the intended range:
     ```html
     <option value="42">42</option>
     ```
   - Alternatively, intercepted the request with a proxy tool and modified the "valeur" parameter
   - Submitted the form with the modified value

4. **Result:**
   - The application processed the unexpected value
   - Server-side logic triggered a hidden condition revealing the flag
   - Flag was displayed in the response

---




### 🔴 Critical Security Impacts:

Key impacts include:

- Unauthorized Access or Data Exposure with Malicious Input Manipulation
-  Bypassing Application Logic
- Access to hidden functionality or privileged features not intended for normal users



---
## 📌 Security Recommendations:

1. **Implement Server-Side Validation:**
   - Always validate input on the server-side, regardless of client-side controls
   - Ensure the submitted values match an allowed whitelist (1-10 in this case)
   - Reject any requests with out-of-range values with appropriate error messages
   - Implement type checking to prevent parameter manipulation

2. **Sanitize User Input:**
   - Explicitly validate and sanitize all input parameters before processing
   - Reject any values that don't match expected patterns or ranges
   - Use parameterized inputs where applicable to avoid direct input processing
