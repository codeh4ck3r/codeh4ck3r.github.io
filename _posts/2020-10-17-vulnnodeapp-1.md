---
title: VulnNodeApp - Error Based SQLi
author: Sujit Suryawanshi
date: 2020-10-17 15:10:00 +0800
categories: [Web Application Pentesting, VulnNodeApp]
tags: [vulnnodeapp, nodejs-pentesting]
---

<h3><strong><span style="color:#ff5555">VulnNodeApp</span></strong></h3>

---

![Desktop View]({{ "/assets/img/vulnnodeapp/main.png" | relative_url }})

---

<strong><span style="color:#ff5555">Introduction</span></strong>

---

Today I am going to do [@4auvar](https://twitter.com/4auvar)'s Vulnerable Node Application (VulnNodeApp).
[Here](https://github.com/4auvar/VulnNodeApp) you can get the VulnNodeApp which you can install on local environment (Ubuntu-VirtualBox) and do Pentesting.
We are going to analyze the backend code for better understanding of vulnerabilities that are covered in this VulnNodeApp.

So let's start Pentesting this awesome application.

**This blog is meant for educational purposes only.**

---

<strong><span style="color:#ff5555">Error Based SQL Injection</span></strong>

---

The first vulnerability category in this vulnerable web application is SQL Injection. [@4auvar](https://twitter.com/4auvar) covered multiple types of SQL Injections here. In the end he also gave one exercise to solve.

* Error Based SQLi
* Blind SQLi
* Second Order SQLi
* Exercise

![Desktop View]({{ "/assets/img/vulnnodeapp/sqli-module.png" | relative_url }})

Let's take the first vulnerability which is Error Based SQLi.

After visiting the page I found that the **id** parameter going in URL.

![Desktop View]({{ "/assets/img/vulnnodeapp/sqli-url.png" | relative_url }})

When I inserted **'** (Single Quote) it gave me database error.

![Desktop View]({{ "/assets/img/vulnnodeapp/dbms-error.png" | relative_url }})

By this error we can guess that this is error based SQLi. And as you can see the DBMS is **MySQL**, For further exploitation, I used SqlMap to confirm and retrieve data from server.

![Desktop View]({{ "/assets/img/vulnnodeapp/sqlmap-1.png" | relative_url }})
![Desktop View]({{ "/assets/img/vulnnodeapp/sqlmap-2.png" | relative_url }})

---

<strong><span style="color:#ff5555">Source Code Analysis</span></strong>

---

Now lets take a look at the vulnerable code of this web application.

When I opened [/routes/users/index.js](https://github.com/4auvar/VulnNodeApp/blob/master/routes/users/index.js) file, I found routes in which below URL routing has been done.

![Desktop View]({{ "/assets/img/vulnnodeapp/error-sqli-routing.png" | relative_url }})

Here the request method is GET which retrieves **/error-based-sqli** page from server, But before that it checks for authentication of user with **isAuthenticated** and then it calls **errorBasedSqli** function from [user.js](https://github.com/4auvar/VulnNodeApp/blob/master/routes/users/user.js) file.

If you don't know what is URL routing, Models, Views, Controllers This is how MVC architecture works.

![Desktop View]({{ "/assets/img/vulnnodeapp/mvc-express.png" | relative_url }})

So now lets look at the [/routes/users/user.js](https://github.com/4auvar/VulnNodeApp/blob/master/routes/users/user.js) file and analyze further code.

In user.js file we got **errorBasedSqli** function which was passing **id** parameter to another function called **getUser**.

![Desktop View]({{ "/assets/img/vulnnodeapp/errorBasedSqli-function.png" | relative_url }})

The **getUser** function was passing the value of **id** parameter to function called **findUserById** which is available in [/controllers/usersController.js](https://github.com/4auvar/VulnNodeApp/blob/master/controllers/usersController.js) file.

![Desktop View]({{ "/assets/img/vulnnodeapp/getUser-function.png" | relative_url }})

After analyzing the [usersController.js](https://github.com/4auvar/VulnNodeApp/blob/master/controllers/usersController.js) file, I found that there is one class named **UsersController** in which **findUserById** function has been created and the input taken from **findUserById** is passed to the another function named **findUserById** which is available in [/models/usersModel.js](https://github.com/4auvar/VulnNodeApp/blob/master/models/usersModel.js) file.

![Desktop View]({{ "/assets/img/vulnnodeapp/UsersController-class.png" | relative_url }})

Now here is the main part comes, The [usersModel.js](https://github.com/4auvar/VulnNodeApp/blob/master/models/usersModel.js) file contains a class named **UsersModel** and inside this class there is another function named **findUserById** which was making queries to database and here you can see our input **id** is passing without any checks.

![Desktop View]({{ "/assets/img/vulnnodeapp/UsersModel-class.png" | relative_url }})

Here we have completed Error Based SQLi vulnerability. Stay tuned for next part of this VulnNodeApp.

**Thanks for reading this writeup and all suggestions are welcome.**
