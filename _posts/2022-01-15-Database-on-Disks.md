--- 
layout: post 
title: Data Storage for Row vs Columnar Databases
date: 14 October 2022 08:33:00 +0400 
categories: [Database, Data] 
tags: [Database] 
published: true
---

I always use to wonder about this question, especially the difference between row store databases and columnar databases. In this blog, I will talk a little bit about how data is stored in row store databases such as MySQL and Postgresql. This is really important to understand because it helps us understand and optimize our DB performance. This also becomes very important when writing database queries from your application. An inefficient database query will bog down the overall performance of the database and hence the application.  
  
But before that, we need to discuss a few core database storage concepts.  

### ROWID pseudocolumn  
The ROWID is a unique column, much like a primary key, that is generated internally by the system and returns the address of a row. Unlike a primary key which is maintained by the user to refer row inside a table, ROWID is created and hidden from the user.  
|Row_Id (Hidden)|User_id (primary key)|Name|Order|Amount|
|:------:|:------:|:------:|:------:|:------:| 
|1|00001|Alex|789654|100|
|2|00002|Ali|789655|59|
