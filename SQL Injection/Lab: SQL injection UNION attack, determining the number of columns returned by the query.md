# Problem Statement

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack. To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values. 

# Solution

Select a category of product such as "Gifts" while proxying traffic through Burp Suite. In Burp Suite's proxy history, identify the request with /filter?category=Gifts in the URL and send it to Burp Suite's Repeater tool. In Repeater, add a single quote to the end of the query and observe the error response:

```
Gifts' 
```

Add a double dash comment sequence to the end of the query and observe the "200 OK" response:

```
Gifts'--
```

Between the single quote and the comment, add "UNION SELECT NULL":

```
Gifts' UNION SELECT NULL--
```

Send the request and observe the 500 error response. Add more NULL columns until a 200 OK response is received:

```
Gifts' UNION SELECT NULL,NULL,NULL--
```
