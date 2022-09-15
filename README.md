ln -s /Applications/VMWare\ Fusion\ Tech\ Preview.app /Applications/VMWare\ Fusion.app# OS-Challenge-Common for Apple Silicon

*By Emil Njor (emjn@dtu.dk)*

As of 13/09/2022 this guide applies to Mac computers with the M1, M1 Pro, M1 Max, M1 Ultra and M2 chips.

## The problem

VirtualBox and the operating system that the course originally uses is made to work on the x86 CPU architecture, which in general is the architecture used by most personal computers. The new Apple Silicon chips however, are based on the ARM CPU architecture which causes problems.

As of this moment VirtualBox has no support for the Apple Silicon devices — and it may never have. Therefore, we need to find another application to act as the hypervisor that we can run our virtual machines on.

As for the operating system we have two options. Run an operating system that is made for ARM or run the normal x86 operating system through an emulator. Running an operating system through an emulator can be really slow, so it would be preferable if we can find an operating system made for ARM. 

## A Solution

Hypervisors and non-macOS operating systems are unfortunately still very much in their infancy on Apple Silicon. This means that support for them is limited, and whatever programs support them tend to be buggy.

The solution that I have found to work the best on my own M1 Pro MacBook is to use the 2022 version of the tech preview of VMWare Fusion for Apple Silicon as the hypervisor. This can be used in combination with vagrant after a little setup.

For the operating system we are going to use “Ubuntu Server for ARM” (https://ubuntu.com/download/server/arm).

## Installation

The tools that we are installing in this section can be installed both by downloading the tools from their respective webpages or through the Homebrew package manager. I personally prefer to use Homebrew to keep track of the installations I have on my computer. See the Homebrew webpage for how to get started with Homebrew (https://brew.sh/)

### Install VMWare Fusion Tech Preview 22H2

First we need to install the hypervisor that we are going to use. You can either download and install this straight from VMWare's webpage (https://customerconnect.vmware.com/downloads/get-download?downloadGroup=FUS-PUBTP-22H2) or install it through homebrew using the following commands:
```
$ brew tap homebrew/cask-versions # This adds a new repository to your homebrew installation. This is needed as the VMWare version that we are using is not in the main repositories as it is currently a tech preview.
$ brew install vmware-fusion-tech-preview
```

After you install VMWare Fusion Tech Preview 22H2 you will need to create a symbolic link from that application to a "fake" application called VMWare Fusion. This is because the vagrant vmware utility, a program that we will install later, assumes this name for VMWare Fusion. Use this command to create the symbolic link:
```
ln -s /Applications/VMWare\ Fusion\ Tech\ Preview.app /Applications/VMWare\ Fusion.app
```
Credit to this GitHub post for discovering this (https://gist.github.com/sbailliez/f22db6434ac84eccb6d3c8833c85ad92)

### Install Vagrant

As with VMWare we can either download and install vagrant from its homepage (https://www.vagrantup.com/) or using homebrew:
```
$ brew install vagrant
```

### Install the Vagrant VMware Utility

In order to work together Vagrant and VMWare needs to have an additional utility program installed. Download and install either from this webpage (https://www.vagrantup.com/docs/providers/vmware/vagrant-vmware-utility) or through homebrew:
```
$ brew install vagrant-vmware-utility
```

### Install the Vagrant VMWare Plugin

Vagrant also needs an additional plugin to work with VMWare. This can be installed using:
```
$ vagrant plugin install vagrant-vmware-desktop
```

### Using a custom Vagrantfile and bootstrap.sh

The Vagrantfile provided by Fontas for the course relies on an x86 version of Ubuntu and VirtualBox. I have created a new Vagrantfile that you can find in this repository that uses the Ubuntu Server for ARM operating system and the VMWare hypervisor.

I have also updated the bootstrap.sh file to set up the operating system correctly.

### Running the machines

I have unfortunately not found a way to run the virtual machines in a completely headless mode. In practice this means that you will have VMWare windows that open when you start the virtual machine. This may have an impact on the speed of the virtual machines. 

Note that it is possible to start the virtual machines in a headless mode by modifying the ``provider.gui = true`` lines to say ``provider.gui = false``. This however results in an error when starting the virtual machines that I have only been able to partially solve. It should still be possible to run the virtual machines like this, however they require a lot of manual steps where we need to replicate the steps that vagrant should do after its error. Please contact me (email in top of document) if you would like to know more about this.

Simply create/start the virtual machines using:
```
$ vagrant up
```

You can verify that the machines are running with the following command:
```
$ vagrant status
```

You should now be able to follow the original guide for connecting and getting familiar with the virtual machines.


## Other Possible Solutions

While investigating how to work with Linux virtual machines on Apple Silicon I did also try out some other solutions. Some of them I had to abandon without exhaustively checking them through. I have tried to give a recap of my findings here.

* **UTM:** This is an open-source combined hypervisor and emulator that is made to work on macOS. Some sources even claim that this has better performance than the tech preview of VMWare Fusion. However, it does not seem to have support for being used together with Vagrant at this time (https://github.com/hashicorp/vagrant/issues/12518). It may however be a good option if you are willing to work around vagrant.
* **QEMU** This is a widely used open-source emulator that has support for emulating a large variety of platforms. There is a project to make QEMU work as a vagrant provider (https://github.com/ppggff/vagrant-qemu). I however encountered a bunch of errors when trying to work with it and ended up pursuing different methods.
* **Parallels** This is a paid program that seems to have good support for running both Windows and Linux virtual machines on Apple Silicon. It also works as a vagrant provider. As it is paid software I did not consider it as a valid option for students. Therefore, I also did not research much into the inner workings of the program, but I expect it to be a combined emulator and hypervisor like UTM.
* **Docker** This project is meant as an alternative to virtual machines that allows “containers” to run using their own runtime environment but without virtualizing an entire operating system. That said it does have support for both Apple Silicon and Vagrant. Some people have reportedly had success with starting virtual machines through vagrant using this. Their methods seemed a bit hacky though, and I am unsure if it is possible to create a virtual network between two machines. Check out the following link if you are interested: (https://betterprogramming.pub/managing-virtual-machines-under-vagrant-on-a-mac-m1-aebc650bc12c).
* **Multipass** This is a project by Canonical - the company behind Ubuntu. It supposedly allows for running Ubuntu virtual machines on Apple Silicon. I did not investigate this option much as I discovered it quite late, but this particular documentation made me think that it would not be able to set up a virtual network between two guest virtual machines (https://multipass.run/docs/additional-networks).