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

Knowing that the target system uses an Oracle database, our attack will use the payload:
```
SELECT banner FROM v$version
```

First, before blindly firing a payload, we should consider the injection context. We know from the description that the vulnerable parameter is the product category filter. The problem statement also tells us that we will use a UNION attack, from which we can infer that the injection context is in a SELECT statement like the following:

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1;
```

Assuming this is the case, we can try an attack such as:

```
' UNION SELECT banner FROM v$version--
```

Before we can attempt this attack, we should consider that the UNION attack must return the same number of columns as the initial query; there is no guarantee that the initial query will return only a single column! Recall that there are two main methods for column enumeration: ORDER BY and NULL. In this walkthrough, we will use the NULL method. Recall that Oracle databases must select from a table, so simple using "UNION SELECT NULL" will cause an error. Instead, use "UNION SELECT NULL FROM dual". The built-in table "dual" is used when selecting data that might not exist in a table, such as dates/times. For our purposes, we're just using it to figure out how many columns to include in our UNION attack. 
