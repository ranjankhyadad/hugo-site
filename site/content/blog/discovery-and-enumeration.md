---
title: "Discovery and Enumeration"
date: 2022-06-05T14:03:18+05:30
draft: false
author: "Ranjan"
---

## Service Scanning
--------------------------------------------------------------------------------------------------------------------------------
- The first thing we need to do is identify the operating system and any available services that might be running.
- Check if we can coerce the service into performing some unintended action that supports our objectives, such as executing a command of our choosing.
- Computers are assigned an IP address, which allows them to be uniquely identified and accessible on a network. The services running on these computers may be assigned a port number to make the service accessible. 
> Ports 1 to 1,023 being reserved for privileged services.

1. Nmap
- Suppose that we want to perform a basic scan against a target residing at 10.129.42.253. To do this we should type nmap 10.129.42.253 and hit return. We see that the Nmap scan was completed very quickly. This is because if we don't specify any additional options, Nmap will only scan the 1,000 most common ports by default. The scan output reveals that ports 21, 22, 80, 139, and 445 are available.

```
User@htb[/htb]$ nmap 10.129.42.253

Starting Nmap 7.80 ( https://nmap.org ) at 2021-02-25 16:07 EST
Nmap scan report for 10.129.42.253
Host is up (0.11s latency).
Not shown: 995 closed ports
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 2.19 seconds

```

> Tip: port 3389 available says it's a Windows machine; port 22 (SSH) being available indicates that the target is running Linux/Unix but it can be configured on Windows too

> `nmap -sV -sC -p- 10.129.42.253`

- #### Nmap Scripts: 
- Specifying -sC will run many useful default scripts against a target, but there are cases when running a specific script is required. 
- In an assessment scope, we may be asked to audit a large Citrix installation. We could use [this](https://raw.githubusercontent.com/cyberstruggle/DeltaGroup/master/CVE-2019-19781/CVE-2019-19781.nse) Nmap script to audit for the severe Citrix NetScaler vulnerability (CVE-2019–19781), while Nmap also has other scripts to audit a Citrix installation.
> `locate scripts/citrix`
- The syntax for running an Nmap script is `nmap --script <script name> -p<port> <host>`

#### Attacking Network Services

1. Banner Grabbing
- Banner grabbing is a useful technique to fingerprint a service quickly.
- Often a service will look to identify itself by displaying a banner once a connection is initiated.
- Nmap will attempt to grab the banners if the syntax `nmap -sV --script=banner <target> `is specified.
- Using netcat,`nc -nv 10.129.42.253 21` gives us the banner on port 21. 

2. FTP
- A Nmap scan of the default port for FTP (21) reveals the vsftpd version installation.
- Run `nmap -sC -sV -p21 10.129.42.253` for checking anonymous authenticationon FTP port 21.
- Connecting to the service using the ftp command-line utility-`ftp -p 10.129.42.253`
- Access files and login data using this to further your testing.

3. SMB
- SMB (Server Message Block) is a prevalent protocol on Windows machines that provides many vectors for vertical and lateral movement.  
- Sensitive data, including credentials, can be in network file shares, and some SMB versions may be vulnerable to RCE exploits such as EternalBlue. It is crucial to enumerate this sizeable potential attack surface carefully.
- Nmap has many scripts for enumerating SMB, such as smb-os-discovery.nse, which will interact with the SMB service to extract the reported operating system version.
> `nmap --script smb-os-discovery.nse -p445 10.10.10.40`

- Shares: 
- SMB allows users and administrators to share folders and make them accessible remotely by other users.
- Often these shares have files in them that contain sensitive information such as passwords. A tool that can enumerate and interact with SMB shares is **smbclient**. 
> `smbclient -N -L \\\\10.129.42.253`
> 
> `smbclient -U bob \\\\10.129.42.253\\users`

4. SNMP
- SNMP Community strings provide information and statistics about a router or device, helping us gain access to it. 
- The manufacturer default community strings of public and private are often unchanged. In SNMP versions 1 and 2c, access is controlled using a plaintext community string, and if we know the name, we can gain access to it. 

### Web Enumeration
--------------------------------------------------------------------------------------------------------------------------------
-  Webservers host web applications (sometimes more than 1) which often provide a considerable attack surface and a very high-value target during a penetration test. Proper web enumeration is critical, especially when an organization is not exposing many services or those services are appropriately patched.

1. Gobuster
- After discovering a web application, it is always worth checking to see if we can uncover any hidden files or directories on the webserver that are not intended for public access. 
- We can use a tool such as ffuf or GoBuster to perform this directory enumeration.
- GoBuster is a versatile tool that allows for performing DNS, vhost, and directory brute-forcing. The tool has additional functionality, such as enumeration of public AWS S3 buckets. 
#### Directory/File Enumeration
> `gobuster dir -u http://10.10.10.121/ -w /usr/share/wordlists/dirb/common.txt`
#### DNS Subdomain Enumeration
> `git clone https://github.com/danielmiessler/SecLists`
>
> `sudo apt install seclists -y`
- Next, add a DNS Server such as 1.1.1.1 to the /etc/resolv.conf file. To target the domain inlanefreight.com, 
> `gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt`

Tips:
- Banner Grabbing / Web Server Headers - cURL, Eyewitness(can be used to take screenshots of target web applications, fingerprint them, and identify possible default credentials.)
- Whatweb - We can extract the version of web servers, supporting frameworks, and applications using the command-line tool whatweb. Ex- `whatweb 10.10.10.121`
- Certificates - Browsing to a website and viewing the certificate reveals the email address and company name. 
- Robots.txt - purpose is to instruct search engine web crawlers such as Googlebot which resources can and cannot be accessed for indexing. The robots.txt file can provide valuable information such as the location of private files and admin pages.
- Source Code - `Ctrl+U`