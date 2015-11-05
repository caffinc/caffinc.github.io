---
title: 'org.hibernate.exception .GenericJDBCException: Incorrect string value'
author: admin
layout: post
permalink: /2014/07/org-hibernate-exception-genericjdbcexception-incorrect-string-value/
videourl:
  - 
categories:
  - Java
tags:
  - hibernate
  - incorrect
  - mysql
  - string
---
I&#8217;m fairly new to Hibernate. I prefer using Java&#8217;s JDBC drivers and getting my work done without any additional frameworks on top of it. But sometimes your client comes along and asks you to use Hibernate or Spring, which are excellent, but a pain in the ass sometimes. Especially when you don&#8217;t have time to solve what you assume is a problem with Hibernate, and not your code.  
One such problem was this:

<p style="padding-left: 30px;">
  <code>[ERROR] 2014-07-25 09:43:28 (SomeDAO.java:insertSomeEntity:74)     - Incorrect string value: '\xEF\xBF\xBDbec' for column 'program_name' at row 1&lt;br />
org.hibernate.exception.GenericJDBCException: Incorrect string value: '\xEF\xBF\xBDbec' for column 'program_name' at row 1&lt;br />
at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert&lt;br />
(StandardSQLExceptionConverter.java:54)&lt;br />
...&lt;br />
Caused by: java.sql.SQLException: Incorrect string value: '\xEF\xBF\xBDbec' for column 'program_name' at row 1&lt;br />
at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:1078)&lt;br />
...&lt;br />
at org.hibernate.engine.jdbc.internal.proxy.AbstractStatementProxyHandler&lt;br />
.continueInvocation(AbstractStatementProxyHandler.java:122)&lt;br />
... 21 more</code>
</p>

I checked the character set, it was latin1. I chucked and said &#8220;Heh&#8230; this has to be UTF8&#8221;. Changed it to UTF8. Restarted the program, same problem. &#8220;Hmmm&#8230; well, let me look at the table from which this data came from&#8221;. That table was latin1 too. Strange&#8230; very strange&#8230;

After spending a considerable amount of time, I found out that the problem was that in the input table, the field was set to UTF8, but in this particular table, the field was latin1. I changed the field alone using an alter table command:

`ALTER TABLE tablename MODIFY columnname varchar(200) CHARACTER SET utf8 DEFAULT NULL;`

That&#8217;s about it. Things started working! Hope this helped you all.