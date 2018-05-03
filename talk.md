# Vagrant Talk
## Brief refresher on Vagrant:
Vagrant is, in short, a tool for managing virtual machines from any of a large number of providers. In fact, it’s a modular framework for working with virtual machines. It allows you to build and manage VMs in a single workflow. It lets you build environments, provisioned with the tools and dependencies that you need, consistently and quickly. It also lets you treat those environments as disposable, and reproduce them on any host machine that can support virtualisation.

It’s a cross-OS platform, and it also handles a lot of your networking for you - as soon as you standup a Vagrant VM, you’re able to access the network.

One of the key strengths of Vagrant is that it helps to dispel “it works on my machine…” - development and test environments can be consistent and match production environments identically. So, if, for example, your development machine is a windows box, but your production environment is Centos-based, you can create the enviroment you need to direct your development efforts toward easily - and then share it.

The Vagrantfile describes the machine or machines that you want to create, including their configuration and any provisioning steps that are required. They’re named ‘Vagrantfiles’ because that is the literal filename. They describe the virtual machines that Vagrant produces completely, so, if they are version-controlled, developers can simply check out the Vagrantfile, run ‘vagrant up’ and have a precisely-defined environment ready in a few moments.

## Vagrant vs Docker:
When I went through the draft of this talk, both Raju and Don asked "Why would you not just use Docker?" - and that's a really good question that I thought it was worth addressing, briefly.

It isn't really a fair comparison to stand Vagrant next to Docker - in some scenarios they do overlap; in the vast majority, they don't.

So, what are the use-cases for which VMs are more appropriate than using a container. I'm going to assume that you're all aware of the difference between a VM and a container, but I will talk a little to the differences...

A VM is a full-stack computer implemented in software on top of a hypervisor - the hypervisor simulates the underlying hardware and sequesters a portion of the host machine's hardware resources. A container, though, is basically a piece of kernel sleight-of-hand - like a virtual machine, it limits and prioritises access to physical resources but it uses the host kernel to do this, and it also provides namespace isolation for an applications view of process trees, user ids, networking and filesystems.

This makes containers much more lightweight than VMs - VMs have the overhead of running a complete operating system as well as the virtual device drivers needed to simulate hardware. However, they bring much greater isolation.

Containers are ideally suited to running a single application - in fact, it's a generally accepted good practice to only run a single application in a container. For creating the development environment in which you'll create that application, a VM with multiple tools, whose definition is stored as code and can be shared among developers is ideal - and this is Vagrant's key strength.

Vagrant launches things in order to run apps and services for the purpose of development - this can be on VirtualBox, VMWare, or remote services like AWS. Within those, you could easily run containers - Vagrant really doesn't care, it embraces that - in fact, as I've already mentioned, Docker is one of the possible providers. It's *also* available as a provisioner for Vagrant - so, in addition to being able to launch

In fact, it's worth mentioning that you can use Vagrant and Docker together - Vagrant works with a variety of virtual environment providers, and Docker is one of them. We'll take a specific look at how this works when we consider the providers that Vagrant works with.

## Talk about private repository

Find out about real-life examples of prod environments commissioned using vagrant

## Creating an Ubuntu xenial server in 3 easy steps:
### Getting a base box
First, we're going to download the base box that we're going to use:
```
vagrant box add ubuntu/xenial64
```

Now, strictly speaking, that step isn't even required, as it will be executed automatically later in the process if you haven't already pulled that box to your local machine.

At this point I'm going to digress very slightly to show you Hashicorp's "Vagrant Cloud" - this is a collection of boxes that folk have put together. This is a key part of the Vagrant functionality. As you'll already know, if you've ever built a VM, the process of creating one from scratch is necessarily a slightly convoluted and tedious process. Vagrant shortcuts this by making base images - called boxes - available on the Vagrant Cloud. This stores a collection of boxes of various types and at various levels of provisioning or configuration.

