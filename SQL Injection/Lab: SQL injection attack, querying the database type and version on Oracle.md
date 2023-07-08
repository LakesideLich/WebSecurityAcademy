# Problem Statement

This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query. To solve the lab, display the database version string.

# Solution

Recall the different methods for displaying database version strings:

```
Oracle:         SELECT banner FROM v$version
                SELECT version FROM v$instance

Microsoft:      SELECT @@version

PostGRESQL:     SELECT version()

MySQL:          SELECT @@version
```
