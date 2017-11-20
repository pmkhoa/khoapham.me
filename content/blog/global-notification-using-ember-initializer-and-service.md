---
date: 2016-04-12T11:18:20-07:00
draft: false
title: Global Notification Using Ember Initializer and Service
tags: [EmberJS, Javascript]
---

When developing Ember app, you may want to have a singleton to handle
global state such as global notification, or error handling.<!--more--> This doesn't fall
into `Route`, `Controller`, or `Component`. Ember offers an easy way to manage
this using `Service` and `Initializers`.

## Create a service to hold notification data   

    //app/services/global-notification.js
    import Ember from 'ember';
    
    export default Ember.Service.extend({
        notification: null,
        init() {
            this.set('notification', null);
        }
    });

## Create an initializer to inject global notification service in application
This allows us to use GlobalNotification service in route, and component.

    //app/initializers/global-notification.js
    import GlobalNotification from '../services/global-notification';
    
    export function initialize(application) {
        application.register('globalNotification:main', GlobalNotification, {singleton: true});
        application.inject('component', 'GlobalNotification', 'service:global-notification');
        application.inject('route', 'GlobalNotification', 'service:global-notification');
    }
    
    export default {
        name: 'global-notification',
        initialize
    };

## Using our global notification service.
Assuming you have a user settings form, and want to display notification when
user changes settings.

    //app/components/user-settings.js
    import Ember from 'ember';
    export default Ember.Component.extend({
        globalNotification: Ember.inject.service('global-notification'),
        actions: {
            saveSettings() {
                let accountInfo = this.getProperties("firstName", "lastName", "email", "password");
                Ember.$.ajax({
                    url: "/api/user",
                    type: "POST",
                    data: JSON.stringify(accountInfo)
                }).then(function(response) {
                    // We want to set notification data to global notification
                    service.
                   this.set("globalNotification.notification", "User settings
                   saved"); 
                });
            }
        }
    });

In the user setting forms, we can put `{{GlobalNotification.notification}}`
where we want to display the notification.

    //app/templates/components/user-settings.hbs
    <form id="user-settings">
        <p class="notification">{{GlobalNotification.notification}}</p>
        ....
    </form>

That is it. Now you can call `global-notification` service anywhere in your
application, and use its injection via `initializers` to display the data in
your template.
