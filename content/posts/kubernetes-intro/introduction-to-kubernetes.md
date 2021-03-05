---
title: "Introduction to Kubernetes"
date: 2021-02-26T12:19:49Z
draft: true
---

I wanted to start learning the basics of Kubernetes. In this first post I will detail the initial setup of the Virtual Machines in a lab environment. I used Hyper-V on Windows 10, connected to a "Default switch" (internal), and running 3x CentOS 8 as the Kubemaster and worker nodes.

This guide assumes you at least have a _basic_ knowledge of configuring virtual machines and the Linux command line.

[Part 1 Lab Setup](https://markkerry.github.io)
[Part 2 Installing Kubernetes](https://markkerry.github.io)

[Centos 8 can be downloaded from here](https://www.centos.org/download/)

Network configuration

| Server      | IP Address      | Gateway     |
| ----------- | --------------- | ----------- |
| kubemaster  | 172.26.32.19/16 | 172.26.32.1 |
| workernode1 | 172.26.32.20/16 | 172.26.32.1 |
| workernode2 | 172.26.32.21/16 | 172.26.32.1 |

Hyper-V setup. 3 x Virtual Machines as follows

| Hyper-V Configuration | Value    |
| --------------------- | -------- |
| VM OS                 | CentOS 8 |
| vCPU                  | 2        |
| Ram                   | 2048 MB  |
| Disk size             | 20 GB    |
| Generation            | 2        |
| Secure Boot           | Disabled |
| Automatic Checkpoints | Disabled |
