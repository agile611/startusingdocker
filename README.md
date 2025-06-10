# Start using docker

Here is an explanation of the architecture to be used in the training.

This is an academic setup; it is not intended to be a production environment.

## Working repository

Attached is the work repository we will use during the course:

[Start Using Docker](https://bitbucket.org/agile611/startusingdocker)

For the training sessions, computers will be needed, either laptops or desktops.

## Previous installation for local development

We need to install these 3 applications on our machines:

* [GIT](https://git-scm.com/downloads)
* [Vagrant](https://www.vagrantup.com/downloads.html)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

Vagrant fetches virtual machines from remote repositories, so we advise disconnecting any VPN connections on your machine.

## Setting up the environment

To download the repository, you need to download the code:

```shell
git clone https://bitbucket.org/agile611/startusingdocker.git
cd startusingdocker
```

From the folder `startusingdocker`, use the following command to set up an Ubuntu 24.04 VM (from now on, the Box):

```shell
vagrant up
```
Then, to access the Box, connect via SSH using the following command:

```shell
vagrant ssh
```

## Troubleshooting potential problems 

### Provisioning the Box

If you have problems provisioning the Box, you can download it directly from [here](https://portal.cloud.hashicorp.com/vagrant/discover/bento/ubuntu-24.04) depending on your architecture.

Then, you need to know the path of the Box and execute the following commands:

```shell
vagrant box add /The/Path/From/Your/Downloaded/box/bento-ubuntu-24-04.box --name bento/ubuntu-24.04
vagrant init bento/ubuntu-24.04
```

The `init` command creates a VagrantFile with your initial configuration. In the same folder where this Vagrantfile is located, please execute the following command:

```shell
vagrant up
```

Then, connect to the Box using the following command:

```shell
vagrant ssh
```

If you get a terminal from the Box, your environment is ready.

### Common Networking Problems

If you have proxies or VPNs running on your machine, it is possible that Vagrant is not able to provision your environment. Please check your connectivity beforehand.

### Virtualization with VirtualBox

It is important to ensure that you can virtualize with VirtualBox. If you cannot, you may need to enable VT-X in your machine's BIOS. Here is how to enable it in the BIOS:

* [(English) How to enable Intel VT-X and AMD SVM in the BIOS](https://wiki.2n.com/faqac/en/virtualizace-vt-x-amd-v-povoleni-virtualizace-na-vasem-pocitaci-pro-spusteni-2n-access-commander-100572533.html)
* [(Spanish) Habilitar la virtualizacion de hardware en BIOS UEFI en Intel VT-X y AMD SVM](https://assistouest.fr/es/habilitar-la-virtualizacion-de-hardware-en-bios-uefi-en-pc-vt-x-y-amd-v/)

### Vagrant Installation Videos

Here are videos on how to install Vagrant on different platforms:

* [Windows](https://www.youtube.com/watch?v=mPBWWu7sZU4)
* [MacOS](https://www.youtube.com/watch?v=kCVsWyR8mbo)
* [Linux](https://www.youtube.com/watch?v=yGviTwD3hWM)

To run all this setup, you need to have Git, Vagrant, and VirtualBox installed on your machine.

## License

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC_BY--NC_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/) This tutorial is released into the public domain by [Agile611](http://www.agile611.com/) under the Creative Commons Attribution-NonCommercial 4.0 International license.

This README file was originally written by [Guillem Hernández Sola](https://www.linkedin.com/in/guillemhs/) and is likewise released into the public domain.

## Contact and support

Please contact [Agile611](http://www.agile611.com/) for further details.

* [Agile611](http://www.agile611.com/)
* Carrer Laureà Miró 309
* 08950 Esplugues de Llobregat (Barcelona)

[![Agile611](https://www.agile611.com/wp-content/uploads/2020/09/cropped-logo-header.png)](http://www.agile611.com/)