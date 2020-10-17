---
layout: post
title: Deploy application on Kubernetes
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
Hopefully when you find this post, the content is still relevant.

People usually recommend to draw the setup to diagram, a term for it is "software architecture design". There are a
certain type of activities happen while doing the architecture design, including analysis, synthesis, evalution,
evolution, knowledge management and communication, design reasoning and decision making, documentation (from [Wikipedia][1]).

I use this as a reference. I believe the architecture should be self documented, just like the code.

> Good code is like good joke, no need for explanation.

I still draw the design to whitepaper the design in some simple form.
It is used for my thought process, not an output.

For a machine to run the Rails app, here is what it needs to installed:

- Nginx
- C compiler
- Required Linux packages: curl, libpq-dev, libvips-dev (or imagemagick), nodejs, git, etc.
- Ruby - of course
- Monit/Systemd

Things aren't necessary when using AWS services
- Ruby version manager - rvm, rbenv, chruby, ruby-install
- iptable, ufw

## A comprehence to-be-done list:

To disappoint you, I won't do that here. But don't too disappointed, here is a tutorial [The great code adventure][2].
I thought Digital Ocean should be the place for those tutorials, turn out they don't focus on that any more.
The last tutorial is a few years ago, using Rails 4 and Ubuntu 14.04, which may be out of date.

1. Add a new user: EC2 usually setup a different user from root, depends on the image, usually is `ubuntu` or
   `ec2-user`.

1. Ensure the server config doesn't allow password login via SSH protocol. Even better, no SSH access at all.
1. Install necessary dependencies
1. Install Ruby from source
1. Install Node JS
1. Setup service monitoring
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

1. Strategy to manage multiple servers
1. Servers don't have public IP
1. Security groups
1. Have a CI/CD tool

### Short comings

The main concern is how to setup a centralize control to the infras.

Currently to maintain the system, there is a must to understand deeply in AWS service.

Last but not least, I am not an expert Devops yet, such things like adding the secret vault to store credential data
isn't a default practice for me, plus I don't want to spend efforts on maintaining the vault.


# Life with Kubernetes

My setup only depends on Kubernetes tool, I don't use Helm.

## A to be done list

To match with previous session format, I start by writing down the list:

1. Setup an IAM account (with permission and role)
1. Create a EKS cluster. Right now the control plane price is 0.1$/hours (72$/month)
1. Create an Ec2 profile. The Fargate profile allows to allocate resource dynamically, use it if your app are
   completely stateless.
1. Install those tools: `kubectl`, `aws`, `eksctl`, `kubefwd`.
1. Setup ECR on AWS to store docker images. Setp up the policy to retain few images to save cost.
1. Create your docker files.
1. Setup postgres cluster with `pgo`. The document isn't very good, need to chew the code to digest
1. Develop a playbook (I use Ansible) to template and build those docker files on CI
1. Setup supports tools (EFK, Kubernetes-dashboard, Caching, etc.)
1. Setup EBS Persistent volume. It creates dynamic volume claim to map into Kubernetes container
1. Setup Nginx ingress controller and ingress for each service.

I don't think this list is enough, hence I talk more.

## Diving in

### Some overview notes

EKS, a short form for Elastic Kubernetes Service, a Kubernetes solution from Amazon Web service.

It is an implementation of Kubernetes that well integrated with existing AWS services. If you have some experiences
working with AWS services before, it definitely helps you in the setup and deploy in EKS.

There is a [great document][6] for step by steps setting up the EKS cluster.

The IAM roles and policy is quite confusing at first.

Use `eksctl` to setup the node group configuration. I don't want to do it manually from the Console UI.

The ENI defines how many pods can run in a node.
When the nodes is started, at least 2 pods are using to run by default. They are proxy and aws-node daemon.

Usually we need to use the persistent storage to store long term data, it means another pod is occupied just right after
the node is started.

Hence around 1 pod left for the small and micro node instance to run your app.
My suggestion is using `medium` or `large` instance to be able to run more pods.

Here is the documment for this constraint: [Amazon Eks AMI][6]

### My setup

Each service is sitting inside a namespace.

Service can access other namespaces' services. A service can be access by `<service_name>.<namespace>`.

My Rails app has those components:

- Main app
- API
- Static Assets
- Background processes
- Cache
- Database
- Config map and Secret
- Ingress

