---
layout: page
title: Node Owner
subtitle: Free heat from your PC
---

## Requirements

1. A **decent CPU** that is about AMD 1090T performance per core. This is the main strict requirement.
    - jusy-server script will automatically test your CPU for compatibility, you may check the result in the log at `/var/log/jusy.log`
2. At least **2GB RAM per CPU** core
3. At least **10GB per core** free space on disk
4. At least **10 mbit/s** internet bandwidth
3. A **root-like access** to **x86-64** Linux distribution
    - Almost any 64-bit x86-based Linux with python 2.7+ is okay, but people usually expect something like Ubuntu LTS and may cancel orders if they find your system inappropriate. Generally virtual machines, docker and any other containerization is okay as soon as you have a root access.
4. **SSH server** configured for **pubkey access** (this is usually true for any server linux distribution, but you may require to configure this manually if you have a custom environment)
5. *Recommended*: `iptables` and `acct` packages with `bsd process accounting` enabled in kernel and ability to mount loopbacks for additional network security and precise accounting

## Installation

You need to [sign up](https://junk.systems/node) first to get the `Owner Hash`. Once you have the `Owner Hash` and a login with requirements met, you can issue these shell commands to start hosting remote users:  

~~~
$ wget https://raw.githubusercontent.com/junk-systems/jusy/master/jusy-server.py
$ sudo python jusy-server.py --install-cronjob --daemon <your Owner Hash here>
~~~

You can host as many nodes as you want. This command is available for copy-paste in the Node Owner interface.

You may want to set up a [chroot jail]({% post_url 2016-07-05-Share-Chroot-Jail %}) or a virtual machine but it is absolutely unnesessary if you just want to share your entire machine.

## Updates

The script tries to install itself at `/opt` and write cron job to start on boot and update itself periodically.

## Security Considerations

The security of your system mostly depends on the security of Linux kernel and system configuration, so you should at least always keep the kernel updated with the latest security patches. If you are not sure that your system is properly configured (which is usually true for development and test machines) you may want to follow [instructions to set up a chroot jail]({% post_url 2016-07-05-Share-Chroot-Jail %}) or launch a virtual machine or a docker container.

It is generally not recommended to host a node if you are not comfortable with the possibility that you environment can be compromised or may require reboot and reinstall.

However, the script tries to apply the following limits per client:

1. Process count no more than 50
2. RAM - no more than 2GB
3. Disk - 5GB (loopback device mounted as user home)
4. Network access only ports 22 for SSH and 53 for DNS resolve

It is up to you if you want to anonymize your network - in which case you may want to implement a transparent TOR router or route your traffic through an anonymizing gateway.

## Accounting

Clients are charged for CPU time only. That means that 1 CPU-hour on a 8-core machine can be used in 1/8 of an hour, or in 7 minutes 30 seconds. Howerver, if the system load is high - some users may not get full CPU time and their session will last more than 1 hour. There is a general limit of 3 hours per session.

Junk Systems is currently responsible for setting the computation price and transfer fee but we try to make the fees as low as possible and prices that allow for the cheapest PC investment to be profitable within less than a year.

If the SSH session is interrupted for any other reason than normal finish (normal is either 1 CPU hour reached or 3 wall-clock hour passed) - the order will become orphaned and its status will be decided at the end of the operation month based on your rating. 

You may check out [earnings calculator](https://jscalc.io/calc/znUKnWTrXZtFnl9t) to estimate your profits.

## Rating System

Currently we are testing our ratings so it may not work at all. 

But you can think of it as an Uber driver rating: the more cancellations, disconnects or complaints you get - the less chances that you will get a paid order. If your rating becomes less than a certain threshold - you will have to operate free of charge until your rating becomes high again. You will have to operate for free when you first sign up to gain rating when our rating system kicks in.

## Bitcoin Transfer

You can request a transfer-out of your earned bitcoins to your BTC address after you have at least $5 worth of bitcoin on your account. Requests for lower than this will be ignored.