---
title: "Home VPN"
date: 2021-07-25T17:05:47+12:00
draft: false
toc: false
images:
tags:
  - Networking
  - Linux 
  - VPN
---

![padlock](padlock.png)

As the amount of services I have running on my home-network increases it's become
more and more sensible to have a secure way of accessing them while out of the 
house.

Wireguard is a free and open-source program that implements encrypted virtual
private networks that has support for Windows, MacOS, Linux and Android. 
I chose wireguard because it supported by NetworkManger and all devices I use 
run NetworkManger.

This blog post refers to a Linux peer but the steps for connecting to a client 
are pretty much ubiquitous for all clients.  

## Dependencies 
  - Wireguard client package 
  - Static IP or Dynamic DNS 
  - Linux based OS to install Wireguard server 
  - Git

When we create the configuration files for our VPN we need to point to the 
clients to an address that will always be up. If your ISP does not provide you 
the option to obtain a static IP address the alternative is to set up a Dynamic 
Domain Name so even if your IP changes users can still be directed to the 
correct location.

### Installing the Wireguard server
The wireguard server can be installed manually however it is much simpler to 
use a script to automate the process, as it automatically handles the creation 
of the encryption keys and certificates, otherwise we would have to generate 
these ourself.

[Nyr's](https://github.com/Nyr/wireguard-install) wireguard script on github is 
what I used to install wireguard and it features highly configurable options for installing and managing wireguard post-installation.

Create a directory for the wireguard installation, `cd` and paste the following 
command:
```
$ wget https://git.io/wireguard -O wireguard-install.sh && bash wireguard-install.sh
```
To run the script we need to run it as root:
```
$ sudo bash wireguard-install.sh
```
When it asks, "`What is the public IPv4 address or hostname?`", enter your 
static IP or Domain name here.

Leave the port as default 

Pick whatever name you want for your first client and DNS server for the client.
I set my client name to `Dan` and chose my DNS server as `Google`

Once it has setup it will produce a config file in `/root/<clientName>.conf`
or if you are using an Android device you can simply scan the QR code and you 
are done.
### Connecting a Linux Client
Ensure all packages are up to date and install wireguard-tools package.
```
$ sudo pacman -Syyu wireguard-tools
```
Copy the configuration file created earlier to a safe place on your client 
machine.

Some desktop environments may have options in the settings for adding a VPN 
connection however I found the experience to be a bit hit and miss so using the 
NetworkManger command line interface -`nmcli` is the simplest solution.
```
$ nmcli connection import type wireguard file /path/to/<clientName>.conf
```
The default configuration files are in `/etc/wireguard/wg0.conf`. I am happy 
with the default settings however we easily change things like the address subnet
interface and port here also every subsequent peer we create is added to this 
file.

The config files for the peers also have options like specifying allowed ip 
addresses, all these options are we documented in the Arch-wiki and the 
Wireguard documentation. 

---
### References and links
- [Padlock vector image used under creative commons license 4.0](https://commons.wikimedia.org/wiki/File:Antu_object-unlocked.svg)
- [Nyr Wireguard install script](https://github.com/Nyr/wireguard-install)
- [Arch-wiki](https://wiki.archlinux.org/title/WireGuard)
- [Wireguard](https://www.wireguard.com/quickstart/)