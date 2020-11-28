---
title: Weak File Permissions - Readable /etc/shadow
author: Sujit Suryawanshi
date: 2020-11-24 15:10:00 +0800
categories: [Privilege Escalation, Linux]
tags: [privilege-escalation]
---

![Desktop View]({{ "/assets/img/privilege-escalation/main.png" | relative_url }})

---

<strong><span style="color:#ff5555">Introduction</span></strong>

---

In this Privilege Escalation technique, We are going to exploit Readable /etc/shadow file.
Sometimes the system administrators give read permission of /etc/shadow file to everyone. In this case it is possible to get root user's password by cracking hashes stored in /etc/shadow file.

Let's start now,

<span style="color:#ff5555">This blog is meant for educational purposes only.</span>

---

<strong><span style="color:#ff5555">Enumeration</span></strong>

---

To identify this weakness, We need to understand file permission given to /etc/shadow file.
We use below command to check permissions of /etc/shadow file.

```
ls -la /etc/shadow
```

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-1.png" | relative_url }})

As you can see in above screenshot, The user have got read and write permission on /etc/shadow file. If you don't know Linux File Permissions then click [here](https://www.linux.com/training-tutorials/understanding-linux-file-permissions/).

---

<strong><span style="color:#ff5555">Exploitation</span></strong>

---

If you check the content of this file you will get password hash of each user placed on each line.

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-2.png" | relative_url }})

To exploit this flaw, We are going to copy root user's hash and crack it using john/hashcat.
In my case, I'm using john to crack the root user's hash.

Copy that hash and save in your system as hash.txt.

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-3.png" | relative_url }})

As you can see in above screenshot, We got password as **password123**.
Now lets use this password and login into root.

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-4.png" | relative_url }})

Here we got root of this system.

<span style="color:#ff5555">**Thanks for reading this writeup and all suggestions are welcome.**</span>
