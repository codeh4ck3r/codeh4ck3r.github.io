---
title: 0ld is g0ld Challenge - Writeup
author: Sujit Suryawanshi
date: 2020-07-30 14:10:00 +0800
categories: [HackTheBox]
tags: [hackthebox, htb, 0ld is g0ld]
---

<h2 data-toc-skip>Writeup of 0ld is g0ld Challenge</h2>


![Desktop View]({{ "/assets/img/htb-challenges/old-is-gold-main.png" | relative_url }})


In this blog I'm gonna share my writeup on one awesome challenge named 0ld is g0ld.
The hint that was given with this challenge is "Old algorithms are not a waste, but are really precious".

So it means this challenges is related to some old algorithms used for encrypting.

So let's go and try this challenge.

After downloading the zip file given in challenge, I unzipped it and it was PDF file. So I opened it but the PDF required password to open it.

After a little bit google, I found a tool called "pdfcrack" which is I used here to crack this PDF.

Let's move further and crack this PDF.

I used rockyou.txt and pdfcrack to bruteforce the PDF and got the password.

![Desktop View]({{ "/assets/img/htb-challenges/old-is-gold-pass-crack.png" | relative_url }})

After getting password of PDF, I opened it and got only one photo and some text.

![Desktop View]({{ "/assets/img/htb-challenges/old-is-gold-pdf.png" | relative_url }})

By looking at the end of the PDF file, I found some weird string, First I assumed that maybe it's for end of file line, But after looking at it carefully, I understood that it's something different maybe some kind of old encoding.

![Desktop View]({{ "/assets/img/htb-challenges/old-is-gold-string.png" | relative_url }})

I copied that string and pasted on Google, so the first website came as "Morse Code Translater". So opened that webiste and pasted that string there and got the flag, This Morce Code Encoding was new for me.

![Desktop View]({{ "/assets/img/htb-challenges/old-is-gold-pdf-morse-code.png" | relative_url }})

Thanks for reading this writeup and all suggestions are welcome.
