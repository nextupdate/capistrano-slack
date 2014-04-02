# Capistrano Slack

## Install

Best way to install Capistrano Slack intergration is via Bundler.

Add the following to your Gemfile, then run the `bundle` command to install the gem direct from the git repository

```
gem 'capistrano-slack', :git => 'https://github.com/nextupdate/capistrano-slack.git'
```

Once installed you can use the settings below in your Capistrano deploy.rb to configure Slack.

## Push deployment messages to Slack

```ruby

require 'capistrano/slack'

# required
set :slack_token, "webhook_token" # comes from inbound webhook integration
set :slack_room, "#general" # the room to send the message to
set :slack_subdomain, "example" # if your subdomain is example.slack.com

# optional
set :slack_application, "Application Name" # override Capistrano `application`
set :slack_username, "Deploy Bot" # displayed as name of message sender
set :slack_emoji, ":cloud:" # will be used as the avatar for the message
```

You can obtain your `webhook_token` from the integrations section of the team page in Slack.  

https://subdomain.slack.com/services/new/incoming-webhook (if your subdomain is subdomain.slack.com)

## Run Smoke Tests after Deployment

You can also configure it to check provided URLs for expected status codes after a release and include the results with the post-deployment slack notification as attachments.

There are two ways to run the smoke tests.

### Option 1 (Simple)

Just set `run_smoke_test` to `true`, and it will use your Capistrano  `application` and `domain` variables to automatically check for a 200 status code at `http://{domain}`.

```ruby
set application: "My Fancy App"
set domain: "example.com"

# optional
set :run_smoke_test, true # Defaults to false
```
### Option 2 (Advanced)

Specify a set of URLs and expected status codes. For instance, you may want to check static or cached content in addition to a dynamic page.

With this approach, it defaults to expecting a 200, so you don't have to provide `expected_status_code` unless you expect a result other than 200.

Currently, if you have a multi-stage environment and set your domain in there, you'll probably need to setup the `urls_to_test` in your environment deploy files.

```ruby
endpoints = [
  {
    name: "Public",
    url: "https://#{domain}"
  },
  {
    name: "Application",
    url: "https://#{domain}/login",
    expected_status_code: 404
  }
]

set :urls_to_test, endpoints
```

### Handling Basic Authentication

If you have an environment that's behind basic auth, you can use the `smoke_test_auth` variable to provide the relevant values for the `curl` request.

```ruby
set :smoke_test_auth, "username:password"
```
