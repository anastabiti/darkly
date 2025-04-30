## 📌 Breach Name: **PathTraversalBreach**

---


## 📌 Vulnerability Type:
- **CWE-22: Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')**
- **CWE-73: External Control of File Name or Path**

![alt text](https://cwe.mitre.org/data/images/CWE-22-Diagram.png)
---
## 📖 Technical Details:

The application accepts a `page` parameter in the URL that likely uses PHP's `include()` or similar functionality to load content. The vulnerable endpoint is: http://h.h.h.h/?page=[page-name]

When this parameter is manipulated with path traversal sequences, the application follows these sequences and accesses files outside the web root: http://h.h.h.h/?page=../../../../../../../../../../../etc/passwd


The multiple `../` sequences navigate up the directory tree from the web root until reaching the system root, then access the `/etc/passwd` file, which contains sensitive system user information and, in this case, reveals a flag.

---

## Code example
      ```<?PHP 
         $file = $_GET["file"];
         $handle = fopen($file, 'r');
         $poem = fread($handle, 1);
         ...
      ?>

### 🛠️ Exploit Execution:

1. Identify a parameter that appears to load content dynamically (in this case, the `page` parameter)
2. Test for path traversal by injecting `../` sequences to navigate up directories
3. Attempt to access common sensitive files like `/etc/passwd`
4. Analyze the response to confirm successful exploitation

Vulnerable URL: http://h.h.h.h/?page=../../../../../../../../../../../etc/passwd



### 🔴 Critical Security Impacts:

- **Unauthorized File System Access**: Attackers can read any file the web server has permissions to access, including sensitive system files
  
- **Information Disclosure**: Exposure of system configuration details, user accounts, and potentially credentials through files like `/etc/passwd`

---

## 📌 Security Recommendations:

1. **Input Validation and Sanitization:**
   - Implement strict server-side validation for the `page` parameter
   - Remove or encode path traversal sequences (`../`) from user input
   - Use a whitelist approach to only allow specific predefined values

2. **Implement Proper Access Controls:**
   - Run the web application with minimal required privileges
   - Set proper file system permissions to prevent access to sensitive files
   - Utilize a chroot jail or container to isolate the application