# Problem Statement

This lab contains a SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned. The database contains a different table called users, with columns called username and password. To solve the lab, find a way to leak the password for the administrator user, then log in to their account. 

# Solution

Start the lab while proxying traffic through Burp Suite. Refresh the home page and identify a request with the TrackingId cookie. Send the request to Burp Suite's Repeater tool. In the repeater tool, add a single quote and double dash comment to the end of the TrackingId cookie so that the cookie looks like this:

```
Cookie: TrackingId=aBc...xYz'--
```

Send the request and note the "200 OK" response. At this point, you may be tempted to attempt a query like the following:

```
xYz' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) > 'm'--
```

However, you will see that the database limits the length of queries, and this will result in a syntax error. Alternatively, try a query like this:

```
xYz'; SELECT CAST((SELECT password FROM users WHERE username='administrator') AS int)--
```

However, a similar error will indicate this query is also too long. Try removing the WHERE clause:

```
xYz'; SELECT CAST((SELECT password FROM users) AS int)--
```

And you will receive a different error indicating that too many rows were returned. Once again, modify the query like so, removing the space between the semi-colon and the next SELECT statement:

```
xYz';SELECT CAST((SELECT password FROM users LIMIT 1) AS int)--
```

After sending this request, you should receive an error like the following:

![Screen Shot 2023-07-22 at 8 38 41 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/b9a9ee3f-4843-4a19-9cd0-98dfec5a8cbd)

Note the string contained in the error. Copy the string into the password field of the login page and log in as the administrator. If the password does not work, you may have extracted the wrong password: send the request again until a different string is retrieved, and try again. 
