---
title: "MongoDB: Can't take a write lock while out of disk space"
author: admin
layout: post
permalink: /2014/07/mongodb-cant-take-a-write-lock-while-out-of-disk-space/
categories:
  - MongoDB
---
While working on a project recently, we had to process a large number of files and dump the results into MongoDB. The client even provided us with a fancy instance on Amazon complete with 4000 IOPS and 500GB of storage.

However, we didn't realize that our processes were filling up too much data until the point where the drive said `"Usage: 100%"`, and our processes couldn't create or drop collections. I figured this was straight forward - went to MongoDB, dropped a collection we didn't really need. Wait a minute - didn't I discover that my drive was full because I couldn't drop a collection? Yep. I couldn't drop it now either. I couldn't do a `db.repairDatabase()` either, there was no space to do this.

```
Can't take a write lock while out of disk space
```

Frustrating as this was, the solution that finally helped me was this:

  1. Identify which *Database* isn't needed (Database, not Collection)
  2. Log into the instance running your mongod via ssh
  3. Stop the MongoDB Daemon using sudo service mongod stop
  4. Go to the data folder where MongoDB stores the databases
  5. Identify the files named `DB_NAME.ns, DB_NAME.0, DB_NAME.1... DB_NAME.n` which correspond to the database you want to remove
  6. Remove the files using `sudo rm DB_NAME.*` (You could also just move these files to a different drive with more space until you figure out a different solution)
  7. Check the used disk space (was about ~95% in my case - this was enough to get me started) and continue removing databases entirely depending on how much space you need
  8. Restart MongoDB using `sudo service mongod start` - the Database you removed is gone
  9. Log into MongoDB using the shell and drop collections as you originally started out to do

This was a quick-fix for a problem which is going to rear its head again, but for now, this helped me get things going. Hope this helps some of you unfortunate souls out there too! The solution is not intended to help MongoDB experts who've set up large clusters with a `mongos` backing up several `mongod`s - you guys should have been smarter at this. It is intended for small scale, test environments which have run into an unfortunate problem and want to get it fixed asap.