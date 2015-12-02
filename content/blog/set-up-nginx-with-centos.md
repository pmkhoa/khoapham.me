---
title: Set up Nginx with Centos
date: 2015-11-18
tags: [Devops, Nginx, Web Development, Server]
---

Recently I had a chance to have my hand on setting up a web server using Nginx
and Centos. The requirements were simple:

- Server can serve multiple domains or subdomains.
- Each domain web folder is stored under different user account.

<!--more-->

In this post, I'd like to give you a walkthrough how to set up a server like
this on any private Centos server:

#### 1. First thing you want to do is to update system

    $ sudo yum update

#### 2. Install nginx
Create a yum repo for nginx at /etc/yum.repos.d/nginx.repo

    $ sudo vi /etc/yum.repos.d/nginx.repo
    # Add following content
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgcheck=0
    enabled=1

Install nginx

    $ sudo yum install nginx

Enable nginx service

    $ sudo chkconfig nginx on
    # Now you can use
    # service nginx start
    # service nginx stop
    # service nginx restart
    # service nginx status
    # service nginx reload

    # Now you can run
    $ sudo service nginx start

Congrats! You just got your nginx set up on your Centos server.

#### 3. Configure nginx, and setup domains
Default nginx configuration file is located at: /etc/nginx/nginx.conf. From the
last line of nginx.conf is where other custom configurations are loaded. This is
where we will setup our domains or subdomains.

Let's setup our first domain (example.com). We create a user call example to
manage this domain.

    $ sudo adduser example
    $ passwd example
    # Make sure to add your public key to this user so you can ssh to it

    $ cd /home/example
    $ mkdir public
    $ vim index.html
    # Add <h1>Hello world</h1>
    # If you are using su or centos account to create these files,
    # do not forget to change those file & folder owner to example, using chmod.

Now we want to create a nginx config for example.com domain at
/etc/nginx/conf.d

    $ cd /ect/nginx/conf.d
    $ mv default.conf example.default # disable default nginx message
    $ vim example.com.conf

Change the example.com.conf as follow

    server {

        # ipv4
        listen 80 default_server;

        # ipv6
        listen [::]:80 default_server;

        #    listen 80 default_server;
        server_name localhost example.com www.example.com;

        access_log /var/log/nginx/example.com-access.log  main;
        error_log /var/log/nginx/example.com-error.log debug;

        client_max_body_size 20m;

        location / {
            root /home/example/public/;
            index  index.html index.htm;
        }

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        # location ~ /\.ht {
        #    deny  all;
        # }
    }

#### 4. Fix permission issues
Now if everything went well, when you visit your server, you should see the 403
permission error. Calm down, this is expected. Since nginx doesn't have access
to the user folder at /home/example/public, the server will return permission
error. We need to add nginx user to example user group.

    $ gpasswd -a nginx example # Add nginx user to example group
    $ chown -R example:example /home/example
    $ chmod g+x /home/example && chmod g+x /home/example/public
    $ sudo -u nginx stat /home/example/public # make sure your all access to public
    # Disable SE Linux enforce
    $ setenforce 0

Congrats! Now you can try to access new server using: `curl localhost`




