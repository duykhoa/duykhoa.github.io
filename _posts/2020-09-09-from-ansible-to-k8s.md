---
layout: post
title: From Ansible to Kubernetes
tags: Kubernetes Ansible k8s EC2 AWS Rails
excerpt_separator: <!--more-->
---

Recently I have successfully setup a kubernetes cluster to deploy a Rails app.

This post is memo of how did I do.

It includes 2 part:

- life before kubernetes
- life with kubernetes

<!--more-->

**Disclaimer**: It will be long

# Life before Kubernetes

First, let's talk about the Rails app.

The app is a simple, stateless project, following the Rails project structure.
There are quite a number of pages and apis. A background processor (Sidekiq) handles heavy side effect works.

It uses Postgresql to store data and Redis to communicate with the background processor.

There are a couple of third party services involves push notification, email sending, etc.

With this technical choice, the common approach for hosting it on cloud usually is straight forward.
It can be a little bit different from my solution, here is how I setup the infras.

The app is hosted in AWS infras. AWS provides a lot of services beside hosting - as you all know EC2.
Those services I used to deploy this app include some new services, and they could be changed in the future.
Hopefully when you find this post, the content is still relevant. Otherwise, please send me an email or reach to me to
update. You can find me in the [About me](/about).

I go deeper to this part soon. Be calm!

People usually recommend to draw the setup to diagram, a term for it is "software architecture design". There are a
certain type of activities happen while doing the architecture design, including analysis, synthesis, evalution,
evolution, knowledge management and communication, design reasoning and decision making, documentation (from [Wikipedia][1]).

I use this as a reference only. I believe the architecture is itself document, just as the code.

> Good code is like good joke, no need for explanation.

I still draw the design to whitepaper the design in some simple form. It is used for my thought process, not an output.

For a machine to run the Rails app, here is what it needs to installed:

- Nginx
- C compiler
- Required Linux packages: curl, libpq-dev, libvips-dev (or imagemagick), nodejs, git, etc.
- Ruby - of course
- Monit/Systemd

Things aren't necessary when using AWS services
- Ruby version manager - rvm, rbenv, chruby, ruby-install
- iptable, ufw

## Step by step setting up a Rails app on EC2

To disappoint you, I won't do that here. But don't too disappointed, here is a tutorial [The great code adventure][2].
I thought Digital Ocean should be the place for those tutorials, turn out they don't focus on that any more.
The last tutorial is a few years ago, using Rails 4 and Ubuntu 14.04, which may be out of date.

I am not going too much details, my setup usually is:

1. Add a new user: EC2 usually setup a different user from root, depends on the image, usually is `ubuntu` or
   `ec2-user`.
   This user can escalate privilege like root. We need another user without this ability. This user is used to access
   from outside to deploy the code in and run the web server command.

1. Ensure the server config doesn't allow password login via SSH protocol. It is very dangerous as hackers can
   bruteforce your password. It is safer to use SSH key to access.

1. Even better, no SSH access at all.
   You may feel it confused because how can we deploy to a host without access to the host.
   No worry, the answer is below.

1. Install necessary dependencies
1. Install Ruby from source
1. Install Node JS
1. Setup monit and systemd
   Maitain the app's life cycle isn't an easy work. It is more difficult when the app is scaled in many servers.
   A tool like systemd and monit can help.

   **Pro tips**: `systemd` spelling is ["System Five Hundred"][3].

1. Nginx configuration

   Nginx acts as proxy which gains performance in multiple ways.

   As Rails doesn't serve static assets in production by default, Nginx is a great solution.

1. Log Agent
   I use Cloudwatch agent to upload logs to Cloudwatch. This agent runs periodly to sync the logs to Cloudwatch.

1. Time Sync
   Even when you have only 1 server, it is a good idea to have it configured.

### How's about other services like Cache and Database?

  It seems like all resources finding online tells us to install the database and other services to the same server, the
  one run the Rails server.

  **No one do that in Production.** We should separate the app and the database, as well as other components.

  If the app goes down, the team can create new server and run the app on that. The new instance still connects to the
  same DB.

  This approach also prevents resources competition between multiple processes.

  The next question is "should I set it up myself?" or get a RDS (Amazon Relational Database Service).
  And the common answer is going with RDS. If pricing isn't your problem, just go with it and put less 1 thing outside
  your head. I have done the setup manually before for database and some search engines, the maintainance cost is much
  higher than the setup.

## Swap space

  Swapspace is a term describes a portion of your disk that is reserved for your system to use as a virtual memory.

  With swap space, you can use less powerful machine to run your app.

  As my setup, a nano or micro instance with 1Gb swapspace is quite okay to run a Rails application.

