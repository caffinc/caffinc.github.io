---
title: MongoDB $eq operator for find()
author: admin
layout: post
permalink: /2014/02/mongodb-eq-operator-for-find/
categories:
  - MongoDB
tags:
  - $eq
  - MongoDB
  - newbie
  - noob
  - operators
---
This is going to be a short post for all the people out there who're new to MongoDB. I had to dive into MongoDB as part of a new project that I joined. I was asked to write a Map-Reduce job, and in the process decided to run a few queries on MongoDB.

I found out that there was a `$lte` and a `$gte` operator. But no $eq operator. It baffled me that there was no way to check for equality. I searched a bit but found no option, so I used an `$and` operator and did a `$lte` AND `$gte`, effectively getting a `$eq` operator.

But now I know better. To check for equality in MongoDB, you just put the second value as the second parameter.

`db.test.find({"a":123})`

This checks if the field named `a` equals `123`.

That's about it. Simple, but not quite straightforward to a person fresh out of the RDBMs world and new to MongoDB :)