Each component has it own deployment strategy and scaling factor.

The main app, api, background process are using the same Docker image.
More than that, the Static asset is used the assets from the main App.

The Build step manages those relationship of building the Docker images and push to ECR.

While applying Kubernetes manifests, Kubernetes only responses for pulling and firing the deployment with given deployed
command.

As those API, main app, static assets are all stateless component, it can be easily wiped out.
Cache and Database aren't. They must retain the data even the pod is terminated.

### From statefulset to Postgres HA cluster

Statefulset is a sibling of Deployment. Plus it handles the volume template per pod.

If using empty dir, the pod needs to stick with same node. It means if the pod got killed, new pod must be spawned in
the same node.

If using EBS volume, the new pod must be spawned in the same subnet node.

Statefulset is a good candidate for setting up a database and other data store.

However, depending on the statefulset only isn't enough.
Setting up a database cluster requires more works.

Take Postgres cluster as an example. We needs to config the master and replica nodes.
Beside that, we want the balancer to redirect traffic to replicas and master.
Backup and restore is another operation needs to be implemented.

[Pgo][8] is a great tool for that.

I have researched few different approaches and from my opinion, this may be the most complete solution.

### Ingress

Go with Nginx ingress!

There are many, I have tried the AWS ALB and Nginx ingress.

The ALB is quite costly because it fires up new AWS load balancer for each ingress.

Nginx ingress supports only one TLS certificate. Your purchasing cert must be able to use for all subdomains.

You can purchase SSL cert from AWS, it is painless things ever seen.

### Not everything need to expose to outside

We may have a lot of services, but some of them are only used in internal, e.g. the logging, monitoring tool, database
tool, etc. Those tools aren't supposed to be exposed to end users shouldn't be exposed to the end users.

To access them, we can forward the port using the `kubectl port-forward` command.

The `kubefwd` ultility is a great tool that forward all the services to the local machine.
It works like a VPN to the clusters.

With this, the staging environment, CI tools, logging and mornitoring can safely stay away from the internet.

### Other advices

- If it is the first time you have worked with Kubernetes, focus on those important concepts first.
- Making change incremental with a lot of small steps. The achievement will be massive. Don't start with a complicated
  design upfront.
- Document everything. When you find something takes more time than expected, check out the tutorial and online
  resources.
- Bash script is quite useful. You are absolutely able to write some code in bash like below

  ```bash

        echo "-- INFO: Apply manifests"
        echo "-- DEBUG: apply services' manifests"
        kubectl apply -f ./dist/app/${stage}/
        
        echo "-- DEBUG: wait for deployments to be finished"
        for deployment in $(kubectl get deployments -n %env.NAMESPACE% -o='name') ; do
            kubectl rollout status ${deployment} -n %env.NAMESPACE% --timeout=%env.K8S_TIMEOUT%
        done
        
        echo "-- INFO: Run deployment jobs"
        pod_name=$(kubectl get pods --selector=app=app -n %env.NAMESPACE% -o jsonpath="{.items[0].metadata.name}")
        tasks="bundle exec rails db:migrate"
        if [ -n "%env.AFTER_DEPLOYED_TASKS%" ]; then
            tasks="$tasks && %env.AFTER_DEPLOYED_TASKS%"
        fi
        
        echo "-- DEBUG: tasks $tasks"
        kubectl exec $pod_name -n %env.NAMESPACE% -- bash -c "$tasks"
  ```

  Hence Helm and other template tool can be skipped. I currently use `envsubst` to template the manifests

- Setup an EFK (Elastic Search, Fluentd, Kibana) to monitor the cluster. Instead of using the container insight service,
  set it up yourself is quite good for your own learning and cost effective.

- Always config the minimum resources for each deployment.

That's all I got for this post.

Til next time. Take care!

  [1]: https://en.wikipedia.org/wiki/Software_architecture
  [2]: https://www.thegreatcodeadventure.com/deploying-rails-to-digitalocean-the-hard-way/
  [3]: https://www.freedesktop.org/wiki/Software/systemd/
  [4]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html
  [5]: https://www.jetbrains.com/help/teamcity/kotlin-dsl.html
  [6]: https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html
  [7]: https://github.com/awslabs/amazon-eks-ami/blob/master/files/eni-max-pods.txt
  [8]: https://github.com/CrunchyData/postgres-operator
