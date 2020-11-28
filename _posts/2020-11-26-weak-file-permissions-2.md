---
title: Weak File Permissions - Writable /etc/shadow
author: Sujit Suryawanshi
date: 2020-11-26 15:10:00 +0800
categories: [Privilege Escalation, Linux]
tags: [privilege-escalation]
---

![Desktop View]({{ "/assets/img/privilege-escalation/main.png" | relative_url }})

---

<strong><span style="color:#ff5555">Introduction</span></strong>

---

Writable /etc/shadow file can also be used to Escalate Privileges in linux. As we seen in previous post ([Readable /etc/shadow](https://www.codeh4ck3r.xyz/posts/weak-file-permissions-1/)) We were able to read content of /etc/shadow file and with that permission we took hash and cracked it using john.
In this Privilege Escalation technique we are going to replace old hash of root user with new hash in /etc/shadow file.
And then we are going to login as root.

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

Now we are going to generate new hash in our system and replace the root user's old hash with new one in target system.

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-5.png" | relative_url }})

Lets edit the /etc/shadow file and replace old hash with new one.

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-6.png" | relative_url }})

After replacing the old hash with new one, Let's login with root user.

![Desktop View]({{ "/assets/img/privilege-escalation/etc-shadow-7.png" | relative_url }})

Here we got root of this system.

<span style="color:#ff5555">**Thanks for reading this writeup and all suggestions are welcome.**</span>
