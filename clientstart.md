---
layout: page
title: Compute Client
subtitle: Instant x86_64 SSH shells for computation 
---

## How it works

You get a non-privileged linux user shell SSH credentials by issuing a `GET` request to 

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

You can use the provided private key at the `privkey` field as ssh identity or you can log in using your own ssh public key that you can provided in the Client interface.

We provide an example helper python script [here](https://github.com/junk-systems/jusy/blob/master/jusy-client.py) to show how it can be used.

~~~
$ wget https://raw.githubusercontent.com/junk-systems/jusy/master/jusy-client.py
$ python jusy-client.py <Your Client Hash>
...
juser234253@remote:~ $
~~~

## Machine types and specs

Generally it is up to you to decide whether the machine is suitable for your needs. The only testing we currently do is checking that the node can execute a PRNG-test within a fixed amount of time which is roughly equivalent to AMD 1090T CPU core.

You can check the machine requirements for Node Owners at [Getting started for Node Owner](/nodeowner) page.

You will get basic SSH networking with only port 22 accessible for connections to remote hosts.

## Registration

You need to sign up at the [Client web interface](/client) and add some funds to your account to start using the service.

## Sending tasks

You may use different clustering techniques like MPI to send tasks to nodes, or implement your own method. For example, you can send binaries to execute or rely on tools that may or may not be available on the node.

## Master SSH connection 

You may want to open a [master SSH channel](http://unix.stackexchange.com/questions/33557/using-an-already-established-ssh-channel) to be able to open multiple shells as when you close the connection your session is finished and user data is deleted.

## Security Considerations

- Your data, algorithms and computation results are seen to the Node Owner and potentially to other parties
- Your computation results are not reliable as they may be compromised or corrupted

So generally you will want to send anonymous data and implement result checking or redundancy for all the tasks.