I'm sure there are plenty of folk here who don't need to be told this, but I feel I need to mention it anyway...: the Vagrant Cloud is an open resource - anyone can put a box that they've created up on it to make it available to the world. That, of course, represents a huge convenience - no matter what VM you want to create, the odds are that someone has already done - but also a *huge* security hole.

The top level namespaces on Vagrant cloud are like the repository namespaces on github - there are no guarantees. When you download a box, you should do so from a known and trusted namespace - for example: 'hashicorp', 'bento', which is the open-source 'bento' project from Chef, or 'ubuntu', which, unsurprisingly, is from Canonical. Otherwise, you should at least check that the namespace is associated with a github project, so you can check out what they do - if you choose to add a random box that appears to do what you want, you should be aware that you do so taking your infosec into your own hands!

### Creating the Vagrantfile

With all that in mind, and after you've identified the box you want to base your VM on, the next step is to create the Vagrantfile that will define your box. For our simple example - this is *really* simple:
```
vagrant init ubuntu/xenial64
```
That is it - all you need to do! This is the most trivial Vagrantfile that it's possible to have - if we take a look into it, we can see the ruby headers, the top level configuration and a box definition - at the moment, that's it. So, let's bring it up and see what that minimal definition gives us.

### Starting the Vagrant VM

Even easier:
```
vagrant up
```
Your new VM is up and running. To jump on:
```
> vagrant ssh
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.4.0-119-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


vagrant@ubuntu-xenial:~$ whoami
vagrant
vagrant@ubuntu-xenial:~$
```

## In-depth look at the Vagrantfile:

OK, so we've seen the most simple of Vagrantfiles now - so what can we do with one?

Vagrantfiles are written in Ruby, so you have the full power of that language at your fingertips if you want. Their primary function is to be the description of the type of machine required, and how to configure and provision that machine.

The Vagrantfile is intended to have a one-to-one relationship with a project and be committed to source-control. As you've seen, other developers can then pick that up and run a simple `vagrant up` to start an identical VM (or several).

### Vagrantfile load order

When `vagrant up` is executed, Vagrant actually loads a sequence of Vagrantfiles and merges the settings they describe as it goes - that means that the order in which they load is important:

1. Vagrantfile packaged with the box that is to be used for a given machine.
2. Vagrantfile in your Vagrant home directory (defaults to ~/.vagrant.d).<br>
This lets you specify some defaults for your system user.
3. Vagrantfile from the project directory.<br>
This is the Vagrantfile that you will be modifying most of the time.
4. Multi-machine overrides, if any.
5. Provider-specific overrides, if any.

Generally, settings later in the sequence will override.

Within the Vagrantfile there can be multiple `Vagrant.configure` blocks - these are merged in the order that they're defined.

### The Vagrant.configure statement

The first thing to note about the `config` object block is the version. Currently, there are only 2 supported versions:
- "1" represents the configuration for Vagrant 1.0.x;
- "2" represents the configuration for Vagrant 1.1 up to 2.0.x - essentially, unless you're using a very old version of Vagrant, this version of `Vagrant.configure` will be "2".

You can mix and match `Vagrant.configure` versions in a single Vagrantfile, but only in separate `Vagrant.configure` blocks.

### Machine settings

The `config.vm` settings define the configuration of the virtual machine that the Vagrantfile manages. There are lots of settings that are useful - I'm going to pick on a few of the more important ones:

- #### The box

  `config.vm.box` <br>
  Requires version 1.5+

- #### The box URL

  `config.vm.box_url` <br>
  Requires version 1.5+

- #### The box version

  `config.vm.box_version` <br>
  Requires version 1.5+

- #### The connection type

  `config.vm.communicator`

### Provisioning virtual machines with Vagrant:


#### Provisioning with a shell script:

#### Provisioning with Ansible:

### Provider-specific configurations:

### Multiple hosts created from a single Vagrantfile:

## Portability across VM providers:

### Docker as a Vagrant provider

## Creating a typical LAMP stack:

## Creating a RHEL server environment (including automated subscription registration):
