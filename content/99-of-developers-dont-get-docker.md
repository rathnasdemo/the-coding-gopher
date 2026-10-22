---
schemaId: urn:gala:schema:content-frontmatter:2.0.0
schemaVersion: 2.0.0
id: 01a16492-a246-7cef-b8f2-a36c1a88deb4
kind: article
title: 99% of Developers Don't Get Docker
description: A container isn't a lightweight VM. It's a process sharing the host kernel through namespaces and cgroups. I walk through how Docker's engine, images, layers and Dockerfiles actually work, plus volumes, orchestration and Podman.
language: en-US
authors:
- 01a16498-1128-725f-af97-840ef616dfdd
tags:
- docker
- containers
- linux-kernel
- virtualization
- devops
- dockerfile
status: published
createdAt: '2026-03-25T12:00:00.000Z'
publishedAt: '2026-03-25T12:00:00.000Z'
slug: 99-of-developers-dont-get-docker
hero:
  path: assets/content-99-of-developers-dont-get-docker-hero.jpg
  alt: Thumbnail of the video "99% of Developers Don't Get Docker"
  role: informative
redirects: []
extensions: {}
---
*Adapted from the video [99% of Developers Don't Get Docker](https://www.youtube.com/watch?v=Sz2ayy2NomY)*

99% of developers don't get Docker.

You've seen the meme. A developer tells a project manager, "It works on my machine," and the PM responds, "Great. We'll ship your machine." Many developers think Docker is just a way to wrap their messy code and dependencies in a box, but they're still wondering why it breaks in production. You're probably building 2 GB images for a simple Node app, hardcoding environment variables, and treating containers like lightweight virtual machines.

But a container isn't a virtual machine. It doesn't need a hypervisor or a bloated guest OS. It's a process shared directly with the host kernel through namespaces and control groups. Today, we're stripping away the black box mystery. We're going to do a deep dive into how Docker engines, images, and layers actually work, so you can stop shipping bloat and start shipping infrastructure.

## Hardware virtualization and the hypervisor

*[Watch from 0:47](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=47s)*

Starting with the foundations: hardware versus OS virtualization. To understand Docker, we have to understand the evolution of isolation and where its predecessor, the virtual machine, fell short.

Hardware virtualization is a technology that uses a software layer called a hypervisor, like ESXi or KVM, to simulate physical hardware: CPU, memory, storage, and network. Each virtual machine runs its own independent operating system and applications, acting like an independent computer, unaware of other virtual machines on the same hardware or physical machine.

The hypervisor sits on the physical hardware and carves it into multiple isolated virtual computers, abstracting software from the underlying infrastructure so virtual machines can operate independently. The guest operating system sitting on top thinks it is running on real bare-metal hardware.

This can be:

- **Full virtualization**, where the guest operating system is unaware it's virtualized, or
- **Para-virtualization**, where the operating system is modified to communicate with the hypervisor for better performance.

## Why virtual machines weren't enough

*[Watch from 1:49](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=109s)*

So why were virtual machines not enough?

- **Resource tax.** Because every virtual machine carries its own full kernel, you lose significant RAM and CPU just to keep the OS alive. Running 10 VMs means 10 copies of the Linux kernel and 10 sets of background drivers wasting resources.
- **Startup latency.** Booting a full operating system takes minutes. In a modern microservices world, waiting minutes to scale up for a traffic spike is unacceptable.
- **Size.** A virtual machine image is usually several gigabytes, making it heavy to store and slow to move across a network.

## The shift to operating system virtualization

*[Watch from 2:25](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=145s)*

Docker virtualizes the operating system instead. Virtualizing the operating system means using containerization to isolate applications by sharing the host machine's OS kernel, rather than virtualizing full hardware like a virtual machine does. Containers act as isolated user spaces, allowing multiple lightweight applications to run independently on one OS.

Containers create isolated user spaces, or namespaces, for processes, libraries, and dependencies, making them faster and more resource efficient than VMs. They're also highly portable: the application and its dependencies are packaged together, ensuring they run consistently across any environment.

Docker also provides resource efficiency. Because they don't require a separate OS, containers use significantly less RAM and CPU compared to full hardware virtualization.

## The lineage of isolation: chroot and FreeBSD jails

*[Watch from 3:15](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=195s)*

The lineage of isolation is built on concepts that existed long before Docker.

**chroot (1979).** This is the oldest ancestor. It's short for "change root," and it allows you to change the apparent root directory for a process and its children. It isolates the file system, effectively creating a chroot jail where a process cannot see files outside its assigned directory, but it is leaky. It doesn't isolate networking, users, or process IDs, and a root user can easily break out of a chroot environment using a second chroot call.

**FreeBSD jails (2000).** This was a massive leap forward that introduced the concept of OS-level virtualization. Jails didn't just isolate the file system. They partitioned the network stack, giving each jail its own IP, the user subsystem, and the process tree. Each jail has its own root user and hostname, but they all share the same FreeBSD kernel. It proved that you could have high-density isolation without the overhead of a VM.

Docker took these concepts to the Linux kernel to create a workflow where a container carries exactly what it needs, like a specific version of Node.js 16 and OpenSSL, without touching the host's global libraries.

## What Docker actually is

*[Watch from 4:29](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=269s)*

So what is Docker exactly? At its core, Docker is a platform designed to package, distribute, and run applications in standardized units called containers. It acts as the translation layer between your code and the infrastructure, providing a consistent interface for managing the software life cycle.

When people say Docker, they are usually referring to four distinct things:

1. **The Docker Engine**, the heart of Docker. It is a client-server application consisting of a long-running background daemon process, dockerd; APIs that specify interfaces for programs to talk to the daemon; and a CLI client.
2. **The Dockerfile.** This is a text-based manifest that defines the source of truth for your environment. It documents every step required for your app to run, making your environment infrastructure as code.
3. **Images.** This is the blueprint of your application. An image is a read-only executable package that includes everything needed to run an application: code, runtime, libraries, environment variables, and config files. When you run an image, it becomes a container.
4. **Docker Hub.** This is a centralized registry, similar to GitHub but for binary images, that hosts official security-scanned images for databases like PostgreSQL, web servers like Nginx, and runtimes like Node.js.

## A word from the sponsor: Depot

*[Watch from 5:54](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=354s)*

Before we look at how the Linux kernel actually draws these isolation boundaries, let's talk about another place you're quietly bleeding engineering cycles: your CI/CD pipeline. Just like wrestling with environment drift is a massive waste of your time, so is staring at a terminal waiting 20 minutes for a Docker image to build. That context switching completely destroys developer momentum.

If you are building multi-architecture container images in standard CI environments like GitHub Actions, you already know the pain of relying on slow, buggy software emulation like QEMU. That's why you need to look at Depot, the sponsor of this video. Depot is a drop-in replacement for your Docker builds that makes them up to 40 times faster. Instead of wrestling with standard CI runners, Depot routes your builds to remote machines equipped with native Intel and ARM processors, completely eliminating the need for slow emulation.

But the real secret weapon is their caching. Instead of wasting time saving, compressing, and loading cache layers over a network, which sometimes takes longer than the build itself, Depot uses a shared, blazingly fast NVMe cache that is instantly available across all of your parallel builds.

There is no complex migration. You literally just swap docker build for depot build in your workflow, and your pipeline is instantly faster. Stop burning money on CI minutes and shattering your team's focus. Go to depot.dev to get your time back.

## Namespaces: what a process can see

*[Watch from 7:21](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=441s)*

Now, back to how the kernel makes Docker possible. Let's talk about the anatomy of isolation and kernel primitives. The Linux kernel draws these boundaries using two secret weapons.

First is namespaces, which are about isolation, or what a process can see. Namespaces provide the virtual reality goggles for a process. In Linux, many resources are global, like the list of all running processes or the network card. Namespaces wrap these global resources in an abstraction so that a process inside a namespace thinks it has its own private, isolated instance of that resource.

- **PID namespace.** On your host, your app might be process ID 4502, but inside the container's PID namespace, the app sees itself as PID 1, the system's init process. It cannot see or interact with any processes outside its own bubble.
- **Net namespace.** It provides a private network stack, including its own IP address, routing table, and firewall rules. This is why you can run three separate containers all listening on port 80 without a "port already in use" error. Each is on its own private network.
- **MNT, or mount, namespace.** This isolates the mount points. The process sees a completely different file system root, or slash, than the host machine. It cannot see the host's /etc/shadow or /home folders unless you specifically mount them.
- **UTS namespace.** This allows the container to have its own hostname and domain name, separate from the host machine. A container hostname is just a unique label assigned to a container within a network, identifying it for inter-container communication, and in Docker it typically defaults to the container ID.

## Control groups: what a process can use

*[Watch from 9:01](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=541s)*

Then we have control groups, or cgroups. If namespaces are about what you can see, cgroups are about what you can use. They set hard limits on RAM and CPU, which prevent a single buggy container from crashing your entire prod node.

## The union file system and layers

*[Watch from 9:18](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=558s)*

Now let's talk about the union file system. A Docker image is tiny because of the union file system. Images are composed of layers. Each instruction in a Dockerfile creates a new layer, and these are stacked and treated as one system. If you have three apps based on Ubuntu 24.04, Docker stores one read-only layer of Ubuntu on your disk, shared by everyone.

When a container is launched, Docker adds a thin, unique writable layer on top of the immutable image layers. All changes made within that specific container are written to this top layer. This is the copy-on-write strategy: if the app changes a file, Docker copies it to the top layer and modifies it there, leaving the base image untouched.

## How copy-on-write works

*[Watch from 10:00](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=600s)*

Let's dive a little deeper into the copy-on-write mechanism.

- **Reading.** If an application needs to read a file, Docker first looks in the writable layer. If it's not there, it accesses the file from the shared read-only layers below.
- **Writing.** The first time an application modifies an existing file from a lower layer, Docker's storage driver performs a copy-up operation, copying the file to the container's unique writable layer. The modification then happens on this copied file in the writable layer, leaving the original file in the read-only layer untouched and available for other containers to use.

This ensures each container has its own isolated data state while maximizing efficiency. In turn, it provides:

- **Reduced storage consumption.** Because images are shared, launching 10 containers based on a 1 GB image does not require 10 GB of storage. It only requires the base 1 GB plus small individual write layers.
- **Fast container startup.** Containers start almost instantly because they do not need to copy the entire file system image, only create a new empty writable layer.

## Designing the build: optimizing the Dockerfile

*[Watch from 11:06](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=666s)*

Now let's talk about designing the build, or optimizing the Dockerfile. A professional Dockerfile is optimized via layer caching. Docker caches each line, and if you change a line, Docker invalidates the cache for that line and everything below it.

Let's walk through a sample Dockerfile, instruction by instruction:

- **FROM** sets the base image. This is the foundation of your stack. Choosing a small base like Alpine significantly reduces your image size and attack surface.
- **WORKDIR** sets the execution context. Any following RUN, CMD, or COPY instructions will happen inside this folder, ensuring your file structure is predictable.
- **COPY** moves files from the host machine into the image. By copying package.json before the rest of the code, we ensure npm install only reruns if our dependencies actually change.
- **RUN** executes commands in a new layer on top of the current image and commits the results. This is used to install packages or build your application.
- **ENV NODE_ENV=production** sets an environment variable named NODE_ENV to the value production within the image. This is standard practice for Node.js applications, as it triggers several optimizations, such as improved performance, disabled development-only warnings and logging, and the omission of development dependencies during installation. That produces a smaller final image.
- **EXPOSE 3000** informs Docker that the container is expected to listen for traffic on TCP port 3000.
- **CMD** provides the default for an executing container. Unlike RUN, which executes during the build, CMD is the entry point of the app once the container is actually launched. In this case, it just runs the server.js file as a Node.js program.

## Data persistence with Docker volumes

*[Watch from 12:49](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=769s)*

Now let's talk about data persistence and orchestration, starting with Docker volumes. By default, data inside a container is ephemeral. If the container is deleted, the data is gone.

Volumes are the preferred mechanism for persisting data generated by and used by Docker containers. They are stored on the host but managed by Docker, allowing you to swap out containers without losing your database records or uploads.

## Orchestration: Swarm versus Kubernetes

*[Watch from 13:12](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=792s)*

For orchestration, let's touch on Swarm versus K8s. As you scale from one container to hundreds, you need orchestration.

- **Docker Swarm** is Docker's native orchestration tool. It's easier to set up and great for smaller, simpler clusters.
- **Kubernetes**, also known as K8s, is the industry standard. It is highly complex, but offers immense power for automated scaling, self-healing, and managing massive distributed workloads.

## Docker versus Podman

*[Watch from 13:37](https://www.youtube.com/watch?v=Sz2ayy2NomY&t=817s)*

There's also the question of Docker versus Podman.

- **Architecture.** Docker uses a client-server architecture with a centralized daemon known as dockerd. Podman, on the other hand, is daemonless. It uses a fork-exec model where the CLI directly interacts with the OCI, or Open Container Initiative, runtime.
- **Security.** For Docker, the daemon typically runs as root, posing a single point of failure and a higher security risk. Podman is rootless by default, significantly reducing the potential attack surface.
- **Process model.** With Docker, all containers are managed by a single persistent background service, while with Podman, each container runs as a regular isolated user process, manageable with standard Linux tools like systemd.
- **Multi-container workflows.** Docker primarily uses Docker Compose for multi-container orchestration, which requires creating a separate YAML file. Podman is built with Kubernetes in mind: it supports native pods, and it can automatically generate Kubernetes-compatible YAML files from local workloads.

*[Watch the full video on YouTube](https://www.youtube.com/watch?v=Sz2ayy2NomY)*

This article is adapted from the video "99% of Developers Don't Get Docker" by The Coding Gopher ([https://www.youtube.com/watch?v=Sz2ayy2NomY](https://www.youtube.com/watch?v=Sz2ayy2NomY)), licensed under [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). It was transcribed and edited into an article by Galascribe.
