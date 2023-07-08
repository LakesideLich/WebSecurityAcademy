# Problem Statement

This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query. To solve the lab, display the database version string. 

# Solution 

Like the previous lab, we are given certain information about how to solve this lab: namely, that the target database is of a particular type (in this case, MSSQL/MySQL). Recall the method that returns the database version string is the same in Microsoft as it is for MySQL.
```
SELECT @@version
```

Therefore, this is the payload we wish to inject to the SQL query. We know that the vulnerable element of the application is the product category filter. Like in the previous lab, we will have to enumerate the columns returned by the filter. Since we used the NULL method in the previous lab, we'll use ORDER BY in this example. 

Recall the following SQL query from previous labs and examples:
```
SELECT * FROM products WHERE category = '%' and released = 1
````
Our injectin point will be where the percent sign % is in this query. 


Start the lab and select any category of product. In this case, I have selected the "Gifts" category. In Burp Repeater, add a single quote to the end of the query and send it. Observe that the application produces an error in response. Add a double dash sequence -- after the query and send it again, once more inducing an error. Change the double dash comment sequence to a pound sign/hashtag # and send the request again, resulting in "200 OK". 

Now, for column enumeration. Between the single quote ' and comment # add an order by statement so that the parameter value appears like this:
```
Gifts'+ORDER+BY+1+#
```
And increment the integer at the end of the query until inducing an error. Once you see an error, you know that the number of columns is one less than the integer at which the error was induced. In this lab, an error is induced by ORDER BY 3, so we know that the number of columns is 2. 

We can now construct the UNION attack, knowing our payload and number of columns, we might try something like this:
```
UNION SELECT @@version, NULL #
```
So we'll put that into the URL in Burp Repeater and send the request. Examining the data returned in the response, we see the database version. If you look at the lab in the browser, you'll see the banner indicating it's solved. 

![Screen Shot 2023-07-08 at 7 20 22 PM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/aa3b8efc-42ad-4f84-95ac-b9d0a3864294)
