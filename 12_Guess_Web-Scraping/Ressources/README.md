## 📌  **HiddenDirectoryFlagHunt**


## 📌 Owasp Vulnerability: 
- **CWE-552: Files or Directories Accessible to External Parties**
- **CWE-548: Exposure of Information Through Directory Listing**


---



## 📖 Exploitation Process:



### Tools Used:
- **Browser**: To manually access and analyze the `robots.txt` file.
- **OWASP ZAP Proxy**: Assisted in automated exploration of  robots.txt and hidden directories.


---


1. **Initial Reconnaissance:**
   - Checked the robots.txt file at `http://10.11.100.193/robots.txt`
   - Found the following content:
     ```
     User-agent: *
     Disallow: /whatever
     Disallow: /.hidden
     ```
   - Noted two disallowed directories: `/whatever` and `/.hidden`

2. **Hidden Directory Investigation:**
   - Accessed `http://10.11.100.193/.hidden`
   - Discovered a large directory structure with cryptic folder names:
     ```
     Index of /.hidden/
     ../
     amcbevgondgcrloowluziypjdh/
     bnqupesbgvhbcwqhcuynjolwkm/
     ceicqljdddshxvnvdqzzjgddht/
     ...
     zzfzjvjsupgzinctxeqtzzdzll/
     README
     ```
   - Manual browsing revealed that each directory contained more subdirectories and README files
   - Determined that manual exploration would be impractical due to the vast number of nested directories

3. **Automating the Directory Traversal:**
   - Used wget to recursively download the entire directory structure:
     ```bash
     wget -erobots=off --no-parent --recursive --level=inf http://10.11.100.193/.hidden/
     ```
     - `-erobots=off`: Ignore robots.txt restrictions
     - `--no-parent`: Don't ascend to parent directory
     - `--recursive`: Follow and download linked pages
     - `--level=inf`: No limit on recursion depth (follow all nested directories)
   - This command created a local copy of the entire `.hidden` directory structure with all files

4. **Systematic Flag Search:**
   - Once the download was complete, searched all README files for the word "flag":
     ```bash
     grep -R --include='README' 'flag' .
     ```
     - `grep -R`: Search recursively through directories
     - `--include='README'`: Only search in files that match the pattern README*
     - `'flag'`: The text pattern to search for
     - `.`: Start the search from the current directory

5. **Flag Discovery:**
   - The command returned:
   ```
    ./.hidden/whtccjokayshttvxycsvykxcfm/igeemtxnvexvxezqwntmzjltkt/lmpanswobhwcozdqixbowvbrhw/README:Hey,
     here is your flag :       d5eec3ec36cf80dce44a896f961c1831a05526ec215693c8f2c39543497d4466
   ```
---


### 🔴 Critical Security Impacts:

This Hidden Directory vulnerability allows attackers to discover and access sensitive content through exposed directory structures. Key impacts include:

- Unauthorized access to confidential data stored in supposedly "hidden" directories
- Exposure of internal application structure that may reveal additional attack vectors
- Information leakage that could facilitate more targeted attacks against the system


---
## 📌 Security Recommendations:

1. **Proper Access Controls:**
   - Implement authentication for sensitive directories
   - Use proper authorization mechanisms instead of hiding resources

2. **Web Server Configuration:**
   - Configure the web server to prevent directory listing
   - Implement proper 403 Forbidden responses for restricted areas

3. **robots.txt Best Practices:**
   - Don't list sensitive directories in robots.txt
   - Remember that robots.txt is publicly accessible and merely a suggestion for bots

