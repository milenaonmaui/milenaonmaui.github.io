---
layout: post
title:      "How I Got Into Web Development"
date:       2018-12-03 16:35:03 -0500
permalink:  how_i_got_into_web_development
---

The only thing I regret is not getting the idea of signing up for a web development bootcamp earlier. If I had started as soon as I lost my full-time engineering job in the end of 2015, I would have caught the peak of the wave of demand for web developers and by now I would have had a few years of experience. But, as the saying goes, better late than never.

Going through a rigorous M.S. program in Electrical Engineering I didn't like coding very much. We had to implement complex image processing algorithms in C, and that kind of low level coding didn't appeal to me very much.
On the other hand, before I got some taste of real web development, I thought if it as mostly graphical design type of skill of creating a visully pleasing web site. I didn't consider much what goes on on the back end.

In 2017 out of a sudden my husband told me to start learning some web development since I was only part-time employed and looking for a tech job. My husband is a Java developer and a friend of his had asked him for help creating a web page. At that time we didn't know any details, we only had a MS Access screenshot that had to be recreated in the browser.

So,  I started a Coursera course from Johns Hopkins University named [HTML, CSS and Javascript for Web Developers](https://www.coursera.org/learn/html-css-javascript-for-web-developers). Halfway through the course, just after I had covered some HTML, CSS and Bootstrap basics, I got another guidance regarding my future assignment:  "Start learning C#, you are going to need it for the project!". So I searched for beginner courses and found a Udemy course titled [Learn C# with Windows Forms and SQL Server](https://www.udemy.com/csharp6windowsforms/). C# is a Microsoft-developed programming language that is part of the .NET platform. So at that point I installed  Visual Studio Community Edition, MS SQL Server Express and SQL Server Management Studio (SSMS) on my computer - all of them free! One awsome thing I find about web development is that you get most of the tools and frameworks for free.

Again, half way through my C# course I realized that I need to focus more seriously on web forms using ASP.NET versus just Windows forms. So then I started yet another course - A 18-hour SQL/SQL Server/ Visual Studio 2017 Course, which covered a lot of necessary ground - SQL, SQL Server, SSMS, ASP.NET, Visual Studio, C#, and HTML.

One more time I didn't have time to complete the course because I received the actual code. The client already had a lot of developmet work done in ASP.NET and C#, performing CRUD (Create, Read, Update, Delete) operations on a database and generating invoices. The issue was that he could not create a new customer or project using the admin web page, so he had to go in MS Access to create these entities and write them to the SQL database, and only then he could generate invoices.

I was also given a "skinny version" of the SQL database, only a subset of the real database, and even that piece was so huge that my PC with 24 GB of RAM was struggling importing it. It had hundreds of stored procedures and probably more than a hundred tables (later I was told that for a databse this si small!!). The code was also huge - it was a classical 3-layer architecture: a Database Access Layer(DAL), which performed all direct interactions with the database, a Busingess Logic Layer (BLL)  which manages communication between the user interface (UI) and the database, implementing business rules and workflows, and the UI layer which takes in user input in the form of textboxes, drop-down lists, checkboxes, etc and passes that to the BLL.

So this was not an entry level project, it had some serious programming in it, and also security features that restricted what machines can get access to the database and other restrictions in the web.config file. Trying to compile the code in my Visual Studio environment resulted in a long list of errors.

At that point we had to hire an ASP.NET expert as a consultant to help us bootstrap the whole project and get it going. With the help of the consultant we were able to bring up the whole functionality and start talking to the database. Then, based on the client specifications, we created the first page which was able to copy and existing project, make some modifications and save it to the databse under a new name.

After that point, I had enough knowledge and examples available and continued on my own, enhancing the project page with a table of rates and commissions associated with the project, and then adding a Customer and Vendor pages, all of them performing CRUD to the database. The visual design was not important to the client at all, as this was meant to be an admin-only interface, and we kept styling to a minimum. However,  I did use Bootstrap ( a front-end framework created by Twitter) to get all the built-in features and templates and make the page look more professional.

[My First Web Development Project](https://imgur.com/a/DORqp0P)

So, overall, I struggled a lot on this project. I spent  hundreds of hours reading, searching up errors on Google and Stack Overflow. In addition to the courses mentioned above I purchased 6 more ASP.NET-related courses and I read countless number of online articles and blog posts. Some days I was stuck with an obscure error for a whole day and didn't make any progress. But with all this struggle I also had a lot of fun. Every time I accomplished something, every time I saw my data being written to the database or displayed neatly in a form, I experienced huge satisfaction. The long hours seemed to fly.  As most things in life, the final results were sweet and sour. Yes, I learned a lot, and knowing where I satrted from, I accomplished a lot. But the customer was not happy with the slow pace of development. He wanted several more pages and given the existing pace, he didn't want to wait another few months. So he cancelled the project and perhaps hired a more experienced developer. For a while, I had a sense of failure, but in the same time, I had to give myself credit for what I achieved, starting from zero.

In summary, this is what lead me to the idea to learn web development the proper way, not jumping around different topics and courses because I am in a rush. I want to develop solid foundations and good practices, so I can build upon them and turn myself into a professional web developer. After some research I decided that Flatiron School offers a great course and career support for a fraction of the price of other bootcamps. And that is why I am here.



