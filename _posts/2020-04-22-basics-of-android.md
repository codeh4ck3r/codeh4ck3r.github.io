---
title: Basics of Android
author: Sujit Suryawanshi
date: 2020-04-22 14:10:00 +0800
categories: [Pentesting, Android Application]
tags: [android]
---

![Desktop View]({{ "/assets/img/bootup-process.jpg" | relative_url }})

<h2 data-toc-skip>Android Boot up Process</h2>

1. When android turns on it loads the Boot Rom first, then Bootloader boots up the Kernel.
2. Kernel launches Init.
3. Init mounts some important directories such as /dev, /sys and /proc.
4. Init reads configuration from init.rc and init.[device-name].rc
5. Init process is used to startup other necessary components such as adb daemon (adbd) which is responsible for ADB communication and volume daemon (vold).
6. Some properties that are used while loading up are kept in build.prop which is located at /system.
7. Init finally loads up process known as Zygote which is responsible for loading up Dalvik Virtual Machine (DVM) with shared libraries and minimum footprint to enable faster loading of overall processes.
8. Zygote keeps listening for new calls to launch more DVM if necessary.
9. Once Zygote fully launched, it forks itself and launches system, which loads other components like Activity/Service Manager.
10. Once the entire boot process completes, the system sends broadcast of BOOT_COMPLETED, which many application listening using a component called Broadcast Receiver.

---

<h2 data-toc-skip>Architecture of Android</h2>

![Desktop View]({{ "/assets/img/android-architecture.jpg" | relative_url }})

1. At the bottom level Linux Kernel contains device drivers like Camera, Display, Audio etc.
2. Then comes Native Libraries, which contains libraries like WebKit, OpenGL, FreeType, SQLite, Media, C runtime library (libc) etc. These libraries support different functionalities like
    * Webkit for browser support
    * SQLite for Database
    * FreeType for fonts
    * Media is for playing media like audio/video
3. Android Runtime contains some core libraries and DVM which is responsible for running applications.
4. Android Framework includes Java APIs which contains :
    * View System used for User Inderface such as Text boxes, Buttons etc.
    * Resource Manager provides access to resources like localized strings, graphics, layout files etc.
    * Notification Manager used to show notifications
    * Activity Manager manages lifecycle of apps and provides common navigation
    * Content Providers enables apps to access data from other apps such as Contacts app
5. Applications contains core applications such as Email, Contacts, Messages etc.
