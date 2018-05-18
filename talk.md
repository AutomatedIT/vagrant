# Vagrant Talk
## Introduction:
### What is Vagrant?
Vagrant is, in short, an automation tool with a domain-specific language that is used to automate the creation of VMs and VM environments from any of a large number of providers. In fact, it’s a modular framework for working with virtual machines. It allows you to build and manage VMs in a single workflow. It lets you build environments, provisioning them with the tools and dependencies that you need, consistently and quickly. It also lets you treat those environments as disposable, and reproduce them on any host machine that can support virtualisation. Crucially, you can also version-control the specification that you use to create these machines.

It’s a cross-OS platform tool, and it also handles a lot of your networking for you - as soon as you stand up a Vagrant VM, you’re able to access the network.

One of the key strengths of Vagrant is that it helps to dispel the classic developer refrain - so beloved of test teams and release managers - “it works on my machine…” - development and test environments can be consistent and match production environments identically. So, if, for example, your development machine is a windows box, but your production environment is Centos-based, you can create the environment you need to direct your development efforts toward easily - and then share it.

The Vagrantfile describes the machine or machines that you want to create, including their configuration and any provisioning steps that are required. They’re named ‘Vagrantfiles’ because that is the literal filename. They describe the virtual machines that Vagrant produces completely, so, if they are version-controlled, developers can simply check out the Vagrantfile, run ‘vagrant up’ and have a precisely-defined environment ready in a few moments.

There is excellent documentation available for Vagrant at vagrantup.com.

### Vagrant vs Docker:
When I went through the draft of this talk, both Raju and Don asked "Why would you not just use Docker?" - that's a really good question and, even if it is slightly off-topic for this workshop, I thought it was worth addressing, briefly.

I'll start by saying that it isn't really a fair comparison to stand Vagrant next to Docker - in some scenarios they do overlap; in the vast majority, they don't. As well as that, they will play well together, as we'll see...

So, what are the use-cases for which VMs are more appropriate than using a container. I'm going to assume that you're all aware of the basic difference between a VM and a container, but I think it's still worth talking a little to what those differences are:

A VM is a full-stack computer implemented in software on top of a hypervisor - the hypervisor simulates the underlying hardware and sequesters a portion of the host machine's hardware resources. A container, though, is basically a piece of kernel sleight-of-hand - like a virtual machine, it limits and prioritises access to physical resources but it uses the host kernel to do this, and it also provides namespace isolation for an application's view of process trees, user ids, networking and filesystems.

This makes containers much more lightweight than VMs - after all VMs have the overhead of running a complete operating system as well as the virtual device drivers needed to simulate hardware. However, they bring much greater isolation.

Containers are ideally suited to running a single application - in fact, it's a generally accepted good practice to only run a single application in a container. For creating the development environment in which you'll create that application, a VM with multiple tools, whose definition is stored as code and can be shared among developers is ideal - and this is Vagrant's key strength.

Vagrant launches things in order to run apps and services for the purpose of development - this can be on VirtualBox, VMWare, or remote services like AWS. Within those, you could easily run containers - Vagrant really doesn't care, it embraces that - in fact, it's worth mentioning again that you can use Vagrant and Docker together. Docker is one of the possible providers for Vagrant and it's *also* available as a provisioner for Vagrant - so, in addition to being able to launch Docker from either images or Dockerfiles, Vagrant can automatically install Docker and configure containers to run on boot of the virtual machine that it creates.

### Public and private repositories
Vagrant builds on top of a basic unit, the 'box'. This is is the package format for a vagrant virtual machine and anything that is built on top of it. You might also come across the term 'base boxes' - these contain the bare minimum for Vagrant to function - just the result of a minimal ISO install. So a box is essentially a base box with any number of other configurations, tools or applications installed on top.