## Time synchronize

  Having multiple servers leads to different problems. One of those is the time sync.

  This is the most easy to forget step, until you have weird timing data because your background
  processor server write data with not-in- sync timestamp to the database. It is my experience before.

  The `ntp` (Network Time Protocol) service is used for this case.

  This is an [excellent document from AWS][4] for configuring the `ntp` service. The service is available out of the
  box, even when you don't use AWS platform.

## How to manage multiple servers

  There are 2 problems to solve: configuring and maintaining.

  For both configuring & maintaining multiple servers, my short answer is to repeat those steps above for each server.

  Seriously, there is no other choice. You need to run those steps for each server. However, you don't have to do it
  manually. By "Document" those steps to an executable code, you can use it to run in each server.

  I used to write the instructions to a bashscript file, which was very exciting that time. There is condition
  checking to check if it needs to perform installation for specific packages.

  After a while, I couldn't maintain the script since it grown too fat. Later on when I tried to find a better way to
  organize the scripts, I found Ansible. Since then, I only use Ansible to provision servers. There are many automation
  tools liek Chef, Puppet, SaltStack, Terraform, etc.

  There are quite countless time I have spent to try to improve my playbook - a term using in Ansible to describe a set
  of instructions running on a target group of servers.

  After reaching to a level of optimization the playbook, make it more robust in handling edge cases,
  compatible in several platforms, reusable etc. I was looking for a solution to run it automatically. Yes, I know you
  already know the name. I use a Continuous Integration tool to run the Ansible Playbook.

## Servers don't have public IP

  A good practice I found is not to give server a public IP. Keeps the instance in the private network is safer. 
  Without public IP, there is no room for people to attack your instance. Every traffic needs to go from the
  LoadBalancer.

  And how can the server connect to the internet?

  Without public IP, the instance can't connect to the internet. Such command like `yum install`, `apt-get update` will
  fail and you stuck.

  The cure for that is the NAT Gateway. To make it easy to relate, a NAT Gateway works like the router in your home
  networks. Not all of your devices need an IP, instead they all use the Router IP to access to internet.

  Amazon offers the NAT Gateway service. They also have the NAT Gateway instance, which is an AMI image from the
  community. You can purchase an EC2 Instance to launch this image. There is some configurations to make it works like
  the cross network traffic.

  **It seems like amazing. How can I access to the instance?**

  If you can access to the VPC private network first, then you can access to the instance by its private IP.

  Amazon supports a few ways to access to the instance, one of them is the Session manager. The session manager agent is
  install by default in some instance types includes Amazon Linux and Ubuntu. This Session Manager allows you to connect
  to the instance using the web console.

## Security groups

  This replaces the `ufw` or `iptables` nicely.

  By adding the inbound and outbound rules for each security group, we can guarantee certain traffic can reach to a
  component in your system.

## Teamcity CI

  Teamcity is a powerful CI tool, it provides both opensource version and enterprise version. The open source version
  allows 3 agents to run and 100 build configurations, which is more than enough in most of the project.

  There is offical Docker images for server and agent. That allows you to launch the Teamcity easily.
  The agent image even supports docker in docker feature, which allows to run a job in a docker container.

  Compare to other CI, Teamcity has a friendly UI for both running and setting up the pipeline. There are [Kotlin
  DSL][5] supports, which provides complicated logic included.

## Integrate everything together

  At this point, we have the server and other components hosted by AWS, the instructions for configuring servers are
  store in Ansible, the Teamcity CI is used to run the deployment.

## Shortcomings

  After launching this system, one of the biggest question I have is how to scale this easily.

  Open new instance and run the playbook on is easy. However, to make this step run automated, the auto scaling group
  feature is required.

  The second disadvantage of this setup is the static assets server is coupling with the main app.

  Each server equals with 1 process. To scale the service, the only way to spin up new server. Actually I'm quite okay
  with that. It is quite fast to create new instance and deploy the code (together with provisioning).

  The third one, which is the most unsolveable problem I have with this. There are many manual works involved in this
  system design. Everytime is done almost from scratch, which is hard to catch up with the business requirements.

  Since there are many manual steps, migrating the process requires a huge effort and time.

**That leads me to give Kubernetes a try**

**Notes:** I also want to ensure that nothing is perfect. Even Kubernetes serves well in some cases, there are disadvantages


  [1]: https://en.wikipedia.org/wiki/Software_architecture
  [2]: https://www.thegreatcodeadventure.com/deploying-rails-to-digitalocean-the-hard-way/
  [3]: https://www.freedesktop.org/wiki/Software/systemd/
  [4]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html
  [5]: https://www.jetbrains.com/help/teamcity/kotlin-dsl.html
