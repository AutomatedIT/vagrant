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

## Interacting with Vagrant - the CLI
- almost all interaction through CLI
- no GUI
- three most important :
  - `vagrant box add`
  - `vagrant init`
  - `vagrant up`

### vagrant box
- manage boxes
- functionality exposed through subcommands
- `vagrant box add`
  - makes box at given address available locally
  - looks on Vagrant Cloud by default
- `vagrant box list`
  - shows local boxes
- `vagrant box remove`
  - removes local box
- `vagrant box repackage`
  - opposite of `vagrant box add`
  - takes local box and packages
- `vagrant box update`
  - brings to latest version.

### vagrant init
- simply initialises current directory
- creates initial Vagrantfile if not extant
- fail if Vagrantfile exists
- highlights that only thing Vagrant environment needs is valid Vagrantfile

### Vagrant VM control commands
-allow you to control state of VM
- `vagrant up`
  - starts VM from Vagrantfile
- `vagrant halt`
  - stops VM
  - tries to bring down gracefully
  - ACPI shutdown
  - if it can't or `--force` flag will just power  off
- `vagrant suspend`
  - halt VM without shutting it down
- `vagrant reload`
  - used for changes to Vagrantfile
  - effectively `vagrant halt` followed by `vagrant up`
- `vagrant destroy`
  - stop running machine and delete all resources
- `vagrant ssh`
  - ssh to running machine as 'vagrant'

- documentation available at vagrantup.com.

## A look at the Vagrantfile:
- Vagrantfiles written in Ruby
  - full power of that language
- one-to-one relationship with project
- committed to source-control
- developers can pick up and run `vagrant up` to start identical VM
- can define whole cluster in a single Vagrantfile
- each box individual configurations
- even base them on completely different base boxes

### Vagrantfile lookup path and load order
- run any `vagrant` command:
  - Vagrant starts looking for Vagrantfile in current directory
  - then climbs directory tree until it finds
- makes it possible to run `vagrant` from any directory in project
- when `vagrant up` is executed:
  - Vagrant loads sequence of Vagrantfiles
  - merges settings as it goes
  - order in which they load is important:
    1. Vagrantfile packaged with the box
    2. Vagrantfile in Vagrant home directory
    3. Vagrantfile from project directory
    4. Multi-machine overrides, if any
    5. Provider-specific overrides, if any
- settings from later Vagrantfiles generally override
- some exceptions:
  - network settings will append, for example

- can be multiple `Vagrant.configure` blocks
  - merged in order defined

### The Vagrant.configure statement
- only 2 supported versions for configuration block:
  - "1" for Vagrant 1.0.x;
  - "2" for Vagrant 1.1 up to 2.0.x
- can mix and match in single Vagrantfile only in separate `Vagrant.configure` blocks

### Machine settings
- `config.vm` settings define configuration of the virtual machine
- lots of settings that are useful
- a few of the more important ones:
  - `config.vm.box`:
    - specifies starting vagrant box
  - `config.vm.hostname`:
    - sets guest hostname
  - `config.vm.define`:
    - multiple machine environments
    - defines namespace per machine

### Network settings:
- `config.vm.network`
  - forwarded ports
    - TCP traffic by default
    - can allow UDP
    - can specify address for host side
  - private networks
    - only talk to host and other machine in same networks
    - static IP or DHCP
    - private networks never allow public access to machine
  - public
    - less private than private networks
    - definition is fuzzy because different providers treat them slightly differently
    - one area where there is inconsistency across providers
    - publics networks might allow public access to machine so should be careful

- Vagrant boxes insecure by default and design
  - use insecure keypairs for ssh access
  - public passwords
  - often allow root access over ssh
- box is potentially wide open
  - bear in mind before using a public network

### Synced folders
- `config.vm.synced_folder`
  - allows share of filesystem location between host and guest
  - simple way of sharing resources between host and guest
  - support for symbolic links across implementations and host/guest combinations not consistent
    - test if important to workflow

### Provider-specific settings
- `config.vm.provider`
  - allows modification of settings specific to provider
  - well-behaved providers generally work with any Vagrantfile with sensible defaults
  - individual providers often expose specific configurations
  - portable as ignored if provider doesn't match

### Vagrant provisioning
- `config.vm.provision`
  - allow installtion of software
  - altered configurations
  - part of the `vagrant up` process
  - can use `vagrant ssh` to install but not repeatable
  - using provisioning allows `vagrant destroy` and `vagrant up` to reset machine
  - sharing much easier

- provisioner here can use inline shell script
- ability to use Ruby functionality to extend Vagrantfile
  - define inline provisioning shell script as variable
  - pass variable straight to provisioning

## Vagrant provisioners
- inline shell script
- shell script file
- Ansible
- Docker
- Puppet
- Chef

## Vagrant providers
- VirtualBox (the default)
- VMWare
- Kernel Virtual Machine (KVM)
- OpenStack
- Docker
- Amazon Web services
- Azure
- Google Cloud
