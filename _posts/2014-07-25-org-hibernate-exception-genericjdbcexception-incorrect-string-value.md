---
title: 'org.hibernate.exception.GenericJDBCException: Incorrect string value'
author: admin
layout: post
permalink: /2014/07/org-hibernate-exception-genericjdbcexception-incorrect-string-value/
categories:
  - Java
tags:
  - hibernate
  - incorrect
  - mysql
  - string
---
I'm fairly new to Hibernate. I prefer using Java's JDBC drivers and getting my work done without any additional frameworks on top of it. But sometimes your client comes along and asks you to use Hibernate or Spring, which are excellent, but a pain in the ass sometimes. Especially when you don't have time to solve what you assume is a problem with Hibernate, and not your code.  
One such problem was this:


```
[ERROR] 2014-07-25 09:43:28 (SomeDAO.java:insertSomeEntity:74)     - Incorrect string value: '\xEF\xBF\xBDbec' for column 'program_name' at row 1
org.hibernate.exception.GenericJDBCException: Incorrect string value: '\xEF\xBF\xBDbec' for column 'program_name' at row 1
at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert
(StandardSQLExceptionConverter.java:54)
...
Caused by: java.sql.SQLException: Incorrect string value: '\xEF\xBF\xBDbec' for column 'program_name' at row 1
at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:1078)
...
at org.hibernate.engine.jdbc.internal.proxy.AbstractStatementProxyHandler
.continueInvocation(AbstractStatementProxyHandler.java:122)
... 21 more
```

I checked the character set, it was latin1. I chucked and said 'Heh... this has to be UTF8'. Changed it to UTF8. Restarted the program, same problem. 'Hmmm... well, let me look at the table from which this data came from'. That table was latin1 too. Strange... very strange...

After spending a considerable amount of time, I found out that the problem was that in the input table, the field was set to UTF8, but in this particular table, the field was latin1. I changed the field alone using an alter table command:

```
ALTER TABLE tablename MODIFY columnname varchar(200) CHARACTER SET utf8 DEFAULT NULL;
```

That's about it. Things started working! Hope this helped you all.
