---
title: "Secure Your Web Apps"
date: 2022-06-02T13:00:55+05:30
draft: false
author: "Ranjan"
---

This is my attempt to understand the basic security flaws a web developer might encounter while developing a web application.

This blog is just my takeaways from the [Hack the Box](https://app.hackthebox.com/home)'s *Introduction to Web Applications* module. Please refer [OWASP Top Ten](https://owasp.org/www-project-top-ten/) for the detailed list.

The top 20 most common mistakes web developers make that cause vulnerabilities are:

1. 	Permitting Invalid Data to Enter the Database
2. 	Focusing on the System as a Whole
3. 	Establishing Personally Developed Security Methods
4. 	Treating Security to be Your Last Step
5. 	Developing Plain Text Password Storage
6. 	Creating Weak Passwords
7. 	Storing Unencrypted Data in the Database
8. 	Depending Excessively on the Client Side
9. 	Being Too Optimistic
10. Permitting Variables via the URL Path Name
11. Trusting third-party code
12. Hard-coding backdoor accounts
13. Unverified SQL injections
14. Remote file inclusions
15. Insecure data handling
16. Failing to encrypt data properly
17. Not using a secure cryptographic system
18. Ignoring layer 8
19. Review user actions
20. Web Application Firewall misconfigurations

These lead to our [OWASP Top Ten](https://owasp.org/www-project-top-ten/):

1. 	Injection
2. 	Broken Authentication
3. 	Sensitive Data Exposure
4. 	XML External Entities (XXE)
5. 	Broken Access Control
6. 	Security Misconfiguration
7. 	Cross-Site Scripting (XSS)
8. 	Insecure Deserialization
9. 	Using Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring

## Front end vulnerabilities
--------------------------------------------------------------------------------------------------------------------------------
1. #### Sensitive Data Exposure
    - Sensitive data like credentials or user information is found on client side source code (``Ctrl+U``) of any webpage. 
    - Usually found in **comments**
    ##### Prevention:
    - Use automated tools to scan and analyze available page source code to identify potential paths or directories and other sensitive information.
    - Don't use comments in the user facing source code 
    - Code reviews to check for exposed information
    - Classify data types within the source code and apply controls on what can or cannot be exposed on the client-side.
    - Front end developers may want to use JavaScript code packing or obfuscation to reduce the chances of exposing sensitive data

2. #### HTML Injection
    - Related to validating and sanitizing accepted user input.
    - HTML injection occurs when unfiltered user input is displayed on the page. This can either be through retrieving previously submitted code, like retrieving a user comment from the back end database, or by directly displaying unfiltered user input through JavaScript on the front end.
    - Web page defacing - injecting new **HTML code** to change the web page's appearance, inserting malicious ads, or even completely changing the page. 
    - Ex- <`style> body { background-image: url('https://academy.hackthebox.com/images/logo.svg'); } </style>`

3. #### Cross-Site Scripting (XSS)
    - HTML Injection vulnerabilities can often be utilized to also perform Cross-Site Scripting (XSS) attacks by injecting **JavaScript code** to be executed on the client-side. 
    - Very similar to HTML Injection in practice. 
    - Ex: `#"><img src=/ onerror=alert(document.cookie)>` -- gives us the browser cookie

4. #### Cross-Site Request Forgery (CSRF)
    - CSRF attacks utilize XSS vulnerabilities to perform certain queries, and API calls on a web application that the victim is currently authenticated to.
    - Ex: craft a JavaScript payload that automatically changes the victim's password to the value set by the attacker. Once the victim views the payload on the vulnerable page (e.g., a malicious comment containing the JavaScript CSRF payload), the JavaScript code would execute automatically. It would use the victim's logged-in session to change their password. Once that is done, the attacker can log in to the victim's account and control it.
    Ex - `"><script src=//www.example.com/exploit.js></script>`
    ##### Prevention:
    - Implement measures on the back end to detect and filter user input
    - Two main controls must be applied when accepting user input:
        - *Sanitization* : Removing special characters and non-standard characters from user input before displaying it or storing it.
        - *Validation*: Ensuring that submitted user input matches the expected format (i.e., submitted email matched email format)
    - Implement a web application firewall (WAF), which helps to prevent injection attempts automatically
    - Refer this [cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) for more tips.


## Back end vulnerabilities
--------------------------------------------------------------------------------------------------------------------------------

1. #### Broken Authentication/Access Control
    - *Broken Authentication* - refers to vulnerabilities that allow attackers to bypass authentication functions. For example, this may allow an attacker to login without having a valid set of credentials or allow a normal user to become an administrator without having the privileges to do so.
    - *Broken Access Control* - refers to vulnerabilities that allow attackers to access pages and features they should not have access to. For example, a normal user gaining access to the admin panel.

2. #### Malicious File Upload
    - Uploading malicious scripts.
    - ##### Prevention
        - validate the uploaded files
        Ex -the WordPress Plugin *Responsive Thumbnail Slider 1.0* can be exploited to upload any arbitrary file, including malicious scripts, by uploading a file with a double extension (i.e. shell.php.jpg). 

3. #### Command Injection
    - Attackers inject another command to be executed alongside the originally intended command, which allows them to directly execute commands on the back end server and gain control over it. 
    - Ex- the WordPress *Plugin Plainview Activity Monitor 20161228* has a vulnerability that allows attackers to inject their command in the ip value, by simply adding | COMMAND... after the ip value.

4. #### SQL Injection (SQLi)
    -  this vulnerability occurs when the web application executes a SQL query, including a value taken from user-supplied input.
    - $query = "select * from users where name like '%$searchInput%'";
    - Ex-  *College Management System 1.2* suffers from a SQL injection vulnerability, in which we can execute another SQL query that always returns true, meaning we successfully authenticated, which allows us to log in to the application.