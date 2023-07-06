![image](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/1e2551b1-d381-4248-b9df-11ca5d47ff7b)# Web Security Academy

This repository is where I upload write-ups for solved labs from PortSwigger's Web Security Academy!

PortSwigger maintains a library of content and different labs for aspiring web security experts to learn, practice, demonstrate, and test their skills. It's the perfect starting point for anyone who is new to web security or wants to learn more. The labs and their corresponding learning material cover different types of web application security vulnerabilities, such as SQL injection (SQLi), cross-site scripting (XSS), XML external entity injection (XXE), and many, many more. Make an account on [PortSwigger's site](https://portswigger.net/web-security/learning-path) and download your preferred version of Burp Suite, Community (free) or Professional (paid subscription) to get started! Even if you're completely new to web security, this is one of the most valuable free learning resources at your disposal. 

![image](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/f6ced643-c924-40ae-a270-212f44f83236)

# Getting Started

To get started on your journey into web security, you should download and install a version of Burp Suite web proxy. There are two main versions of Burp Suite available, [Community edition](https://portswigger.net/burp/communitydownload) and [Professional edition](https://portswigger.net/burp/pro). The Community edition is free with limited features, and the Professional version features the full suite of tools with a subscription. As of the writing of this document, the price for Burp Suite Pro is $449 USD. Keep in mind that while most labs can be completed with only the Community edition, some require Professional. I am currently using Community, so I will not cover the labs that require Professional access. If you intend to use an external browser like Firefox or Chrome, you will have to add PortSwigger's certificate to your trusted certificates before continuing. This is a fairly quick process, and you can find the documentation [here](https://portswigger.net/burp/documentation/desktop/external-browser-config/certificate). Alternatively, you can use Burp's built-in browser, Chromium. Personally, for my setup, I use the Firefox browser with the FoxyProxy extension. 

# Quick Tips 

1. When working through the labs, you may find it useful to manage your scope settings. Rather than adding / removing the different subdomains for different labs, here's a quick scope setting configuration so you can move between labs quickly without having to mess around with your scope settings:
   
![Screen Shot 2023-07-06 at 1 03 42 AM](https://github.com/tatruesdell/WebSecurityAcademy/assets/43506369/ee9475bf-3df4-4583-b021-5a864c9b05b7)

Note the academyLabHeader file is excluded as well to keep it from showing in the HTTP history. 
