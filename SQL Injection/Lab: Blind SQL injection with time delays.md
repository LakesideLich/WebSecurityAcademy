# Problem Statement

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information. To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay. 

# Solution

Recall the sleep functions for different database versions:

```
Oracle:     dbms_pipe.receive_message(('a'),10)
Microsoft:  WAITFOR DELAY '0:0:10'
PostgreSQL: SELECT pg_sleep(10)
MySQL:      SELECT sleep(10)
```

While proxying traffic through Burp Suite, start the lab. In the proxy history, identify a request with the TrackingId cookie and send the request to Burp Suite's repeater tool. Add a single quote and a double-dash comment sequence to the end of the TrackingId cookie. Between the single quote and the comment, add a semi-colon. Try each of the above methods to induce a 10-second sleep on the server. In this case, the server uses PostgreSQL, so the query that causes sleep looks like this:

```
xYz'; SELECT pg_sleep(10)--
```

After sending the request you should notice a 10-second delay in the response. You may have to refresh the home page to make the "solved" banner appear. 
