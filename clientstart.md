---
layout: page
title: Compute Client
subtitle: Instant x86_64 SSH shells for computation 
---

## How it works

You buy CPU time in 1 hour chunks and get a non-privileged linux user SSH shell at random nodes. 

It means that each session that you buy is counted in CPU time, not in wall-clock time. So on a 8-core machine if you run 8 processes you can use your 1 hour in 1/8 of time (or about 7 min 30 sec). On the other hand, if the system is at heavy load your task may take more than one wall clock hour to complete. In either way, the session is limited to 3 hours max.

The system sets these limits per session:

- maximum of 2GB of RSS ram consumption
- max 50 processes
- 5GB disk
- only TCP port 22 outbound connections are allowed

If you exceed the limits the session will be dropped with no refund.

To start using, please [Sign up](https://junk.systems/client/) and copy your `Client Hash`.

## How to use

You request credentials by issuing a `GET` request to 

~~~
https://proxy.junk.systems/order/<your Client Hash>
~~~

where `Client Hash` is your identity thay you can find in the [Client Login](https://junk.systems/client/) area. The reply is a `JSON` object with information of how to access the SSH shell. You will be immediately charged for 1 CPU-hour. The order will be cancelled automatically if you close the connection within 20 seconds (or will not connect at all).

The JSON object has the following structure: 

```python
{
    'status': 'OK', 
    'username': 'jsuser84807110', 
    'order_id': '577bd0f4921d34cf3d122a0c', 
    'host': 'proxy.junk.systems', 
    'port': 51642, 
    'privkey': '<SSH private key here>'
}
```

which gives the full credentials to the remote SSH shell. You can log in using 

~~~
ssh -i <file you saved privkey to> -l <username> -p <port> <host>
~~~

You can use the provided private key at the `privkey` field as ssh identity or you can log in using your own ssh public key that you can set in the Client interface.

We provide an example helper python script [here](https://github.com/junk-systems/jusy/blob/master/jusy-client.py) to show how it can be used.

~~~
$ wget https://raw.githubusercontent.com/junk-systems/jusy/master/jusy-client.py
$ python jusy-client.py <Your Client Hash>
...
juser234253@remote:~ $
~~~

## Example project

You can find a demo project based on [execnet](http://codespeak.net/execnet/index.html) distributed python execution framework at the [Junk Systems Github repository](https://github.com/junk-systems/jusy).

## Machine types and specs

Generally it is up to you to decide whether the machine is suitable for your needs. The only testing we currently do is checking that the node can execute a PRNG-test within a fixed amount of time which is roughly equivalent to a single AMD 1090T CPU core.

You can check the machine requirements for Node Owners at [Getting started for Node Owner](/nodeowner) page.

## Registration

You need to sign up at the [Client web interface](/client) and add some funds to your account to start using the service.

We use bitcoin as system currency to support anonymity on both sides. You can buy bitcoins directly at [Coinbase](https://www.coinbase.com) which we use to handle transactions.

## Sending tasks

You may use different clustering techniques like BOINC to send tasks to nodes, or implement your own method. For example, you can send portable binaries to execute or rely on tools that may be available on the node.

## Master SSH connection 

You may want to open a [master SSH channel](http://unix.stackexchange.com/questions/33557/using-an-already-established-ssh-channel) to be able to open multiple shells as when you close the connection your session is finished and user data is deleted.

## Security Considerations

- Your data, algorithms and computation results are seen to the Node Owner and potentially to other parties
- Your computation results are not reliable as they may be compromised or corrupted

So generally you will want to send anonymous data and implement result checking or redundancy for all the tasks.

## Rating System

We will gradually roll out a rating system with the feel of uber car-sharing ratings: if the Node Owner drops sessions, has a slow machine or otherwise misbehaves he will be either automatically or manually voted down and punished. On the other hand, we will introduce rating system for Clients to filter out abuse.

## Final step

Please [Sign up](https://junk.systems/client/) to start running your tasks.