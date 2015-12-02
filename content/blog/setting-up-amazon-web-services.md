---
date: "2013-05-17T23:14:51-08:00"
title: Setting up Amazon Web Services
draft: false
tags: [Devops, AWS, Web Development, Web Service, Apache, HTTP, MySQL, Server]
---

To setting up Amazon Web Services, the process of setting up an EC2 on Amazon Web Services (AWS) is not complicated, all you need to get a server up and running are Apache, PHP,  MySQL.
Here are some initial steps that help you get started:
<!--more-->

###1. SSH connect to you EC2 instance.
To connect to your instance, you need the private key and the elastic IP address that associate with your instance. You can create an associated elastic IP address right in your Amazon Console Management.

    ssh -i [FILE NAME].pem ec2-user@[IP ADDRESS]

###2. Once you're connected, install update on your instance.

    sudo yum -y update

### 3. Install PHP &amp; its package.

    sudo yum install php php-mysql php-xml php-mcrypt php-mbstring php-cli php-devel php-pdo php-pear
    sudo pear install Log
    sudo yum install -y pcre-devel

### 4. Install Apache http.
    sudo yum install httpd

### 5. Install MySQL
    sudo yum install mysql-server mysql-devel mysql

### 6. Install PHP APC module
    sudo yum install php-pecl-apc

### 7. Configure MySQL and PHP
 Before you get everything running, make sure to check the PHP and MySQL configuration files.

- The PHP configuration file is php.ini, which is located under /etc/php.ini. Open the php.ini and increase memory limit to: 128M (or bigger if you wish to).   

    <pre>sudo vim /etc/php.ini
    memory_limit = 128M</pre>

- The MySQL configuration file is my.cnf located under /etc/my.cnf. Change it to this:

    <pre>[mysqld]
    datadir=/var/lib/mysql
    socket=/var/lib/mysql/mysql.sock

    # Disabling symbolic-links is recommended to prevent assorted security risks
    symbolic-links=0
    # Settings user and group are ignored when systemd is used.
    # If you need to run mysqld under a different user or group,
    # customize your systemd unit file for mysqld according to the
    # instructions in http://fedoraproject.org/wiki/Systemd
    skip-external-locking
    long_query_time=1
    slow_query_log
    slow_query_log_file=/var/log/log-slow-queries.log
    log-bin=mysql-bin
    server-id= 1
    key_buffer_size = 256M
    max_allowed_packet = 3M
    table_open_cache = 64
    read_buffer_size = 4M
    read_rnd_buffer_size = 8M
    myisam_sort_buffer_size = 16M
    thread_cache_size = 8
    query_cache_size= 32M
    thread_concurrency = 8
    [mysqld_safe]
    log-error=/var/log/mysqld.log
    pid-file=/var/run/mysqld/mysqld.pid
    myisam_recover_options</pre>

Now you have all packages installed, you can start your web server by starting mysql-server and httpd.

    sudo service mysqld start
    sudo service httpd start

Auto start mysql server, and httpd.

    sudo /sbin/chkconfig --levels 235 mysqld on
    sudo /sbin/chkconfig --levels 235 httpd on
