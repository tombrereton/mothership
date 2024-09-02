# Introduction
Here we use a home server as a host for several Virtual Machines (VMs). The aim is to host a Game Server,
Seafile, Home Assistant, and have a playground for running web servers.

The architecture will include the host (aka Mothership), 1 public facing VM on it's own Virtual Network (VNet) and
1 private VM also with it's own network. We will expose the public VM to the internet using a network bridge on the
host and then setup port forwarding on the router to the public VM. We'll also add a VPN so that we can connect to
the host through the internet for any on the road maintenance.

# Table of Contents
- Setup Home Server with Ubuntu Server
  - Add ssh public key to host (aka Mothership) for passwordless ssh
  - Configure router to use fixed ip for Host Server
- Create Virtual Machines on host
  - Configure network bridge so VMs can access the local network directly
  - Install libvirt and dependencies for creating and managing VMs
  - Configure router to use fixed ip for public VPN
  - Configure public virtual network and VM with custom domain so it can be accessed from the internet
- Add Virtual Proxy Network (VPN) to Host
  - Configure VPN so that host can be accessed from the internet

---

# Setup Home Server with Ubuntu Server
This guide is assuming your you have a Mac, Linux, or Unix system and have access to a bash shell.

## Create Ubuntu Server USB Installer
- Download Ubuntu Server iso file from [Ubuntu](https://ubuntu.com/download/server) for this guide we are using `Ubuntu 20.04.1 LTS`
- Insert USB drive that has a lest 4GB of space
- Open terminal and run `diskutil list` to find the disk number of the USB drive
- Run `diskutil unmountDisk /dev/diskX` where `X` is the disk number of the USB drive
- Run `sudo dd if=~/Downloads/<ubuntu-server>.iso of=/dev/rdiskX bs=1m` where `<ubuntu-server>` is the name of your iso and `X` is the disk number of the USB drive
- Wait for the command to finish and then eject the USB drive using `diskutil eject /dev/diskX`

## Install Ubuntu Server
Here we create the host server, setup passwordless ssh and configure the router to use a fixed ip for the host server.

Optional Steps:
- Add ssh public key to Github account, this will be used later in setup

Installation Steps:
- Ensure the host server is connected to the internet with an ethernet cable (or you can configure wifi during installation)
- Connect a monitor and keyboard to the device
- Insert the USB drive into the host server and boot it up
- Follow the installation instructions and choose the following options (some straight forward steps are skipped)
  - Choose `Install Ubuntu Server` or similar
  - Configure the hostname as what you want to call the host server, this will show up on the network
  - Configure the username and password, this will be the user you use to ssh into the host server and for sudo access
  - Choose `Install OpenSSH server` so you can ssh into the host server
  - Choose add ssh public key if you have added it to your Github account, this will allow you to ssh into the host server without a password
- After the installation is complete, it will ask you to remove the installation media and reboot the host server
- Once the host server has rebooted, take note of the ip address as you use this in following steps.
  - If it is not shown on the screen you can run `ip a` to find the ip address
  - It should show up as `inet <ip-address>/24` where `<ip-address>` is the ip address of the host server
- You can now ssh into the host server using `ssh <username>@<ip-address>`

## Configure router to use fixed ip for Host Server
- Login to your router and find the DHCP settings
- Add a new static ip for the host server, this will ensure the host server always has the same ip address
- For my TP-Link router I go to `Advanced > Security > IP & Mac Binding` and in the ARP List I select the ip address of the host server and click `Bind`

## Add a Hostname for a friendly ssh experience
Here we add a host entry to your local machine so you can ssh into the host server using a friendly name.

- Open terminal and run `sudo nano /etc/hosts`
- Add the following line to the end of the file `<host-name> <ip-address>`, where `<host name>` is the name you want to use to ssh into the
host server and `<ip-address>` is the ip address of the host server
- When sshing into the host server you can now use `ssh <username>@<host-name>`

---

# Create Virtual Machines on host
