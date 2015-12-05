---
date: 2015-12-03T23:13:30-08:00
draft: false
title: My First Hugo Theme
tags: [Motivation, Golang, Hugo, Design]
---

When I started to get back to blogging, I felt my website
took a little long to compile. As a revolution of redesigning my blog, I want to switch my blog platform to something faster. <!--more-->
My blog was previously developed using [Middleman](http://middlemanapp.com), and now it's powered by [Hugo](http://gohugo.io), a faster static site
generator. I'm really pleased with the transition so far.

I also wrote a simple Hugo theme called [Hugo
Minimalist](https://github.com/pmkhoa/hugo-minimalist), this is just a mimic of
my current blog style, which I explained in [Minimalist in
design](http://khoapham.me/blog/minimalist-in-design-and-development/). Of
course, it's free to use if you want to try.

Another thing that I'd like to share is the better workflow for my website
deployment. With Middleman, I host the website on
[S3](https://aws.amazon.com/s3/), use Markdown to write content, and use
Middleman extension
[s3_deployment](http://localhost:1313/blog/sync-middleman-with-amazon-s3/) to deploy to S3, and then commit to github.
The whole process wasn't taking too long, but it can be simplified.
With Hugo, the website is hosted on Github (free), and I wrote a [simple script](https://github.com/pmkhoa/khoapham.me/blob/master/deploy.sh) to deploy and commit to github just one command when the
content is ready for production. I think the whole process is just better now.

If you plan to write lot of blog posts, and want to use static site generator
for it, I highly recommend [Hugo](http://gohugo.io).


