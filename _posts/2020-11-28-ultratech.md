---
title: UltraTech Machine - Writeup
author: Sujit Suryawanshi
date: 2020-11-28 15:10:00 +0800
categories: [TryHackMe]
tags: [tryhackme, thm, ultratech]
---

![Desktop View]({{ "/assets/img/thm-machines/ultratech-main.png" | relative_url }})

---

<strong><span style="color:#ff5555">Introduction</span></strong>

---

Hello InfoSec Folks, Today I did UltraTech machine from TryHackMe. It was medium level machine in which there are two webservers in which one is for API running on NodeJs and another is for frontend. The initial shell can get by doing Os Command Injection on webserver's API running on port 8081. For User there is MD5 hash saved in sqlite database and root can be owned with exploiting misconfigured docker.

Let's pawn this box.

<span style="color:#ff5555">This blog is meant for educational purposes only.</span>

---

<strong><span style="color:#ff5555">Port & Service Enumeration</span></strong>

---

Starting with Nmap,
As you can see below, The nmap scan revealed 4 port in which two are webserver running http on port 8081 and 31331 respectively.

```
root@kali:/tmp/ultratech# nmap -sC -sV -p- -T4 10.10.187.66
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-28 23:49 IST
Nmap scan report for 10.10.187.66
Host is up (0.17s latency).
Not shown: 65531 closed ports
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 dc:66:89:85:e7:05:c2:a5:da:7f:01:20:3a:13:fc:27 (RSA)
|   256 c3:67:dd:26:fa:0c:56:92:f3:5b:a0:b3:8d:6d:20:ab (ECDSA)
|_  256 11:9b:5a:d6:ff:2f:e4:49:d2:b5:17:36:0e:2f:1d:2f (ED25519)
8081/tcp  open  http    Node.js Express framework
|_http-cors: HEAD GET POST PUT DELETE PATCH
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: UltraTech - The best of technology (AI, FinTech, Big Data)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1021.01 seconds
```

We tried to do anonymous login on port 21 but it wasn't configured for anonymous login and also there were no exploit or CVE available for this particular version of ftp.

---

<strong><span style="color:#ff5555">Web Enumeration</span></strong>

---

For further enumeration, I moved to Port 8081 to check which kind of web application is running.
It was API server. Next I checked webserver running on port 31331 and There I got web application.

I started directory fuzzing on this web application to find something fruitful and I got one directory as /js/ which has directory listing enabled.

![Desktop View]({{ "/assets/img/thm-machines/ultratech-dirsearch.png" | relative_url }})

![Desktop View]({{ "/assets/img/thm-machines/ultratech-dir-listing.png" | relative_url }})

There I found one Javascript file named api.js which contains below code.

```
function checkAPIStatus() {
        const req = new XMLHttpRequest();
        try {
            const url = `http://${getAPIURL()}/ping?ip=${window.location.hostname}`
            req.open('GET', url, true);
            req.onload = function(e) {
                if (req.readyState === 4) {
                    if (req.status === 200) {
                        console.log('The api seems to be running')
                    } else {
                        console.error(req.statusText);
                    }
                }
            };
```
So here the web application calling one api with endpoint as <span style="color:#ff5555">/ping?ip=</span> which looks vulnerable for OS Command Injection.

For confirmation I tested the functionality.

![Desktop View]({{ "/assets/img/thm-machines/ultratech-bash-error.gif" | relative_url }})

As you can see in above GIF, When I inserted <span style="color:#ff5555">'</span> (single quote) then it gave syntax error as gives in bash.

---

<strong><span style="color:#ff5555">Initial Shell</span></strong>

---

For initial shell we used <span style="color:#ff5555">`</span> (backtick) to insert our command and with this we got out initial shell.

![Desktop View]({{ "/assets/img/thm-machines/ultratech-initial-shell.gif" | relative_url }})

Here we got initial shell of this machine. Now let's do some enumeration to get user.

---

<strong><span style="color:#ff5555">Privilege Escalation: User</span></strong>

---

After getting initial shell, First I checked /home directory to see what users are there and I found three users there (lp1, r00t, www)

After visiting to /home/www/api/ directory, We found one sqlite file which contains r00t user's hash.
As it was MD5 hash, We cracked it using john.

![Desktop View]({{ "/assets/img/thm-machines/ultratech-md5-hash.png" | relative_url }})

```
john --format=Raw-MD5 hash.txt
```

![Desktop View]({{ "/assets/img/thm-machines/ultratech-md5-hash-crack.png" | relative_url }})

We got password as <span style="color:#ff5555">n100906</span> after cracking r00t user's md5 hash.
Now let's login into r00t with SSH using above password.

---

<strong><span style="color:#ff5555">Privilege Escalation: Root</span></strong>

---

After doing login with r00t I ran linpeas.sh to check for Privilege Escalation Vulnerabilities.

![Desktop View]({{ "/assets/img/thm-machines/ultratech-linpeas.png" | relative_url }})

As you can see in screenshot, The current user is in docker group so I just did some google for Privilege Escalation using Docker and got [GTFOBins](https://gtfobins.github.io/gtfobins/docker/).

To get root using docker, We first need to check containers of docker.

```
docker images
docker run -v /:/mnt --rm -it bash chroot /mnt sh
```

![Desktop View]({{ "/assets/img/thm-machines/ultratech-root.png" | relative_url }})

<span style="color:#ff5555">**Thanks for reading this writeup and all suggestions are welcome.**</span>
