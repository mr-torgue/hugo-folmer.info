---
title: 'Running OpenStack using Virtual Private Servers'
date: '2023-06-30'
---

Currently, I use several VPS'es. Over the years the costs of these servers have significantly increased.
Every now and then, there is a good deal for a VPS. The website [LowEndBox](https://lowendbox.com/) for example has sometimes great deals.
The problem with buying a new VPS however is that I have currently no good method to migrate.
Migration takes quite a bit of effort and there is risk that it goes wrong.
So, how can migration to a new VPS be minimized?

# Some Solutions
There are several solutions. One very effective is to create snapshots.
However, I don't know if snapshots are universally supported: is it just plug-and-play for every different hoster?
Different virtualization techniques could give some complications.
Another solution that is guaranteed to work universally is to use rsync to sync all the files.

These solutions might work well enough. It lacks a certain elegance however.
The optimal solution would be that VPS'es can be clustered: new VPS deal? Add it to the cluster. Old VPS too expensive? Remove it from the cluster.
So, the VPS'es are basically harvested for their resources and the actual applications are build on top of it.
One way of achieving this is by using OpenStack.

# OpenStack
OpenStack is software that can be used to create your own private cloud environment.
I did some testing with it but I was unsuccessful in setting it up on a VPS.
The main challenge here is the networking. It needs a private network between the nodes to communicate.
Also, we do not have a public IP range, so deploying within the provider network is difficult.
The last limitation is that OpenStack is very powerful: maybe too powerful for a VPS.

# Conclusion
Whilst OpenStack is not out of the question, it will require a lot of work to make it work.
A private network can be established by using a VPN between the nodes.
However, this complicates the design and it is likely that this will cause a performance overhead.

For our purposes, OpenStack might be overkill. 
In the future I will focus on a different approach using Kubernetes.
Kubernetes is not as flexible and requires that everything is containerized.
Running your applications in a container is always a good idea and I have been using containers for a while.
Kubernetes might be just what is needed...