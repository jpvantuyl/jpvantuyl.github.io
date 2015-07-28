---
layout: post
title: "How I think about Docker"
date: 2015-07-29 -0800
comments: false
categories: [automation, docker, linux, pluralsight]
---

The first explanation that crystallized into a mental model was Carl Franklin's from the .NET Rocks [docker podcast](https://www.dotnetrocks.com/default.aspx?ShowNum=1077).  It helps that he's repeated it repeatedly on subsequent podcasts when the subject has come up.

> Docker is like a VM with the weight of a process.

This is true as far as it goes.  They're both isolation mechanisms that allow you to host more than one sandboxed environment on a machine.

This weekend I watched Nigel Poulton's [Docker Deep Dive](http://www.pluralsight.com/courses/docker-deep-dive) on Pluralsight.  It was nice to see someone step through all the Linux commands for creating and maintaining Docker images and instances.  But above and beyond that, it was helpful to flesh out my anemic mental model.  Here's a few tidbits that came to light.

##Build Process

I'd heard that Dockerfiles allow you to version your servers just like you version your source code.  From Nigel's course, I learned that the contents of that Dockerfile are transformed into a final server image by executing the Docker build process.  Given a base image as a starting point, each line in the Dockerfile modifies the filesystem then the build process takes a snapshot image of the changes and moves on to the next instruction in the Dockerfile.

##Image Layers

I already knew that you can download a Docker image from the Docker Hub repository.  It had the look and feel of downloading a package using a package manager but for an OS and the rest of the environment.  A lot like my first experience of `npm` actually.  What I hadn't grasped fully until I saw Nigel's course was that beneath the surface, each of those images is composed of many different layers during the Docker build process.

The similarities to `git` helped inform my mental model.  In both tools, we're taking snapshots as we go.  You can see a history of all snapshots and how they relate to each other using the built-in tools.  If you do so, each looks like a directed graph where each node is a step from the past state into the future state.

##Union Mounts

Docker relies on a union mounted filesystem to merge together all these image layers.  Something like `aufs` on Linux allows Docker to superimpose the files of one layer upon another so that when the OS opens a file, it gets the most recently modified version.  If I've got 7 layers in my favorite Docker image, then it'll search the top-most layer for my file and if it doesn't find it, the OS will search the next layer down.

It's always safe, and relatively fast, to read such a filesystem but what about writing to it?  All filesystem layers except the top-most one are set to read-only so when we want to write to a file the OS makes a copy of it at the top-most layer and edits that.  We call this behavior copy-on-write.  Ayende Rahien has a good description on his blog.

##PID 1

Docker assumes that you'll only be using one process inside the container.  That process is assigned a process ID of 1 (PID 1).  In Unix-land it seems that PID 1 is usually an `init` process that bootstraps and orchestrates other processes but in Docker-land it's a little less-so.  Still, Docker starts each container with something at PID 1 and will shut the container down when that process exits.  Picking which process to use is a whole lot easier when there's only one to choose from; for anything more complicated you should either reconsider your direction or do you homework.

##A Network Switch in the Kernel

This one made sense to me from my time with Hyper-V.  To get network packets from the host to the client (and back) you need a virtualized switch.  Docker has one built into the Linux kernel to support this level of transport.  Only the kernel had the level of visibility necessary to mediate communication with the sandboxed containers.

##Where Next?

Getting the lowdown on the Linux implementation of Docker provokes some interesting questions regarding the Microsoft implementation.  The network virtualization elements seem really well baked in Hyper-V so I'm assuming that'll be a piece of cake to build into the next Windows OS.  The union mount filesystem seems harder to pull off.  I'm not sure if Microsoft can add that feature to NTFS or if they need to build something from scratch.  Time will tell, they've announced that they'll support Docker containers so I'm sure they have a plan.