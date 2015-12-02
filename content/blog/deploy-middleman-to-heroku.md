---
title: Deploy Middleman to Heroku
date: 2014-10-14
tags: [Rails, Heroku, Middlemanapp, Ruby, Devops]
---

Heroku is a great cloud service, it uses git for version control and deployment which is very handy for me to just update my blog in one command.
Here are some simple step that you can set it up. 

<!--more-->

In your Gemfile, add puma and rake:

    gem 'puma',      '~> 2.7'
    gem 'rake',      '~> 10.1'

Create a Procfile, and add this code

    web: bundle exec puma -p $PORT

Since we're going to tell Heroku to use our build folder under our Middlemanapp, we will use the rake task to compile our build assets. Under your root app, create: Rakefile

    namespace :assets do
      task :precompile do
        sh 'middleman build'
      end
    end

Finally we need to setup a Rack server to tell Heroku to load our build folder, and its assets

    module Rack
      class TryStatic
        def initialize(app, options)
          @app = app
          @try = ['', *options.delete(:try)]
          @static = ::Rack::Static.new(lambda { [404, {}, []] }, options)
        end
        def call(env)
          orig_path = env['PATH_INFO']
          found = nil
          @try.each do |path|
            resp = @static.call(env.merge!({'PATH_INFO' => orig_path + path}))
            break if 404 != resp[0] && found = resp
          end
          found or @app.call(env.merge!('PATH_INFO' => orig_path))
        end
      end
    end
    use Rack::Deflater
    use Rack::TryStatic, :root => "build", :urls => %w[/], :try => ['.html', 'index.html', '/index.html']
    # Run your own Rack app here or use this one to serve 404 messages:
    run lambda{ |env|
      not_found_page = File.expand_path("../build/404.html", __FILE__)
      if File.exist?(not_found_page)
        [ 404, { 'Content-Type'  => 'text/html'}, [File.read(not_found_page)] ]
      else
        [ 404, { 'Content-Type'  => 'text/html' }, ['404 - page not found'] ]
      end
    }

Now your site is ready to deploy to Heroku. Just create a Heroku app and add Heroku git remote link, and enjoy!

P.S. Don't forget to add the build directory to your .gitignore
