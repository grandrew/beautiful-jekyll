---
layout: post
title: Setting up a chroot jail
subtitle: Step-by-step instructions to securely share CPU on your production system
---

Using a chroot jail can help to leverage potential threats that may arise from a misconfigured system and is very easy to set up. Like if you unknowingly end up having sensible data read access granted to `junknobody` users - chroot will prevent these users from accessing your data. 

This is useful when you want to share the unused CPU time on some (non-critical of course) production system and you want it the easiest way. Other ways include setting up a docker container or a virtual machine - these may require additional work like kernel replacement.

We will use [jailkit](http://olivier.sessink.nl/jailkit/) to set up a secure chroot and then configure SSH daemon to chroot all `junknobody` users.

## 1. Install jailkit and configure chroot

Issue these commands as root user:

~~~
cd /tmp
wget http://olivier.sessink.nl/jailkit/jailkit-2.19.tar.gz
tar -zxvf jailkit-2.19.tar.gz
cd jailkit-2.19
./configure
make
make install
~~~

Now we will create a chroot jail at `/var/jail`. Make sure you have at least 5GB free space availabe per CPU core there.

~~~
mkdir /var/jail
chown root:root /var/jail
jk_init -v /var/jail basicshell
jk_init -v /var/jail netutils
jk_init -v /var/jail ssh
~~~

Now make sure that nothing in your /home directory is world-readable:

~~~
chmod o-r,o-x,o-w /home/*
~~~

And mount the /home and passwd to chroot:

~~~
mkdir /var/jail/home
mount -o bind /home /var/jail/home
mount -o bind /etc/passwd /var/jail/etc/passwd 
~~~

Now you may want to add this line to your `/etc/fstab` to make the mount happen at boot automatically

~~~
/home /var/jail/home        none    defaults,bind 0 0
/etc/passwd /var/jail/etc/passwd        none    defaults,bind 0 0
~~~

## 2. Configure SSH

Add these lines line to your `sshd_config` file, usually at `/etc/ssh/sshd_config`:

~~~
Match group junknobody
          ChrootDirectory /var/jail/
~~~

And restart sshd service, on ubuntu you would do `service ssh restart`.

## 3. Install jusy-server script

Now you will want to head to [Node Owner sign-up](/node) to get your `Node Owner Hash`, and issue these commands:

~~~
wget https://raw.githubusercontent.com/junk-systems/jusy/master/jusy-server.py
python jusy-server.py --install-cronjob --daemon <Owner Hash here>
~~~

On ubuntu, debian or centos -based distributions that will install and set everything up. On SUSE linux you may require to manually add this script to start up at boot.

You can check the status by calling `tail /var/log/jusy.log`.

## Final notice

Please remember to always have the latest kernel installed as your security relies on the security of linux kernel.