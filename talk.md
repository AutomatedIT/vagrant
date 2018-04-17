# Vagrant Talk
## Brief refresher on Vagrant:
Vagrant is in short, a tool for managing virtual machines from any of a large number of providers. In fact, it’s a modular framework for working with virtual machines. It allows you to build and manage VMs in a single workflow. It lets you build environments, provisioned with the tools and dependencies that you need, consistently and quickly. It also lets you treat those environments as disposable, and reproduce them on any host machine that can support virtualisation.

It’s a cross-OS platform, and it also handles a lot of your networking for you - as soon as you standup a Vagrant VM, you’re able to access the network.
One of the key strengths of Vagrant is that it helps to dispel “it works on my machine…” - development and test environments can be consistent and match production environments identically.

The Vagrantfile describes the machine or machines that you want to create, including their configuration and any provisioning steps that are required. They’re named ‘Vagrantfiles’ because that is the literal filename. They describe the virtual machines that Vagrant produces completely, so, if they are version-controlled, developers can simply check out the Vagrantfile, run ‘vagrant up’ and have a precisely-defined environment ready in a few moments.

## Creating an Ubuntu xenial server in 3 easy steps:
### Getting a base box
First, we're going to download the base box that we're going to use:
```
vagrant box add ubuntu/xenial64
```

Now, strictly speaking, that step isn't even required, as it will be executed automatically later in the process if you haven't already pulled that box to your local machine.

At this point I'm going to digress very slightly to show you Hashicorp's "Vagrant Cloud" - this is a collection of boxes that folk have put together. This is a key part of the Vagrant functionality. As you'll already know, if you've ever built a VM, the process of creating one from scratch is necessarily a slightly convoluted and tedious process. Vagrant shortcuts this by making base images - called boxes - available on the Vagrant Cloud. This stores a collection of boxes of various types and at various levels of provisioning or configuration.

I'm sure there are plenty of folk here who don't need to be told this, but I feel I need to mention it anyway...: the Vagrant Cloud is an open resource - anyone can put a box that they've created up on it to make it available to the world. That, of course, represents a huge convenience - no matter what VM you want to create, the odds are that someone has already done - but also a *huge* security hole.

The top level namespaces on Vagrant cloud are like the repository namespaces on github - there are no guarantees. When you choose to download a box, you should do so from a known and trusted namespace - for example: 'hashicorp', 'bento', which is the open-source 'bento' project from Chef, or 'ubuntu', which, unsurprisingly, is from Canonical. Otherwise, you should at least check that the namespace is associated with a github project, so you can check out what they do - if you choose to add a random box that appears to do what you want, you should be aware that you do so taking your infosec into your own hands!

### Creating the Vagrantfile

With all that in mind, and after you've identified the box you want to base your VM on, the next step is to
## In-depth look at the Vagrantfile:
Vagrantfiles are written in Ruby, so you have the full power of that language at your fingertips if you want. One of the key things about the Vagrantfile, though, is its accessibility - you can get started very quickly
## Provisioning virtual machines with Vagrant:
### Provisioning with a shell script:

### Provisioning with Ansible:

## Provider-specific configurations:

## Multiple hosts created from a single Vagrantfile:

## Portability across VM providers:

## Creating a typical LAMP stack:

## Creating a RHEL server environment (including automated subscription registration):
