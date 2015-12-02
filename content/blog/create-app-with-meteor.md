---
title: Create App With Meteor
date: 2013-10-16
tags: [Web Development, Meteor, Javascript, Tutorial, Resource]
---

When I started to build the DearJJAbrams.com site, I came across a very simple functionality which was the realtime counter. This realtime counter worked as follow, when the user submitted their email, the counter would increase 1. However, this counter had to automatically update and synchronize with the server whenever anyone around the world submit their email, in other words, I had to create real time app with Meteor.
<!--more-->
At this point, things were getting little more complicated. The big question was how could the browser know when the database was updated in order to get synchronized? But, I was lucky to found out that [Meteor](http://www.meteor.com) was a perfect tool for this simple counter app.

In this article, I would like to share my little experience to create app with Meteor. You can see live example here at: [http://simple-counter.meteor.com](http://simple-counter.meteor.com).
The source code available at my [Github](https://github.com/khoap/simple-counter).

**1. Firstly, setting up Meteor app:**

Install and set up a Meteor app is extremely easy, you can follow the quick start from Meteor to setup you first app. [Meteor Documentation8](http://docs.meteor.com)
If you prefer to use the [meteorite](http://oortcloud.github.io/meteorite/) to initially create Meteor app, you can install Meteorite using following command:

    $ npm install -g meteorite
Then create a Meteor app by using:

    $ mrt create counter-app
**2. Structure Meteor app.**

When creating meteor app, you will have a simple structure of your app. However, you may want to re-organize things around to make things easier to work with.
Here is an example of my meteor app structure:

    counter-app
      client/
          lib/
          stylesheet/
          simple-counter.js
      public/
          fonts/
          images/
      server/
          simple-counter.js
      index.html
You can see more about the app structure from [meteor documentation](http://docs.meteor.com).

**3. On the client side.**

We display the counter value queried from the database, and handle the input event that people submit their email. Moreover, we want to create a publish &amp; subscribe relationship between the Meteor server &amp; client for the Counts documents in database.

    // Declare the Counts documents to hold the subscription from the server.
    Counts = new Meteor.Collection("counts");
    Meteor.subscribe("counts");
    // Output the counter value to the handlebar template.
    Template.CounterWrapper.CounterValue = function () {
      if (Counts.findOne()) {
        return Counts.findOne().countValue;
      }
    };
    // Handle the submit button event. If the email is correct, then call function on server side.
    // submitEmail, passing a param: email.
    Template.bodyContainer.events({
      'click .submit-counter' : function () {
        var emailReg = /^([\w-\.]+@([\w-]+\.)+[\w-]{2,4})?$/;
        var email = $("#email").val();
        if ( (email != "") &amp;&amp; (emailReg.test(email)) ) {
          Meteor.call('submitEmail', email, function(error) {
            if (error) {
              $(".notice-email-duplicate-input").fadeIn(600);
            }
            else {
              $(".notice-empty-input").fadeOut(500);
              $(".notice-email-duplicate-input").fadeOut(600);
            }
          });
        }
        else {
          $(".notice-empty-input").fadeIn(600);
        }
        return false;
      }
    });
**4. On the server side.**

We will define the method 'submitEmail' that is called in the client side to insert the email into the database.

    // We have two documents to handle the emails &amp; counter values. Emails will be kept privately.
    Emails = new Meteor.Collection("emails");
    Counts = new Meteor.Collection("counts");
    // Set up publish to the client.
    Meteor.publish("counts", function() {
      return Counts.find({});
    });

    // When Meteor startup, we want to make sure that the counts is matched with the current number of emails in Email documents.
    Meteor.startup(function () {
      if (Counts.find().count() === 0) {
        Counts.insert({name: 'counter', countValue: Emails.find().count()});
      }
    });

    // Declare Meteor method, to input the email into database.
    Meteor.methods({
      submitEmail: function (email) {
        var currentCount = Counts.findOne();
        if (Emails.find({email : email}).count() &gt;= 1) {
          throw new Meteor.Error(404, "error");
        }
        else {
          Counts.update({_id: currentCount._id}, {$inc: {countValue: 1}});
          var user_id = Emails.insert({email: email});
        }
      }
    });
**5. Deploying the app.**
Now it's time to deploy the app, I used the Heroku server to deploy the DearJJAbrams.com site, but in this simple app, I just use the Meteor server. Since it's very easy &amp; free:

    $ meteor deploy myapp.meteor.com
