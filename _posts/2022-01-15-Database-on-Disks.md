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

  
### Page
In a row-based database, a set of rows is stored together on the disk, these set of rows are known as pages. These pages are fixed-size units and can be configured to optimize a database, for example, PostgreSQL's default page size is 8KB.  
When we try to read a single row, let's say using SELECT, it will read the entire page and will filter the row we selected. For example, if a single page store 100 rows, running a query to read a single row would return 100 rows against each operation. On the other hand, if you reduce the size of the page, and you want to select multiple sequential rows this will increase the IO operations on the disk and hence slow down the database.  
  
### Index 
Indexes are used to find rows with specific column values quickly. Without an index, a database must begin with the first row and then read through the entire table to find the relevant rows. An index can pinpoint the page where the row you are looking for is stored. An index is also stored as a page on the disk, so we have to be careful in selecting the column we want to index because we can not index everything.  

  
## Row Store vs Column Store
As explained earlier, row store databases are stores all rows (in most cases) together these rows are placed in chucks together called pages. Unlike in a row store, columnar databases stores entire columns together if the column does not fit on a single page it will break the column into multiple pages.  

