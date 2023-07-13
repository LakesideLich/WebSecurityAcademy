# Problem Statement 

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query. You can do this using a technique you learned in a previous lab. The next step is to identify a column that is compatible with string data. The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional row containing the value provided. This technique helps you determine which columns are compatible with string data. 

# Solution

While proxying traffic through Burp Suite, select a category of product, such as "Gifts". Send the request with /filter?category=Gifts in the URL to the Repeater tool. With the Repeater tool, send the request and observe the response. Add a single quote to the URL and send the request again. Add a double-dash comment sequence and send the request again. Using the ORDER BY method, discern that the query returns 3 columns:

```
Gifts' ORDER BY 4--
```

The above query causes an error, while changing the number to 3 removes the error, indicating that the query returns 3 columns. Modify the query to use the UNION SELECT NULL method:

```
Gifts' UNION SELECT NULL,NULL,NULL--
```

Send the request, and observe the 200 OK response. Change the first NULL column to a string, such as 'a', and send the request. 

```
Gifts' UNION SELECT 'a',NULL,NULL--
```

Observe this request causes an error. Change the first column back to NULL, and change the second column to a string:

```
Gifts' UNION SELECT NULL,'a',NULL--
```

Send the request. Observe that this request results in 200 OK response. Modify the string to the string specified on the lab's homepage, in this case, the strings is "S0z4hk". 

```
Gifts' UNION SELECT NULL,'S0z4hk',NULL--
```

Send this request to solve the lab. 
