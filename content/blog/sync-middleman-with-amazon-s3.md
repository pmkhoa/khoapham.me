---
title: Sync Middleman with Amazon S3
date: 2013-05-27T22:18:14-08:00
tags: [Web Development, Middleman, AWS, S3]
---

One thing that I love using Middleman and Amazon S3 is that they're a perfect combination for building and hosting a static HTML5 site.  
<!--more-->

Indeed, using Middleman, you can generate an HTML5 Boilerplate in one command:
<pre>middleman init my_new_boilerplate_project --template=html5</pre>
After generate a template, you can start up your development server by using command:
<pre>bundle exec middleman server (or just) middleman server</pre>
When you finish your site, you can build your site using command:
<pre>bundle exec middleman build</pre>
Is it better you can sync Middleman with Amazon S3 automatically when you build your site, so that you don't need to use any other FTP clients to upload these files manually? Yes, [middleman s3_sync](https://github.com/karlfreeman/middleman-sync) can do it for you:

**In your Gemfile, add following lines:**
  <pre>gem 'middleman-s3_sync'</pre>

**In your config.rb file, activate your middleman s3 sync**
<pre>activate :s3_sync do |s3_sync|
  s3_sync.bucket                     = 'your_s3_bucket_name' # The name of the S3 bucket you are targetting. This is globally unique.
  s3_sync.region                     = 'us-west-2'     # The AWS region for your bucket.
  s3_sync.aws_access_key_id          = 'your_access_key_id'
  s3_sync.aws_secret_access_key      = 'your_secret_access_key'
  s3_sync.delete                     = false # We delete stray files by default.
  s3_sync.after_build                = false # We chain after the build step by default. This may not be your desired behavior...
  s3_sync.prefer_gzip                = true
  s3_sync.path_style                 = true
  s3_sync.reduced_redundancy_storage = false
  s3_sync.acl                        = 'public-read'
  s3_sync.encryption                 = false
end</pre>

**Make sure you install all the necessary gems to your app.**
<pre> bundle install</pre>

Now you can sync your files to S3 using:
<pre> middleman sync_s3</pre>
