---
title: "Installing & Configuring CentOS 8 Minimal Install"
date: 2021-05-09T12:19:49Z
draft: false
tags: ["CentOS", "Linux", "Hyper-V"]
cover:
    image: "images/centos.png"
    alt: "<alt text>"
    caption: "Create a CentOS LAB for Kubernetes and Ansible"
    relative: false
---

I wanted to start learning the basics of Kubernetes and thought the best way to get started was by creating a virtualised lab and starting from scratch. But I will not cover k8s in this post. Instead I will detail the initial setup of the CentOS 8 minimal install VM for a lab environment which can be used for k8s or Ansible.

I used Hyper-V on Windows 10, connected to a "Default switch" (internal), and running 3x CentOS 8 VMs. This guide assumes you at least have a _basic_ knowledge of configuring virtual machines and the Linux command line.

[Centos 8 can be downloaded from here](https://www.centos.org/download/)

<br>

## Overview

The network configuration for the VMs in the lab look as follows

| Server      | IP Address        | Gateway      |
| ----------- | ----------------- | ------------ |
| kubemaster  | 172.23.242.120/16 | 172.23.240.1 |
| workernode1 | 172.23.242.121/16 | 172.23.240.1 |
| workernode2 | 172.23.242.122/16 | 172.23.240.1 |

You can be flexible with the IP range above, I simply used the current IP range assigned to my Default Switch network adapter. You can find yours by typing the following command

```powershell
Get-NetIPAddress -InterfaceAlias "vEthernet (Default Switch)" -AddressFamily "IPv4" | Select InterfaceAlias,IPAddress
```

![getIP](images/getIP.png)

<br>

Each VM in the Hyper-V setup is as follows

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

## Installing CentOS 8

1. Power on the first Virtual machine and boot from the CentOS ISO. Start by selecting the language and keyboard layout you want to install

2. Next you will be taken to the __INSTALLATION SUMMARY__ page

![InstallSummary](images/InstallSummary.jpg)

3. Select __Installation Destination__, ensure the local disk is selected then click done.

![disk](images/disk.jpg)

4. Now select __Software Selection__. We want the Base Environment to be __Minimal Install__, select it then click done.

![MinimalInstall](images/MinimalInstall.jpg)

5. Back on the INSTALLATION SUMMARY page, select __Network & Host Name__. First switch __Ethernet (eth0)__ to __ON__, then enter the Host Name as `kubemaster` and click done. NOTE: the image below was taken when creating workernode1.

![Network](images/NetworkSetupGUI.jpg)

6. Select __Root Password__ and create a root password. Click done when complete.

7. Select __Time & Date__ and set your location. Click done when complete.

8. Lastly, select __User Creation__. Enter the __Full name__ and __User name__ as admin, tick "Make this user administrator", and enter a password for it. I set mine the same as the root password as it's only a lab environment. Click done when ready.

![adminSetup](images/adminSetup.jpg)

9. Now click __Begin Installation__. The INSTALLATION SUMMARY page should look as follows:

![InstallSummary2](images/InstallSummary2.jpg)

10. Once the installation is complete you can log into the VM with the `admin` username and password. First job will be to update the OS which can be achieved with the following command:

```terminal
sudo yum update
```

![yumUpdate](images/yumUpdate.png)

After the updates are all installed you can either move onto the next part for the kubemaster VM, or complete part 1 again for workernode1 and workernode2.

<br>

## Setting a Static IP

The next important steps will be to configure static IPs and then configure the hosts file. The following will instruct you how to do this with the kubemaster VM's IP address. To follow this again for the workernodes, change the IP accordingly.

First backup the resolve.conf file:

```terminal
sudo cp /etc/resolv.conf /etc/resolv.conf.backup
```

Next we need to set the static IP in the ifcfg-eth0 file:

```terminal
cd /etc/sysconfig/network-scripts/
sudo vi ifcfg-eth0
```

Press the insert key to modify the file, change BOOTPROTO from `dhcp` to `static` and add the IPADDR as below:

```terminal
BOOTPROTO=static
IPADDR=172.23.242.120
```

Once complete, you can save you changes by pressing the Esc key then:

```terminal
:wq!
```

Next, modify the network file as follows:

```terminal
sudo vi /etc/sysconfig/network
```

Add the following to the file:

```terminal
NETWORKING=yes
GATEWAY=172.23.240.1
```

Once complete, you can save you changes by pressing the Esc key then:

```terminal
:wq!
```

Now reboot the machine. I recommend from here you complete configuring all three VMs before moving onto the next part:

```terminal
shutdown -r now
```

<br>

## Update the hosts File

Updating the hosts files allows for communication over hostname rather than IP address. Change the hosts file on each machine as follows:

```terminal
sudo vi /etc/hosts
```

Add the following:

```terminal
172.23.242.120 kubemaster
172.23.242.121 workernode1
172.23.242.122 workernode2
```

Once complete, you can save you changes by pressing the Esc key then:

```terminal
:wq!
```

<br>

## Generate a SSH Key Pair

To enable us to easily access the worker nodes from the kubemaster for system administration, and possibly configuration management with Ansible at a later date, we can easily setup an SSH key-pair as follows:

From the kubemaster VM, create the ssh key-pair as follows:

```terminal
ssh-keygen -t rsa -b 4096
```

Press Enter to accept the key to be saved in `/home/admin/.ssh` and do not specify a passphrase. Then use the following command to copy to workernode1 and workernode2:

```termial
ssh-copy-id admin@workernode1
ssh-copy-id admin@workernode2
```

Test by connecting to the machine via SSH:

```terminal
ssh admin@workernode1
```

<br>

## Summary

We have now successfully created 3 x CentOS 8 virtual machines with connectivity to each other and the internet. Each machine can ping each other via hostname, and the kubemaster VM can ssh onto both workernode1 and 2.

![vms](images/vms.png)

From here you can begin to install Kubernetes (future post), or install and configure Ansible (using different hostnames, again a future post) for Configuration Management automation.
