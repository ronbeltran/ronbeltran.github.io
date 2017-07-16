---
layout: post
title: Install Oracle XE Universal in Ubuntu 
---

**Q: How to install Oracle Express Edition (XE) in Ubuntu**

[Oracle Express Edition](http://www.oracle.com/technetwork/products/express-edition/overview/index.html) is an entry-level, small-footprint database based on the Oracle Database. It's free to develop, deploy, and distribute; fast to download; and simple to administer.

In my previous company our Oracle installation is running on a high-end SunOS Unix server which I can never afford to have. At home I wanted to play with PL/SQL and needed to have a development environment with Oracle database where I can code at will. Thankfully my favorite Linux distro which is Ubuntu has access to a debian repository containing only [Oracle 10g Express Edition XE](https://help.ubuntu.com/community/Oracle)  which is provided by Oracle itself.

To install Oracle in Ubuntu just follow the instructions [here](https://help.ubuntu.com/community/Oracle).

One problem I encountered is that suddenly the repository is returning an unknown error message  and I can't install the Oracle database via 'sudo apt-get install oracle-xe-universal' so I have no other choice but to install it via .deb package.

To install Oracle via .deb package download the following:

    wget https://oss.oracle.com/debian/dists/unstable/main/binary-i386/libaio_0.3.104-1_i386.deb
    wget https://oss.oracle.com/debian/dists/unstable/non-free/binary-i386/oracle-xe-universal_10.2.0.1-1.1_i386.deb

and then install it via dpkg. Since Oracle XE depends on libaio we have to install that first.

    sudo dpgk -i libaio_0.3.104-1_i386.deb
    sudo dpkg -i oracle-xe-universal_10.2.0.1-1.1_i386.deb


ant then run the configure script as root.

    sudo /etc/init.d/oracle-xe configure


After that you have to edit your .bashrc to add some variables . In case you don't set Oracle to run automatically on start-up. You can run Oracle by invoking this command:

    sudo service oracle-xe start|stop|restart

To login issue this command:

    sqlplus sys as sysdba

That's it.
