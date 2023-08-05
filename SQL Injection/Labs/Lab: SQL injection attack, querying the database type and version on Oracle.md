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

Before we can attempt this attack, we should consider that the UNION attack must return the same number of columns as the initial query; there is no guarantee that the initial query will return only a single column! Recall that there are two main methods for column enumeration: ORDER BY and NULL. In this walkthrough, we will use the NULL method. Recall that Oracle databases must select from a table, so simply using "UNION SELECT NULL" will cause an error. Instead, use "UNION SELECT NULL FROM dual". The built-in table "dual" is used when selecting data that might not exist in a table, such as dates/times. For our purposes, we're just using it to figure out how many columns to include in our UNION attack. 

To start, just select one of the categories and observe the application's response:

![Screen Shot 2023-07-07 at 11 16 40 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/095c5967-ac4c-4726-b7ee-c442c2a2a723)

In Burp Suite's proxy history, select the request and send it to Burp Suite's Repeater tool:

![Screen Shot 2023-07-07 at 11 20 43 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/f7c685db-4acd-4ccd-9c0e-ba3750e529e3)

In the Repeater tool, send the request once more. Add a single quote to the query, and observe that an error occurs. 

![Screen Shot 2023-07-07 at 11 25 07 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/af2b99a0-e8cd-440c-8ed8-3ad9215a7cd3)

<redact></redact>
Note: you may want to select "URL-encode as you type" for this step. 
Add a UNION query in front of the single quote so that the query looks like the following:

```
/filter?category=Gifts'+UNION+SELECT+NULL+FROM+dual--
```

Send the request and observe that another error has occurred. Add another NULL column to the query so that the query looks like this, and send the request:

```
/filter?category=Gifts'+UNION+SELECT+NULL,NULL+FROM+dual--
```

Notice that this query causes the same response as the initial "Gifts" query. This means we've identified the number of columns as 2, and satisfied SQL syntax. From this point, change the "dual" table to "v$version" and replace the first NULL column with "banner", so that the query looks like this:

```
/filter?category=Gifts'+UNION+SELECT+banner,NULL+FROM+v$version--
```

Send this request, and see if you can find the version details in the application's response. You've solved the lab!
