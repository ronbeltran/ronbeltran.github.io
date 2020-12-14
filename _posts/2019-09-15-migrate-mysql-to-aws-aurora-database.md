---
layout: post
title: "Migrate MySQL to AWS Aurora Database"
date: 2019-09-15 20:16:58 +08:00
---

Recently I needed to migrate an MySQL database hosted in AWS RDS to AWS Aurora. I was surprised that it was a painless
process. The steps I followed are the following:

1. Stop cron jobs, applications, web servers so that no processes are accessing the database.
2. Create a database dump of a MySQL database that you want to migrate.
3. Create a AWS Aurora database, database user and grant permission, the very same way you do in MySQL.
4. Restore the database dump to the new Aurora database.
5. Update your application configuration to use the new Aurora database connection strings.
6. Make sure your application works and none is breaking. Success!


## Connect to remote MySQL instance

    mysql -u admin -p -h database-cluster-name.us-east-1.rds.amazonaws.com

## Create MySQL database and user

    $ mysql -u database_user -p -h database-cluster-name.us-east-1.rds.amazonaws.com

    mysql> CREATE DATABASE mydatabase;
    mysql> CREATE USER 'database_user'@'%' IDENTIFIED BY 'secret-password';
    Query OK, 0 rows affected (0.38 sec)

    mysql> GRANT ALL PRIVILEGES ON mydatabase.* TO 'database_user'@'%' WITH GRANT OPTION;
    Query OK, 0 rows affected (0.44 sec)

    mysql> flush priviliges;

## Backup MySQL database

    mysqldump -u database_user -h 'database-cluster-name.us-east-1.rds.amazonaws.com' mydatabase -p > mydatabase.2019-09-18.sql

## Restore MySQL database

    mysql -u database_user -p -h database-cluster-name.us-east-1.rds.amazonaws.com mydatabase < mydatabase.2019-09-18.sql

## Migrate from AWS Aurora Cluster to AWS Aurora Serverless

   After successfully migrating over to AWS Aurora, our project specs had changed and now we need to do another
   migration to use AWS Aurora Serverless. This is really simple to do with only few clicks at the AWS console.

   1. Make a snapshot/backup of a Aurora database instance.
   2. Restore this snapshot to a Aurora Serverless instance.
   3. Adjust your scaling configuration and you're done.

## Pro Tip: Tmux

When making a database dump or restoring a database while being SSH'd into a server, there's a big chance
that your ssh connection might timeout especially when your database is large. Obviously, you don't want it to stop
at the middle of dumping or restoring a database. In this case use some tools that will keep the process active while
its doing its work. I use and recommend **tmux** for this purposes. In Ubuntu you can install it like below:

    sudo apt install tmux
    tmux new -s migration

You install **tmux**, then you create a tmux session which is named **migration** or whatever name you want. While being inside tmux session, you
can now safely do dumping or restoring the database. For instance when making a backup of a MySQL database you can do
like so:

    mysqldump -u database_user -h 'database-cluster-name.us-east-1.rds.amazonaws.com' mydatabase -p > mydatabase.2019-09-18.sql

Then press Ctrl + b then press d, that will detach you to the tmux session while the process is not being stopped or
killed.

Verify that the tmux session is still active with the **tmux ls** command:

    tmux ls

If you see the tmux session named **migration** or whatever you named it to, you can re-attach to this session with the
**tmux attach** command.

    tmux attach -t migration

Practice creating a tmux session, detaching and re-attaching to it. This is very useful and had saved me a lot of time
and is very convenient.