At this point I'm going to digress very slightly to talk to you about Hashicorp's "Vagrant Cloud" - this is a collection of boxes that folk have put together, hosted online by Hashicorp. This is a key part of the Vagrant functionality. As you'll already know, if you've ever built a VM, the process of creating one from scratch is necessarily a convoluted and tedious process - especially if you have to repeat the process again and again! Hashicorp have shortcut this process by making a variety of boxes available on the Vagrant Cloud. In addition, they've provided hosting for anyone else to store boxes that they've created. This means that the Vagrant Cloud stores a huge collection of boxes of various types and at various levels of provisioning or configuration.

I'm sure there are plenty of folk here who don't need to be told this, but I feel I need to mention it anyway...: the Vagrant Cloud is an open resource - anyone can put a box that they've created up there to make it available to the world. That, of course, represents a huge convenience - no matter what VM you want to create, the odds are that someone has already done it - but it is also a *huge* security hole.

The top level namespaces on Vagrant cloud are like the repository namespaces on github - there are no guarantees. When you download a box, you should do so from a known and trusted namespace - for example: 'hashicorp', 'bento', which is the open-source 'bento' project from Chef, or 'ubuntu', which, unsurprisingly, is from Canonical. Otherwise, you should at least check that the namespace is associated with a github project, so you can check out what they do - if you choose to add a random box that appears to do what you want, you should be aware that you do so taking your infosec into your own hands!


## Creating an Ubuntu xenial server in 3 easy steps:
### Getting a base box
First, we're going to download the base box that we're going to use:
```
vagrant box add ubuntu/xenial64
```

Now, strictly speaking, that step isn't even required, as it will be executed automatically later in the process if you haven't already pulled that box to your local machine. What this step is doing is downloading compressed box file from the default location - vagrantcloud.com - and uncompressing it into the .vagrant.d folder in your home area.

### Creating the Vagrantfile
After you've identified the box you want to base your VM on, the next step is to create the Vagrantfile that will define your box. For our simple example - this is *really* simple:
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

## Interacting with Vagrant - the CLI
Almost all interaction with Vagrant is through the command line interface - there is no GUI. So, we've already seen what are probably the three most important CLI commands for interacting with Vagrant - `vagrant box add`, `vagrant init` and `vagrant up` - in fact, if you start using it regularly as a tool, these will probably make up the bulk of your interactions with it.

Now, I'm not going to go into a huge amount of detail on the command-line interface - it would take all of the time we've got tonight, you'd get no time for beer or pizza, and it would bore you - and me - half to death! However, as it is pretty much the only way that we can interact with vagrant, it's worth covering the more important commands, so let's have a brief look at some of them:

### vagrant box
We've already talked about how vagrant builds everything it does on top of boxes, and the vagrant box command is what we can use to manage them. The main functionality is exposed by even more subcommands.

We've already looked at `vagrant box add` - this makes a box at the given address available locally to vagrant. We have seen it run with just a simple name - by default, this looks for the box with that name on the Vagrant Cloud and downloads it. You can, however, give it an http URL, or even a local path to a box file. It will take that box, from whatever source, and unpack it ready for use. `vagrant box list` will show you the machines that are available locally. `vagrant box remove` will remove a local box - which can be helpful for space management as boxes, unsurprisingly, can take up quite a bit. `vagrant box repackage` is essentially the opposite of `vagrant box add` - where `add` unpacks a `.box` file - `repackage` takes a local box and packages it up into a `.box` file for redistribution. Finally, `vagrant box update` will bring a local box up to the latest version from whatever its original source was.

### vagrant init
Next, I want to mention `vagrant init`. We've seen this one, it's quite an important command, but it's very simple, so I'll be brief - it doesn't even warrant a slide - it simply initialises the current directory to be a Vagrant environment by creating the initial Vagrantfile if it doesn't already exist. That's it. It will fail if a Vagrantfile exists and it will populate the box identity and URL if you passose in as parameters.

The only other thing that is noteworthy about the command is that it highlights that the only thing a Vagrant environment needs is a valid Vagrantfile.

