---
date: 2016-04-25T15:46:17-07:00
draft: true
title: Deploy Ember App on Github Pages
tags: [EmberJS, Devops]
---

Github pages is ideal for hosting static websites. Especially if you're developing a open source project, and want to have a simple website for it.
<!--more-->

When using Ember Cli, the application is built into `/dist` folder, this includes static assets such as index.html, application.js, etc... Would it be nice if you create a gh-pages branch for just the `/dist` folder?  

Here are steps that I used to deploy my recent open source tool [Meta Tags
Generator](http://pmkhoa.github.io/meta-tags-generator/):

```

//Update baseURL from config environment
//Change meta-tags-generator to reflect your github page url
//ENV.baseURL = '/meta-tags-generator';

//From master branch
//Remove /dist from your .gitignore
//Remove /dist folder
git commit -am "Ready to deploy " && git push origin master

//Create orphan gh-pages branch
git checkout --orphan gh-pages
git rm --cached $(git ls-files)
git add README.md
git commit -am "Init gh-pages"
git push origin gh-pages
git co master

//Make sure to change meta-tags-generator to reflect your repo
git subtree add --prefix=dist git@github.com:pmkhoa/meta-tags-generator.git gh-pages --squash

//Build ember app & push to gh-pages
ember build --prod
git add -am "Update latest /dist"
git push origin master
git subtree push --prefix=dist git@github.com:pmkhoa/meta-tags-generator.git gh-pages
```


To make it easier for deploying process, we can create an shell script to handle
`git commit`, `ember build`, and `git push`. Create a deploy.sh file like this:

```    

#!/bin/bash

echo -e "\033[0;32mDeploying updates to Github...\033[0m"

# Build the project.
ember build --prod

# Add changes to git.
git add -A

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
# Make sure to change meta-tags-generator here to your repo name.
git push origin master
git subtree push --prefix=dist git@github.com:pmkhoa/meta-tags-generator.git gh-pages
```

You can run deploy.sh script like this: `./deploy.sh -m "Update website"`

Happy coding & happy deploying!
