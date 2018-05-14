# Vagrant Talk
## Introduction:
### What is Vagrant?
- Vagrant is
  - automation tool with domain-specific language
  - automates creation of VMs and environments
  - large number of providers
  - modular framework for working with virtual machines
  - build and manage VMs in single workflow
  - build environments with tools and dependencies consistently and quickly
  - environments become disposable
  - can be reproduced on any host machine supporting virtualisation
  - version-control machine specifications
  - cross-OS platform tool
  - handles networking automatically

- dispels “it works on my machine…”
- development and test environments can be consistent
- match production environments
- can easily share

- Vagrantfile describes machines
- includes configuration and provisioning
- named ‘Vagrantfiles’ because literal filename
- complete description
- developers can:
  - check out Vagrantfile
  - ‘vagrant up’
  - environment ready in seconds

### Vagrant vs Docker:
- not really fair comparison
- some overlap
- play well together

- VM is:
  - full-stack computer implemented in software
  - sits on top of a hypervisor
  - hypervisor simulates underlying hardware
  - ring-fences hardware resources
- A container is:
  - kernel sleight-of-hand
  - host kernel limits/prioritises access to physical resources
  - also provides namespace isolation:
    - process trees
    - user ids
    - networking
    - filesystems

- containers much more lightweight
- VMs run complete operating system
- also virtual device drivers to simulate hardware
- much greater isolation

- containers ideally suited single applications
- good practice to only run single application
- for development environment:
  - require multiple tools
  - definition is stored as code
  - shared among developers
  - Vagrant's key strength

- Vagrant launches things to run apps and services for development
- can be on:
  - VirtualBox
  - VMWare
  - remote services like AWS
- could easily run containers
- Vagrant really doesn't care
- it embraces that
- can use Vagrant and Docker together
- Docker one of possible providers for Vagrant
- also provisioner for Vagrant
- Vagrant can:
  - launch Docker from images or Dockerfiles
  - automatically install Docker
  - configure containers to run on VM boot

### Public and private repositories
- Vagrant builds on top of a 'box'
- package format for Vagrant VM plus anything built on top
- 'base boxes' contain bare minimum for Vagrant to function
  - minimal ISO install
- box essentially base box with:
  - configurations
  - tools
  - applications
  installed on top.

- "Vagrant Cloud" is online collection of boxes
- hosted online by Hashicorp
- key part of the Vagrant functionality
- creating VM from scratch convoluted and tedious
- especially if repeated
- Vagrant Cloud shortcuts this
- huge variety of boxes available
- anyone can store boxes
- various types
- various levels of provisioning or configuration

- Vagrant Cloud is open resource
- huge convenience
- odds are someone has already created what you want
- huge security hole.
- top level namespaces like github repository namespaces
  - no guarantees
- download boxes from known, trusted namespace
  - for example:
    - 'hashicorp'
    - 'bento' - open-source 'bento' project from Chef
    - 'ubuntu' - Canonical
- otherwise, should check namespace associated with github project
    - check out what they do
- random boxes present infosec risk!

## Creating an Ubuntu xenial server in 3 easy steps:
### Getting a base box
- download base box:
```
vagrant box add ubuntu/xenial64
```
- not strictly required
- will be executed automatically if not already pulled

### Creating the Vagrantfile
- create the Vagrantfile:
```
vagrant init ubuntu/xenial64
```
- most trivial Vagrantfile possible:
  - ruby headers
  - top level configuration
  - box definition
  - nothing else (other than comments)

### Starting the Vagrant VM
- start VM:
```
vagrant up
```
- new VM up and running
- to jump on:
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

## A look at the Vagrantfile:

- Vagrantfiles written in Ruby
  - full power of that language if required
- primary function is description of VM required
  - configuration
  - provisioning

- Vagrantfile intended to:
  - have one-to-one relationship with project
  - be committed to source-control
- developers can check out and run simple `vagrant up`

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
