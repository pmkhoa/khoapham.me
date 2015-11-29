---
title: Completely setup Resque + Resque Schedule and Resque Mailer
date: 2014-09-10
tags: Rails, Resque, Resque Mailer, Resque Scheduler
---
When developing Rails app, there will be a chance that you will need to setup
job scheduler using Resque. In this post, I'd like to share my experience
setting up Resque schedule, and resque mailer on my recent Rails application
<!--more--> [My OneSight](https://my.onesight.org/en).

In your Gemfile, add these gems:

    gem 'resque', :require => "resque/server"
    gem 'resque_mailer'
    gem 'resque-scheduler', :require => 'resque/scheduler/server’
  

Create config/resque.yml, to store redis environment.

    defaults: &defaults
      host: localhost
      port: 6379
      db: 6
    development:
      <<: *defaults
    test:
      <<: *defaults
    staging:
      <<: *defaults
    production:
      <<: *defaults


Create config/initializers/resque.rb to bootstrap your resque:

    require 'resque/failure/multiple'
    require 'resque/failure/redis'
    Resque::Failure::Multiple.classes = [Resque::Failure::Redis]
    Resque::Failure.backend = Resque::Failure::Multiple
    Dir[File.join(Rails.root, 'app', 'jobs', '*.rb')].each { |file| require file }
    config = YAML.load(File.open("#{Rails.root}/config/resque.yml"))[Rails.env]
    Resque.redis = Redis.new(host: config['host'], port: config['port'], db: config['db'])


Create AsyncMailer to send mail via resque mailer

    class AsyncMailer < ActionMailer::Base
      include Resque::Mailer
    end

Then, replace all current ActionMailer, to AsyncMailer. Ex: mailers/communication_mailer.rb

    class CommunicationMailer< AsyncMailer
      ...
    end


Create rake task for resque setup: /lib/tasks/resque.rake , and create resque-scheduler

    require "resque/tasks"
    require 'resque/scheduler/tasks'

    task "resque:setup" => :environment do
      require 'resque'
      require 'resque-scheduler'
      Resque.schedule = YAML.load_file(Rails.root.join 'config', 'resque_schedule.yml')
    end

Create a resque scheduler jobs /config/resque_schedule.yml:
  
    complete_profile_reminder:
      every: 30s
      class: MyJob
      queue: user_notifications
      args:
      description: Runs the perform method in MyJob

The MyJob class is defined under: /app/controllers/jobs
    
    class MyJob
      def self.perform
        # Do anything here
        puts "Email user to complete their profile"
      end
    end

Route resque-web interface inside Rails.
Since we already required "resque/server”, and 'resque/scheduler/server’, in our Gemfile. We can just route the resque inside our routes.rb

    mount Resque::Server.new => '/resque’

Bonus: Setup Procfile to use foreman to start rails server and resque. Create your Procfile as follow:

    web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
    redis: redis-server
    worker: env TERM_CHILD=1 QUEUES=* bundle exec rake resque:work
    scheduler: bundle exec rake resque:scheduler

---
**Resources**

1. [Resque](https://github.com/resque/resque)
2. [Resque Scheduler](https://github.com/resque/resque-scheduler)
3. [Resque Mailer](https://github.com/zapnap/resque_mailer)
