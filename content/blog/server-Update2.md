---
title: "December server update"
build:
  render: never
  list: never
  publishResources: false
---

Tailscale, Ticketing System, Pihole for Clients
<!--more-->

I have started using a ticketing system to track problems as they emerge on the tailnet. The homelab has now grown to more users and with that comes more complexity for my usecase. So I pulled OsTicket again and I think a proper DNS or maybe even another pihole woould be nice to have for failover and more redundancy.

So far, I have been looking at branching the server to use 2x Dell poweredge r430's but I am going to wait a bit before proocuring them and deal with the problems I have as of late. I also really want to learn more things such as :

- [ ] wanting to use more docker environments and use cases
- [ ] wanting to use kubernetes for things as well
- [ ] Using subnets for different locations to extend the tailscale network where things are physically located
- [ ] When setting up a high availability cluster what are all the things that I need to worry about from load balancing to when a vm goes done etc.

In 2026, I really want to utilize the cluster style of things rather than having individual nodes. To me it makes things more accessible and easier for the end user to use. Like making the piHole be the default dns for the tailnet and block ads for all. Another thing I need to figure out is when I am done with school where the physical servers will reside. The biggest thing going forward will be utilizing the ticketing system like a git repo for specific config changes. I plan on just updating a ticket for the machine with any real changes to make it easy to go back and see whats happening. More importantly though this will make it easier for aanother person to come in and see whats what in the network. I have an idea of who I want to add as a sys admin but it depends on if they want in. What I want to really finishup now is building the knowledgebase up with the basics of the network and usual thing that I have to do. Like adding the tunnel information for a lxc config to let tailscale work.

I also need to start looking at making default dotfiles or installs on servers to try and automate the setup process a bit. Something where I am setting up a user, downloading a bunch of files and then going from there for the specific use case. I am taking a cyberdefense and ethical hacking course that is accompanied by a networked infrastructure course this coming semester and I think it'll be the best classes i've taken since they told me I had to go back to school in kindergarden. Im also taking an intelligence class but I don't have the highest hopes for that one as I do the Information Science courses.

A certbot or using the pihole signing certs should be another thing that I look into. I want to point stuff to acutal names rather than ip addresses. And if I am going to be doing that I might as well make them https as well.