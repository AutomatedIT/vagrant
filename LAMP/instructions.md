# Creating a LAMP Stack using ansible

## Setup

You should already have VirtualBox and Vagrant installed on your machine. If you don't, please follow these instructions:

### Windows:

### Mac:

### Linux:

## The base box

We're going to start by creating a really simple Vagrantfile that initialises a Centos 7 box. In order to do this, we've provided a local copy of the 'Centos/7' box. On your USB drive you'll see a folder, in the root, called `centos-VAGRANTSLASH-7`. Copy this to the `.vagrant.d` folder in your home area.

## The Vagrantfile

Create a Vagrantfile (literally, a file called 'Vagrantfile' with the following content:
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos/7"
end
```
