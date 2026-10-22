---
schemaId: urn:gala:schema:content-frontmatter:2.0.0
schemaVersion: 2.0.0
id: 01a16493-7864-739b-ba9b-e135dffbb359
kind: article
title: You'll Understand Terraform After Watching This Video
description: Terraform turns infrastructure into software. I cover what it is, why declarative code beats clicking, the init-plan-apply-destroy workflow, how core and providers work under the hood, and why it beat the alternatives.
language: en-US
authors:
- 01a16498-1128-725f-af97-840ef616dfdd
tags:
- terraform
- infrastructure-as-code
- devops
- cloud-computing
- hashicorp
status: published
createdAt: '2026-02-25T12:00:00.000Z'
publishedAt: '2026-02-25T12:00:00.000Z'
slug: youll-understand-terraform-after-watching-this-video
hero:
  path: assets/content-youll-understand-terraform-after-watching-this-video-hero.jpg
  alt: Thumbnail of the video "You'll Understand Terraform After Watching This Video"
  role: informative
redirects: []
extensions: {}
---
*Adapted from the video [You'll Understand Terraform After Watching This Video](https://www.youtube.com/watch?v=AYpGVdZFBHQ)*

Stop clicking. You think Netflix or Uber spins up 5,000 servers by clicking "create instance" 5,000 times?

No. They define a blueprint. They run a single command, and an entire data center appears out of thin air. That is why Terraform is everywhere. It is the only way to survive at scale. It turns your infrastructure into software. If you are still building servers by hand, you are building a house of cards. Stop being a server mechanic and become a cloud architect.

## What Terraform is

*[Watch from 0:30](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=30s)*

At its core, Terraform is an open source infrastructure as code tool created by HashiCorp. It allows you to define, provision, and manage cloud infrastructure using human-readable configuration files. Think of it as a blueprint for your data center.

Instead of manually clicking buttons in the AWS, Azure, or Google Cloud consoles, which is slow and hard to replicate, you write a text file describing your desired end state. Terraform then reads that file and handles the complex logic to make it a reality.

## Declarative, not imperative

*[Watch from 0:58](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=58s)*

A key distinction of Terraform is that it is declarative, not imperative.

- **Imperative**, or scripting, would involve telling the computer *how* to do something. For example: log in, click "create server," wait 5 seconds, and then install the update.
- **Declarative**, which is what Terraform is, means you tell the computer *what* you want. For example: I want three servers with a load balancer like Nginx.

Terraform figures out the how and the order of operations for you.

## Why use Terraform

*[Watch from 1:25](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=85s)*

Managing infrastructure used to be manual, via "ClickOps," and very error-prone. If you needed 10 servers, you created them one by one. If a configuration drifted (say a junior engineer manually opened a firewall port for testing and forgot to close it), it was nearly impossible to track.

Terraform solves these specific engineering challenges:

1. **Idempotency.** You can run the same Terraform code multiple times, and the result remains the same. If the server already exists, Terraform won't create a second one. It simply verifies the existing one matches your code.
2. **Infrastructure as code.** Your infrastructure is stored in Git. This enables version control, meaning you can roll back to previous infrastructure versions if a deployment breaks. It also enables code reviews: peers can review infrastructure changes via pull requests before they go live. And finally, audit trails: you know exactly who changed what and when.
3. **It is platform agnostic.** While you write specific code for AWS or Azure, Terraform provides a unified workflow for all of them. You can even manage multiple clouds, for example AWS for compute and Cloudflare for DNS, in a single configuration.

## The core workflow

*[Watch from 2:36](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=156s)*

So how does it work? Terraform follows a strict life cycle that ensures safety and predictability:

1. **terraform init.** This is the initialization step. Terraform scans your code, identifies which providers you are using (for example, AWS), and downloads the necessary plug-in binaries to a hidden `.terraform` folder.
2. **terraform plan.** This is a safety check. Terraform compares your code against your current live infrastructure and creates an execution plan. It shows you a diff, green for additions and red for deletions, of exactly what will happen. This is a read-only operation.
3. **terraform apply.** This is the execution. If the plan looks correct, you confirm it. Terraform makes the API calls to the cloud provider to build or modify the infrastructure to match your code.
4. **terraform destroy.** This is the cleanup. When an environment like a testing sandbox is no longer needed, this command reads your state file and removes all associated resources efficiently.

## Under the hood: core and providers

*[Watch from 3:40](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=220s)*

To understand why Terraform is so reliable, you need to look at its internal architecture. It isn't just one big program; it's a modular system.

**First, the Terraform core, or the brain.** This is the static binary you download from HashiCorp. It is the engine that drives everything, but remarkably it knows absolutely nothing about AWS, Azure, or Google Cloud. Its job is strictly logical:

- **It reads your config** by parsing your .tf files and validating the syntax.
- **It manages state** by maintaining the terraform.tfstate file, which acts as its map of reality. By reality, I mean the actual live infrastructure currently running in the cloud. The core uses this file to link your abstract code, like a resource called web server, to the specific real-world ID, like the AWS ID i-0123456789. If you delete this file, Terraform loses its memory and forgets that your servers even exist, often requiring manual cleanup.
- **It calculates the resource dependency graph**, a directed acyclic graph. It figures out that the database needs the subnet, which needs the VPC.

**Second, the providers, or plugins.** These are separate binaries, executable files that the core downloads during terraform init. The AWS provider, for example, is a Go binary that knows exactly how to make API calls to AWS. Since the core doesn't know AWS or Azure or Google Cloud, it uses these plugins, which are small binaries that the core talks to via RPC.

The core says, "I need a resource of type AWS instance created." The AWS provider says, "Okay, understood. I will translate that request into a RunInstances API call and send it to the AWS endpoint."

This separation is why Terraform is so powerful. HashiCorp updates the core to be smarter, while AWS updates their provider to support new features, and they work together seamlessly.

## The dependency graph and parallelism

*[Watch from 5:40](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=340s)*

When you run terraform plan, Terraform builds a mathematical representation, a directed acyclic graph, of all your resources. It looks at your code and sees that the EC2 instance refers to the subnet ID. Therefore, it knows the subnet must exist before the EC2 instance. It walks this graph to determine the exact order of operations.

Because it has this graph, Terraform can identify resources that don't depend on each other and create them in parallel, for example creating a database and a load balancer at the same time, which speeds up your deployment significantly.

## The components that power Terraform

*[Watch from 7:22](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=442s)*

To master Terraform, you must understand the components that power it.

- **Providers.** Terraform is just the core engine. It doesn't know how to talk to the cloud by default. It uses providers, or plugins, to translate Terraform syntax into specific API calls. Note that providers are not just for clouds. There are providers for Kubernetes, Datadog, GitHub, and even ordering Domino's Pizza.
- **Resources.** A resource is the most important element. It describes a specific infrastructure object. It uses syntax like `resource` followed by the type and the local name. For example, AWS S3 bucket could be the type, and it could be named my data bucket, which is the local name used to reference it elsewhere in code.
- **State.** This is the brain of Terraform and the most critical concept. Terraform keeps track of the infrastructure it manages in a file called terraform.tfstate. It maps your code (for example, resource A) to the real-world ID. It caches resource attributes so Terraform doesn't have to query the cloud API for every single detail every time. In prod, you never store this file on your laptop. You store it remotely, for example inside an AWS S3 bucket, so your entire team shares the same view of reality. This also enables state locking, which prevents two people from modifying infra at the exact same time.
- **Modules.** As you scale, putting everything in one single file becomes messy. Modules allow you to group resources together into reusable components. You can write a web server module once and reuse it for your staging, QA, and prod environments just by passing different parameters.

In a more realistic setup, you'd use variables to avoid hard coding and outputs to return useful data.

## Terraform versus tools like Ansible

*[Watch from 9:09](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=549s)*

It's common to be confused by the landscape of tools, so here's a breakdown.

- **Terraform is for provisioning.** It's best at creating the skeleton of your infra: the VPCs, subnets, load balancers, and bare servers. It follows an immutable model. If a server is broken, destroy it and build a new one.
- **Ansible, Chef, Puppet, and so on are for configuration management.** They're best at fleshing out the servers. Once the server exists, these tools install software, patch kernels, and manage user accounts. They often follow a mutable model, which means updating the existing server in place.

Most modern companies use Terraform to spin up the infra, including Kubernetes clusters, and then use tools like Helm or Ansible to manage the applications running inside.

## Why Terraform won

*[Watch from 9:55](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=595s)*

But you might be wondering: why Terraform? Why not just use AWS CloudFormation or Ansible? Terraform didn't just get lucky. It solved three specific problems better than anyone else.

1. **The cloud agnostic advantage.** Tools like AWS CloudFormation or Azure ARM templates are powerful, but they lock you into one vendor. If you learn CloudFormation, you only know AWS. Terraform is universal. You learn one syntax, HCL, and you use it to manage AWS, Azure, Google Cloud, Kubernetes, Datadog, and even GitHub. It allows you to build a multi-cloud strategy without learning five different languages.
2. **The immutable mindset versus configuration management.** Tools like Ansible, Chef, and Puppet were designed for a different era. They focus on changing existing servers, i.e. mutable infrastructure: installing updates, patching configs, and tweaking settings. Terraform focuses on replacing servers, i.e. immutable infrastructure. In the cloud era, if a server acts up, you don't spend hours fixing it. You shoot it and you spin up a new perfect copy. Terraform is built native to this disposable mindset.
3. **The massive ecosystem: the provider model.** Because Terraform is open source and plug-in based, the community exploded. Need to manage a firewall? There's a provider for that. Need to configure a Spotify playlist? There's also a provider for that. This pluggable architecture meant that Terraform grew faster than any single company could build it. It became the standard API for the entire internet.

## Wrapping up

*[Watch from 11:22](https://www.youtube.com/watch?v=AYpGVdZFBHQ&t=682s)*

To wrap up, Terraform is the bridge between your code and the cloud. It replaces the fragile chaos of manual ClickOps with a single version-controlled blueprint.

- **It is declarative.** You define the what, the end goal, and Terraform figures out the how, the API calls.
- **It is state aware.** It manages the terraform.tfstate file to track the reality of your infra, ensuring that what is running in the cloud matches exactly what is written in your code.
- **It is universal.** Whether you are managing AWS, Azure, or Kubernetes, the workflow is identical.

Please subscribe if you enjoyed, and happy coding.

*[Watch the full video on YouTube](https://www.youtube.com/watch?v=AYpGVdZFBHQ)*

This article is adapted from the video "You'll Understand Terraform After Watching This Video" by The Coding Gopher ([https://www.youtube.com/watch?v=AYpGVdZFBHQ](https://www.youtube.com/watch?v=AYpGVdZFBHQ)), licensed under [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). It was transcribed and edited into an article by Galascribe.
