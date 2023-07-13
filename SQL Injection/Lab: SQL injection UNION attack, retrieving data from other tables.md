# Problem Statement 

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs. The database contains a different table called users, with columns called username and password. To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user. 

# Solution

Knowing the name of the table is "users" with columns "username" and "password", the payload we want to inject will look something like this:

```
UNION SELECT password FROM users WHERE username = 'administrator'
```

Select a category of product, such as "Gifts", while proxying traffic through Burp Suite. In Burp Suite's proxy history, identify the request with /filter?category=Gifts and send the request to Burp Suite's Repeater tool. In the Repeater tool, add a single quote to the URL and send it. Add the double-dash comment sequence and send the request, observing the response. Perform column enumeration using the ORDER BY or NULL method(s). Using the above SQL query like a prototype, develop a UNION attack such as the following:

```
Gifts' UNION SELECT password,NULL FROM users WHERE username = 'administrator'--
```

Send the request and observe the response. Note the password present in the response. Copy the password and use it to log in as the administrator, solving the lab. 

![Screen Shot 2023-07-13 at 12 31 48 AM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/2ab66fb9-dec4-4256-a1e1-f9710122af37)