### Vagrant VM control commands
I've grouped the next set of commands together because these are the ones you'll be using most frequently, and between them they allow you to control the state of your VM. We've already looked at `vagrant up` - this starts a VM from the first Vagrantfile that it finds in the directory tree. `vagrant halt` stops it - it tries to bring the machine down gracefully (the equivalent of an ACPI shutdown) but if it can't or the `--force` flag is used, it will just power it off. `vagrant suspend` will halt a VM without shutting it down. `vagrant reload` is used if you'd made changes to your Vagrantfile that you'd like reflected in the VM - it is effectively a `vagrant halt` followed by `vagrant up`. When you're done with your box, `vagrant destroy` (that does sound dramatic, doesn't it?) will stop the running machine and delete all the resources associated with it. Finally, `vagrant ssh` will open an ssh session onto your box, automatically logging in as the 'vagrant' user.

OK - that's it for our brief look at the command line interface. Those commands will cover everything you need to start with - as I mentioned, though, there is great documentation available at vagrantup.com.

## A look at the Vagrantfile:
Now, the slide shows a pretty simple Vagrantfiles - in fact, you'll recognise this later, because it's what the workshop will generate. What is it doing?

First up, Vagrantfiles are written in Ruby, so you have the full power of that language at your fingertips if you want. However, their primary function is to be the description of the type of machine required, and how to configure and provision that machine.

The Vagrantfile is intended to have a one-to-one relationship with a project and be committed to source-control. As you've seen, other developers can then pick that up and run a simple `vagrant up` to start an identical VM (or several).

Although the Vagrantfile is intended to have a singular relationship with a project, that doesn't mean you're restricted to a single machine. You can define a whole cluster in a single Vagrantfile if you need to, and you can give each of those boxes individual configurations, even base them on completely different base boxes.

### Vagrantfile lookup path and load order
When you run any `vagrant` command, Vagrant will start looking for a Vagrantfile in the current directory - then it will climb the directory tree until it finds one. This makes it possible to run `vagrant` from any directory in your project.

It's worth mentioning that when `vagrant up` is executed, Vagrant actually loads a sequence of Vagrantfiles and merges the settings they describe as it goes - that means that the order in which they load is important:

1. Vagrantfile packaged with the box that is to be used for a given machine.
2. Vagrantfile in your Vagrant home directory (defaults to ~/.vagrant.d).<br>
This lets you specify some defaults for your system user.
3. Vagrantfile from the project directory.<br>
This is the Vagrantfile that you will be modifying most of the time.
4. Multi-machine overrides, if any.
5. Provider-specific overrides, if any.

Generally, settings from Vagrantfiles later in that sequence will override settings from earlier - although there are some exceptions - for example, the network settings will simply append to each other.

Within the Vagrantfile there can be multiple `Vagrant.configure` blocks - these are merged in the order that they're defined.

### The Vagrant.configure statement
The first thing to note about the `configure` object block is the version. Currently, there are only 2 supported versions:
- "1" represents the configuration for Vagrant 1.0.x;
- "2" represents the configuration for Vagrant 1.1 up to 2.0.x - essentially, unless you're using a very old version of Vagrant, this version of `Vagrant.configure` will be "2".

You can mix and match `Vagrant.configure` versions in a single Vagrantfile, but only in separate `Vagrant.configure` blocks.

### Machine settings
The `config.vm` settings define the configuration of the virtual machine that the Vagrantfile manages. There are lots of settings that are useful - we could be here for hours if I picked through all of them, but they're well documented on the Vagrant pages at https://www.vagrantup.com/docs/vagrantfile/ - I'm going to pick on a few of the more important ones:

`config.vm.box` <br>
This specifies the vagrant box that is the starting point for this particular VM. Anything we add to this configuration is built on top of the box defined here. By default, Vagrant will look in the `boxes/` folder within the `.vagrant.d` folder in your home area - however, as I mentioned earlier, if it can't find what it's looking for and you're connected to the internet, vagrant will automatically go to the Vagrant Cloud and try to download the box when you run `vagrant up`. There is also a `config.vm.box_url` setting that could specify a different location to look.

