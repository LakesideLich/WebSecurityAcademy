# Problem Statement

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables. The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users. To solve the lab, log in as the administrator user. 

# Solution

Recall the methods for listing the database contents of non-Oracle databases:

```
SELECT * FROM information_schema.tables

SELECT * FROM information_schema.columns WHERE table_name = 'YOUR-TABLE-NAME'
```

So these are the payloads we want to incorporate into our attack. The first payload will be used to find the names of tables within the database. The second payload will incorporate those table names to identify useful data that we want to extract, in this case, the password for the user called 'administrator'. 

Start the lab while proxying requests through Burp Suite. Click on a category of item such as "Gifts", and identify the request in Burp Suite's proxy history. Send the Request to Burp Repeater. In the Repeater tab, add a single quote to the query, and send the request. Observe the induced error and add a double-dash comment sequence to the end of the query, then send the request again, noting the "200 OK" response. 

Enumerate columns using the ORDER BY or NULL method. You should find that there are two columns returned by the table. So now, construct a query like the following:

```
Gifts' UNION SELECT table_name,NULL from information_schema.tables--
```

![Screen Shot 2023-07-08 at 7 50 55 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/0d1c8020-55cf-4c51-ae1a-1449c2260195)

The database should respond with a number of different table names, in this case we have identified the users table as "users_wfgyrc". <b>Note: the table name will likely be different in your lab instance.</b> Now, to identify the column names, we will construct a query like this one:

```
Gifts' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name = 'users_wfgyrc'--
```

The responses below show the column names returned by the database. 

![Screen Shot 2023-07-08 at 7 56 23 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/9265a521-0cf9-4f8d-9b2e-d54d52cf050e)


![Screen Shot 2023-07-08 at 7 58 17 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/2bc42071-f56d-4bb1-8f0c-4b32e451f9f6)

With these column names, we can construct a final query like this one:

```
Gifts' UNION SELECT username_wdcgsi,password_ihurpp FROM users_wfgyrc--
```

Which should show us the usernames and passwords, allowing us to login as the administrator. After sending the request and examining the response, we find the administrator's password. 

![Screen Shot 2023-07-08 at 8 03 26 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/b6de4825-faa0-4f25-b728-08a1823b8254)

We copy the password and use to log in as the administrator, solving the lab. 
