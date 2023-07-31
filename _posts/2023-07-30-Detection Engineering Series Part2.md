---
title: Detection Engineering Series Part 2 - Setting up a lab
date: 2023-07-30 12:01:00 -500
categories: [Engineering,Detections]
tags: [SOC,Engineering,Detections,Lab]

---

> All is flux; nothing stays still.
> — <cite>Heraclitus</cite>

---

## Detection Engineering Series Part 2 - Setting Up a Lab

If you dont have HyperVisor Installed pick one:
1. Hyper V
2. VirtualBox
3. Vmware
4. Parrallels
5. ESXi
6. Proxmox

Install the OS's of your choosing. Here are just a few:

- [Ubuntu Desktop or Server 22.04.02](https://releases.ubuntu.com/jammy/ubuntu-22.04.2-desktop-amd64.iso)
- [Windows Evals Server, Workstations, etc](https://www.microsoft.com/en-us/evalcenter/)
- [Windows Developer VM](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/)
- [Parrot OS](https://parrotsec.org/download/)
- [Kali Platforms](https://www.kali.org/get-kali/#kali-platforms)
- [Arch Linux](https://gitlab.archlinux.org/archlinux/arch-boxes/)
	- [BlackArch](https://blackarch.org/downloads.html) - `My favorite for learning how to install from scratch`
- [Proxmox VE Helper Scripts Scripts for Streamlining Your Homelab with Proxmox VE](https://tteck.github.io/Proxmox/) - `Install scripts for several other apps, services and OS's from tteck's github.io site.`

I'm using Proxmox so if I wanted to I could loud the images to the local server storage location or add them to the NAS shared storage location and all the nodes could access the iso images or QCOW files.

When you install Ubuntu and make sure you check `qemu agent` on install if you didn't turn it on in option under the VM then install qemu

	sudo apt-get install qemu-guest-agent 

Dont reboot on proxmox just shutdown then restart and then the IP address under the VM's Summary tab > IP's

Once you install the Linux hosts or other hosts update them, `IF` you don't want them to be vulnerable for testing. 

Once all hosts are installed make sure the hosts can ping each other. 

I ran into some bridged network settings issues with parrotOS my fix was to select the NIC I was using out of two instead of auto. But I found this awesome bridged network fix guide for vmware. [Fix VMware Bridge Net Problems](https://appuals.com/fix-vmware-bridged-network-not-working/)

If you want to virtualize your hosts install them using `Techno Tim's` guides. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/YR9SNDD8WB4?start=99" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Once you install the Ubuntu Device go to VM > Hardware > Edit Memory > Check advanced > Check Ballooning > Set min memory to low end and then adjust high end memory max.

![[Pasted image 20230730162417.png]]

Install openssh server if necessary 

	sudo apt-get install openssh-server

[SSH Configuration Quicky](https://www.cyberciti.biz/faq/ubuntu-linux-install-openssh-server/)

Setup Connections on MobaXterm your in.

Change the default port to not 22 and add fail2ban to the server if you really want to up the game on your SSH enabled host. 

