---
title: "Introduction to Kubernetes"
date: 2021-02-28T12:19:49Z
draft: true
tags: ["Kubernetes", "CentOS", "Hyper-V"]
---

I wanted to start learning the basics of Kubernetes. In this first post I will detail the initial setup of the Virtual Machines in a lab environment. I used Hyper-V on Windows 10, connected to a "Default switch" (internal), and running 3x CentOS 8 as the Kubemaster and worker nodes.

This guide assumes you at least have a _basic_ knowledge of configuring virtual machines and the Linux command line.

Related posts:

[Kubernetes - Part 1 Lab Setup](https://markkerry.github.io/posts/kubernetes-part-1/)

[Kubernetes - Part 2 Installing Kubernetes](https://markkerry.github.io/posts/kubernetes-part-2/)

[Centos 8 can be downloaded from here](https://www.centos.org/download/)

<br>

The network configuration for the VMs in the lab look as follows

| Server      | IP Address      | Gateway     |
| ----------- | --------------- | ----------- |
| kubemaster  | 172.26.32.19/16 | 172.26.32.1 |
| workernode1 | 172.26.32.20/16 | 172.26.32.1 |
| workernode2 | 172.26.32.21/16 | 172.26.32.1 |

And each VM in the Hyper-V setup is as follows

| Hyper-V Configuration | Value    |
| --------------------- | -------- |
| VM OS                 | CentOS 8 |
| vCPU                  | 2        |
| Ram                   | 2048 MB  |
| Disk size             | 20 GB    |
| Generation            | 2        |
| Secure Boot           | Disabled |
| Automatic Checkpoints | Disabled |

<br>

In [part 1](https://markkerry.github.io/posts/kubernetes-part-1/) we'll look at setting up the lab