`config.vm.hostname` <br>
This sets the guest hostname.

Now, as you can see here, I've also included a `config.vm.define` setting - and, slightly confusingly, all the `config.vm` settings appear as `lamp.vm`. This is for setting up multiple machine environments - as you can see, you can define a namespace per machine - in fact, in this case, there is a single machine, but we've added this define statement in so that it is given a name other than 'default'. However, if we wanted we could have as many of these blocks as we need, each with its own name and namespace, and, potentially, each set up completely differently.

### Network settings
`config.vm.network` <br>
The next few settings we're going to look at in this Vagrantfile are to do with networking. The Vagrant networking options provide you with an abstraction that works across all the possible providers - so, if you bring up a box on VirtualBox, for example, you can reasonably expect that the Vagrantfile will behave in the same way on a VMWare environment.

Now, by default, Vagrant brings up virtual machines that have access to the same network as the host - these settings allow you to refine that.

- #### Forwarded ports
The two examples here show how you can forward a port from the guest to the host - in these examples, localhost:8080 will access port 80 on the guest and in the second line we pass port 3306 straight through to the guest.

By default, forwarded ports will only pass TCP traffic, but you can specify the protocol to allow UDP traffic to pass. We can also specify that they will only work on the loopback address, for example, so that there won't be any access from the wider network.

- #### Private and public networks
I also want to quickly address a couple of the other networking possibilities - private and public networks - which can also be set up with the same setting. A private network basically allows your guest to be accessible from the host by an address that is not available to the wider network. Multiple machines within the same private network can communicate with each other. You can assign a specific IP address or use DHCP. Public networks are essentially less private than private networks, but the definition is made a bit fuzzy by the fact that different providers treat them slightly differently. This is one area where there is inconsistency across providers. While private networks will never allow the general public to access your machine, publics networks might do - so you should be careful.

One other quick note - Vagrant boxes are insecure by default (and by design). They use insecure keypairs for ssh access, public passwords and often allow root access over ssh. With these known credentials, your box is potentially wide open - bear this in mind before using a public network!

### Synced folders
The next setting we're going to take a look at is `config.vm.synced_folder`. This allows you to share a filesystem location between your host and guest. In this example, we're sharing the host folder that the Vagrantfile occupies with the location /var/www/vagrant on the guest, and setting some mount options. It's a pretty simple way of sharing resources between host and guest, so you'll probably use it pretty frequently. However, one quick gotcha to remember is that support for symbolic links across implementations and host/guest combinations is not consistent - so make sure you test them if they're important to your workflow.

### Provider-specific settings
Just quickly going to jump back up to look at this `config.vm.provider` block. This allows you to modify settings that are specific to the provider that you're using. Now, generally, well-behaved providers should work with any Vagrantfile with sensible defaults - however, individual providers often expose specific configurations that you can utilise here. This section is portable, because it will be ignored if the provider doesn't match. In this particular case, we're just changing the vanity name for Virtualbox.

### Vagrant provisioning
Finally, we've got to the `config.vm.provision` section, where a lot of the heavy lift happens. Provisioners allow you install software, alter configurations and more on the guest VM as part of the `vagrant up` process. Generally, boxes built by other people are not built exactly the way we want them. You can, of course, use `vagrant ssh` to install what you need by hand, but that removes one of the key wins that Vagrant provides - the repeatability. Also, using provisioning means that you can `vagrant destroy` and `vagrant up` a machine and have a pristine environment returned to your specifications with only a couple of commands. Finally, sharing is much easier if you can just check the Vagrantfile in to source control with a complete description of, say, an ideal development environment ready to go, for another developer to check out and `vagrant up`.

The provisioner here is using an inline shell script to set up the tools it needs, and this shows off another strength of the Vagrantfile - the ability to use Ruby functionality to extend itself. In this case, we're defining an inline provisioning shell script as a variable further up the Vagrantfile, and then passing the variable straight to the provisioning setting.

## Vagrant provisioners

## Vagrant providers
