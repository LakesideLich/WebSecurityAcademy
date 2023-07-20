# Problem Statement

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned, and no error messages are displayed. But the application includes a "Welcome back" message in the page if the query returns any rows. The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user. To solve the lab, log in as the administrator user. 

# Solution

While proxying traffic through Burp, start the lab. Refresh the page, and send the request with the TrackingId cookie to Burp Repeater. Send the request and observe the 200 OK response. Search for "Welcome back!" in the response. The welcome back message is how we will exploit the blind SQL vulnerability. 

![Screen Shot 2023-07-19 at 6 22 34 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/c28ae4e4-6737-48ab-9458-eba3aa8c3ca1)

Try adding a single quote to the end of the TrackingId cookie, and send the request again. Observe that the welcome message is not included in this response. Add a double dash comment sequence after the single quote and send the request again. Observe that the welcome back message has returned, indicating the query returned rows. Between the single quote and double dash comment is where we will structure our queries. 

We will structure the following sequence of queries like this:

```
TrackingId=AbC...xYz' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) > 'm'--
```

This format of query will be used to enumerate the administrator user's password one character at a time by incrementing the start of the string in the substring function. Don't worry if that doesn't make sense right away, just follow along! 

Note: the parameters of the SUBSTRING function are as follows: SUBSTRING(string, start, length). 

![Screen Shot 2023-07-19 at 6 44 40 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/28e0d950-fad9-4bb4-b48b-0b1e19e20b2f)

The above request tests whether the first character of the administrator's password is greater than 'm'. In SQL, digits are less than lowercase letters, and lowercase letters are less than uppercase letters. For this exercise, we will begin with lowercase 'm' when enumerating characters. In my own lab instance, the admin's password begins with a character greater than 'm', but this may not be the case in your own lab instance, so you will have to mimic this methodology, but it is unlikely you will arrive at the same password. 

The next character I will try is 'z'. The absence of the welcome message tells me the first character of the admin's password is between 'm' and 'z', so I'll split the difference and test if the first character is greater than 's'. This test comes back negative, so the first character of the password resides on the interval ('m', 's']. Once again, we will split the difference and test if the first character is greater than 'p'. This request results in a response including the welcome message, i.e. the truth of the condition, so we can conclude the first character of the admin's password is 'q' or 'r'. At this point, it would be a good idea to change the greater than sign > to an equal sign. Addiitionally, change 'p' to 'q' and send the request. If the response includes the welcome message, then the first character of the admin's password is 'q'. If not, then it is 'r'. In my instance, the first character is 'q'. 

![Screen Shot 2023-07-19 at 7 11 26 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/9b6ef2ab-d351-4bcb-90d5-b746f9be199f)

From this point, alter the query to look like this:

```
TrackingId=AbC...xYz' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'), 2, 1) > 'm'--
```

Notice that it is like the previous query we started with, except that the start of the string is positioned at 2 instead of 1. We will repeat the previous process to enumerate each character of the admin's password. In this case, we find that the second character is greater than 'm', less than or equal to 'z', less than or equal to 'p', and greater than 'o'. If the second character is greater than 'o' and less than or equal to 'p', then the second character must be 'p'. 

![Screen Shot 2023-07-19 at 7 12 14 PM](https://github.com/LakesideLich/WebSecurityAcademy/assets/43506369/0155f51c-8192-4d14-bfcc-f99ea155e27d)

For the third character, again test if it is greater than 'm'. I find that it is less than or equal to 'm', so I test if it is greater than 'a'. It is less than or equal to 'a', so I test if it is greater than or equal to '0'. This test returns a positive, so I test if the third character is greater than '9', which does not include the welcome message (negative). These tests indicate the third character resides on the interval ('0', 'a'), so it is certainly a digit greater than 0. Further queries indicate that the third character is greater than '2', but not '3', so the third character is '3'. 

Repeat this process until the administrator's password is fully enumerated. It can be tedious and time-consuming, but this is a valuable example for how blind SQL injection really works. Once we reach the 21st character, a series of tests returns a response without the welcome message when querying if the 21st character is greater than or equal to '0'. This indicates that the password is likely complete at 20 characters of length. After copying and pasting the password, we log in as the administrator, solving the lab. 

In my lab instance, the password turned out to be 'qp3vz8c9mdgeiytn80rd'. 

Note: if you want to check if the password is correct before logging in, try this query:

```
TrackingId = 'AbC...xYz' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 20) = '[your password here]'--
```

Variations of the above query can also be useful if you want to find if / where you might have made a mistake. Happy Hacking!
