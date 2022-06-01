---
title: "Google Dorking"
date: 2022-05-31T13:54:43+05:30
draft: false
author: "Ranjan"
---

I came across this [article](https://securitytrails.com/blog/google-hacking-techniques) about Google dorking.

With Google's tremendous web-crawling capabilities, it can index almost anything within your website, including sensitive information.This means you could be exposing too much information about your web technologies, usernames, passwords, and general vulnerabilities without even knowing it.

#### Definition:
Google “Dorking” is the practice of using Google to find vulnerable web applications and servers by using native Google search engine capabilities.

[Google Hacking Database](https://www.exploit-db.com/google-hacking-database) has a list of all the dorking scripts.

> Tip: Block specific resources from your website using a robots.txt file. 

> Tip: Do not use a single static IP. Google will block your connections.

## Popular Google Dork operators

Google’s search engine has its own built-in query language. The following list of queries can be run to find a list of files, find information about your competition, track people, get information about SEO backlinks, build email lists, and of course, discover web vulnerabilities.

Let’s look at the most popular Google Dorks and what they do.

- *cache:* this dork will show you the cached version of any website, e.g. cache:securitytrails.com
- *allintext:* searches for specific text contained on any web page, e.g. allintext: hacking tools
- *allintitle:* exactly the same as allintext, but will show pages that contain titles with X characters, e.g. - allintitle:"Security Companies"
- *allinurl:* it can be used to fetch results whose URL contains all the specified characters, e.g: allinurl:clientarea
filetype: used to search for any kind of file extensions, for example, if you want to search for pdf files you can use: email security filetype: pdf
- *inurl:* this is exactly the same as allinurl, but it is only useful for one single keyword, e.g. inurl:admin
- *intitle:* used to search for various keywords inside the title, for example, intitle:security tools will search for titles beginning with “security” but “tools” can be somewhere else in the page.
- *inanchor:* this is useful when you need to search for an exact anchor text used on any links, e.g. inanchor:"cyber security"
- *intext:* useful to locate pages that contain certain characters or strings inside their text, e.g. intext:"safe internet"
- *site:* will show you the full list of all indexed URLs for the specified domain and subdomain, e.g. site:securitytrails.com
- *: wildcard used to search pages that contain “anything” before your word, e.g. how to * a website, will return “how to…” design/create/hack, etc… “a website”.
- *|:* this is a logical operator, e.g. "security" "tips" will show all the sites which contain “security” or “tips,” or both words.
- *+:* used to concatenate words, useful to detect pages that use more than one specific key, e.g. security + trails
- *–:* minus operator is used to avoiding showing results that contain certain words, e.g. security -trails will show pages that use “security” in their text, but not those that have the word “trails.”

**[SEJ - SearchEngineJournal.com](https://www.searchenginejournal.com/google-search-operators-commands/215331/) gives the list of all such dork operators.**

## Dork examples
1. allintext:username filetype:log --  **Log files**
2. inurl:/proc/self/cwd -- **Vulnerable web servers**
3. intitle:"index of" inurl:ftp -- **Open FTP servers**
4. **env files**
5. intitle:index.of id_rsa -id_rsa.pub -- **SSH private keys**
6. filetype:log username putty -- **Putty SSH usernames**
7. filetype:xls inurl:"email.xls" -- **Email lists** ex- site:.edu filetype:xls inurl:"email.xls"
8. Live cameras 
    - inurl:top.htm inurl:currenttime -- **IP based cameras**
    - intitle:"webcamXP 5" -- WebcamXP-based transmissions
    - inurl:"lvappl.htm" -- general live cameras
9. intitle: index of mp3; intitle: index of pdf ; intext: .mp4 -- **MP3, Movie, and PDF files**
10. intitle:"Weather Wing WS-2" -- **Weather**
11. inurl:zoom.us/j and intext:scheduled for  -- **Zoom meetings**
12. "index of" "database.sql.zip" -- **SQL dumps**
13. intitle:"Index of" wp-admin -- **WP-ADMIN**
14. intitle:"Apache2 Ubuntu Default Page: It works" -- **Apache2**
15. "Index of" inurl:phpmyadmin -- **phpMyAdmin**
16. **JIRA/Kibana**
    - inurl:Dashboard.jspa intext:"Atlassian Jira Project Management Software"
    - inurl:app/kibana intext:Loading Kibana
17. inurl:_cpanel/forgotpwd -- **cPanel password reset**
18. allintitle: restricted filetype:doc site:gov -- **Government documents**

## Preventing Google Dorks

These measures are suggested to prevent your sensitive information from being indexed by search engines.

- Protect private areas with a user and password authentication and also by using IP-based restrictions.
- Encrypt your sensitive information (user, passwords, credit cards, emails, addresses, IP addresses, phone numbers, etc).
- Run regular vulnerability scans against your site, these usually already use popular Google Dorks queries and can be pretty effective in detecting the most common ones.
- Run regular dork queries against your own website to see if you can find any important information before the bad guys do. You can find a great list of popular dorks at the Exploit DB Dorks database.
- If you find sensitive content exposed, request its removal by using Google Search Console.
- Block sensitive content by using a robots.txt file located in your root-level website directory.

## robots.txt file
1. The following configuration will deny all crawling from any directory within your website, which is pretty useful for private access websites that don’t rely on publicly-indexable Internet content.

> User-agent: *
>
> Disallow: /

2.  You can also block specific directories to be excepted from web crawling. If you have an /admin area and you need to protect it, just place this code inside:

> User-agent: *
>
> Disallow: /admin/

This will also protect all the subdirectories inside.

3. Restrict access to specific files:

> User-agent: *
>
> Disallow: /privatearea/file.htm

4. Restrict access to dynamic URLs that contain ‘?’ symbol

> User-agent: *
>
> Disallow: /*?

5. To restrict access to specific file extensions you can use:

> User-agent: *
>
> Disallow: /*.php$/

In this case, all access to .php files will be denied.