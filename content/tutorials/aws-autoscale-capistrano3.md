---
title: "How to Deploy AWS Auto Scaling Group using Capistrano 3"
date: 2021-09-19
description: "Deploy code to EC2 instances in an AWS Auto Scaling Group using Capistrano 3."
tags: ["AWS", "Capistrano", "Rails", "DevOps"]
---

### Motivation

If you've set up an AWS Auto Scaling Group and need to deploy code to those running EC2 instances, this Capistrano task can streamline the process by targeting only healthy nodes in the group.

---

### Requirements

- `aws-sdk-ec2 ~> 1`  
- `aws-sdk-autoscaling ~> 1`  
- `capistrano ~> 3`

---

### Installation

In your Rails app's `Gemfile`, add:

```ruby
gem 'capistrano3-asg-deploy'
```

Then run:

```bash
bundle install
```


Or install it yourself as:

```ruby
gem install capistrano3-asg-deploy
```

Add this line to your application’s Capfile:
```ruby
require 'capistrano/autoscaling_deploy'
```
---

### Usage


Set credentials with AmazonEC2FullAccess permission in the capistrano deploy script / stage files add the following lines
```ruby
set :aws_region, 'ap-northeast-1'
set :aws_access_key_id, 'YOUR AWS KEY ID'
set :aws_secret_access_key, 'YOUR AWS SECRET KEY'
set :aws_autoscaling_group_name, 'YOUR NAME OF AUTO SCALING GROUP NAME'
set :aws_deploy_roles, [:app, :web, :db]
set :aws_deploy_user, 'USER FOR SSH CONNECTION'
invoke 'autoscaling_deploy:setup_instances'
```
you can add more auto scaling configs to deploy to multiple auto scaling groups like a cluster

**How it works**:

This gem will fetch only running instances that have an auto scaling group name you specified

It will then reject the roles of :db and the :primary => true for all servers found but the first one

(from all auto scaling groups you have specified such as using more then once the auto scaling directive in your config — i.e cluster deploy)

this is to make sure a single working task does not run in parallel

you end up as if you defined the servers yourself like so:

```ruby
server ip_address1, :app, :db, :web, :primary => true
server ip_address2, :app, :web
server ip_address3, :app, :web
```

Thanks, Happy Deployment
