## 📌 Breach Name: **Media Parameter Reflected XSS**

---


## 📌 Vulnerability Type:
- **CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')**
- **CWE-20: Improper Input Validation**
- **CWE-116: Improper Encoding or Escaping of Output**

---

## 📖 Exploitation Process:

1. **Discovery of the Vulnerable Parameter:**
   - Identified a page that accepts a `src` parameter: `http://10.11.100.193/?page=media&src=nsa`
   - This parameter appeared to control what media content is displayed on the page

2. **Source Code Analysis:**
   - Examined the page source code to understand how the `src` parameter is handled
   - Found that user input from the `src` parameter is passed to an `<object>` tag:
     ```html
     <object data="http://10.11.100.193/images/nsa_prism.jpg"></object>
     ```
   - The application constructs this tag dynamically based on user input without proper validation

3. **Data URI Injection:**
   - Leveraged the HTML `<object>` tag's ability to load content from various sources
   - Used a data URI with Base64-encoded content to inject JavaScript:
     ```
     data:text/html;base64,PHNjcmlwdD5hbGVydCgnSGknKTwvc2NyaXB0Pg==
     ```
   - The Base64 string decodes to: `<script>alert('Hi')</script>`

4. **Payload Construction:**
   - Final malicious URL:
     ```
     http://10.11.100.193/?page=media&src=data:text/html;base64,PHNjcmlwdD5hbGVydCgnSGknKTwvc2NyaXB0Pg==
     ```

---

## 📌 How This Exploit Works:

1. **Object Tag Vulnerability:**
   - The `<object>` HTML tag is designed to embed external resources in a webpage
   - It accepts various types of content through its `data` attribute
   - When the application directly inserts user input into this attribute without validation, it creates an XSS vector

2. **Data URI Scheme Explained:**
   - Data URIs are a URI scheme that allow including small data items inline in a document
   - Format: `data:[<media-type>][;base64],<data>`
   - Components breakdown:
     * `data:` - The URI scheme identifier
     * `<media-type>` - MIME type of the content (e.g., text/html, image/png)
     * `;base64` - Optional flag indicating the data is Base64 encoded
     * `,` - Separator between the header and the data
     * `<data>` - The actual content, either in plain text or Base64 encoded

3. **Media Type Significance:**
   - The media type determines how the browser interprets the embedded content:
     * `text/html` - Renders as HTML and executes any embedded scripts
     * `image/jpeg` - Displays as an image
     * `application/javascript` - Treated as JavaScript code
   - In our exploit, using `text/html` causes the browser to parse and render the decoded content as HTML

4. **Base64 Encoding Process:**
   - Original payload: `<script>alert('Hi')</script>`
   - Convert to Base64: `PHNjcmlwdD5hbGVydCgnSGknKTwvc2NyaXB0Pg==`
   - Benefits of Base64 encoding:
     * Avoids URL encoding issues with special characters
     * Bypasses certain security filters
     * Makes the payload less obvious to casual inspection

## 📌 Technical Details:

### Data URI Scheme Examples
The exploit leverages the `data:` URI scheme which allows embedding small data items inline as if they were external resources.

---

### 🔴 Critical Security Impacts:

This reflected XSS vulnerability allows attackers to execute arbitrary JavaScript in users' browsers through the unvalidated `src` parameter. Key impacts include:

- Session hijacking through cookie theft
- Perform any action within the application that the user can perform.
- Modify any information that the user is able to modify.

---

## 📌 Security Recommendations:

1. **Input Validation:**
   - Implement strict validation for the `src` parameter
   - Whitelist allowed resources and media types
   - Reject requests with suspicious patterns like `data:` URIs

2. **Output Encoding:**
   - Apply context-appropriate encoding for all user-controlled data
   - HTML-encode special characters before inserting them into HTML elements
   - Use Content Security Policy (CSP) headers to restrict script sources

3. **Resource Restriction:**
   - Limit the `src` parameter to predefined paths or resources
   - Validate that requested resources exist in allowed directories
   - Consider implementing a resource mapping system instead of direct path references

