---
title: Drozer - Basics
author: Sujit Suryawanshi
date: 2020-10-17 15:10:00 +0800
categories: [Android Application Pentesting, Dynamic Analysis]
tags: [drozer]
---

![Desktop View]({{ "/assets/img/android-pentesting/dynamic-analysis/drozer-main.png" | relative_url }})

---

<strong><span style="color:#ff5555">Introduction</span></strong>

---

Hi all, Hope everyone is good and doing well. In this blog we gonna look at one awesome framework which is really helpful in **Dynamic Runtime Analysis in Android Application Pentesting**.

The Drozer ([Link to Download](https://labs.f-secure.com/tools/drozer/)) is application assessment framework for android created by MWR InfoSecurity. It is written in iPython and has modules to exploit android vulnerabilities such as Leaking Content Providers, LFI, Various Vulnerability Scanners, Reverse Shell etc.

We can also write our own modules to automate, exploit things in android applications.

To getting started with Drozer just install on your system and drozer-agent on android device then we need to do port forwarding to connect with drozer-agent.
For port forwarding you can use <span style="color:#ff5555">adb forward tcp:31415 tcp:31415</span> command. By default Drozer use 31415 port so in my case I'm gonna use the same.

After forwarding port, enter <span style="color:#ff5555">drozer console connect</span> to connect drozer server to drozer agent which is installed on android device.

![Desktop View]({{ "/assets/img/android-pentesting/dynamic-analysis/drozer-connect.png" | relative_url }})

PS. In my case I'm going to use multiple vulnerable applications such as DIVA, InsecureBank etc.

<span style="color:#ff5555">This blog is meant for educational purposes only.</span>

---

<strong><span style="color:#ff5555">Basics of Drozer</span></strong>

---

Drozer has various collection of modules which are really helpful in Android Pentesting. To list down all modules of drozer just enter <strong><span style="color:#ff5555">ls or list</span></strong> command.

![Desktop View]({{ "/assets/img/android-pentesting/dynamic-analysis/drozer-list.png" | relative_url }})

Let's go through below modules and see what they does exactly.

---

<strong><span style="color:#ff5555">app.package.list</span></strong>

This module is used to get all applications (packages) installed on android device.
It will show you package names along with application names.

Command : <span style="color:#ff5555">run app.package.list</span>

To find particular application we can use -f argument along with application name.

Command : <span style="color:#ff5555">run app.package.list -f insecure</span>

---

<strong><span style="color:#ff5555">app.package.attacksurface</span></strong>

The app.package.attacksurface module gives attack surface about particular android application in which it shows exported activities, broadcast receivers, content providers and services.

Command : <span style="color:#ff5555">run app.package.attacksurface com.android.insecurebankv2</span>

---

<strong><span style="color:#ff5555">app.activity.info</span></strong>

This module shows exported activities which can be vulnerable.

Command : <span style="color:#ff5555">run app.activity.info -a com.android.insecurebankv2</span>

To check if the activity is vulnerable, We can use below command.

Command : <span style="color:#ff5555">run app.activity.start --component com.android.insecurebankv2 com.android.insecurebankv2.DoTransfer</span>

![Desktop View]({{ "/assets/img/android-pentesting/dynamic-analysis/drozer-activity-start.gif" | relative_url }})











<span style="color:#ff5555">**Thanks for reading this writeup and all suggestions are welcome.**</span>
