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

![Pasted image 20230730162417.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230730162417.png)

Install openssh server if necessary 

	sudo apt-get install openssh-server

[SSH Configuration Quicky](https://www.cyberciti.biz/faq/ubuntu-linux-install-openssh-server/)

Setup Connections on MobaXterm your in.

Change the default port to not 22 and add fail2ban to the server if you really want to up the game on your SSH enabled host. 

See my other blog article for Blue and Red MSFT Defender disabling commands. 

[Blue and Red MSFT Defender Commands](https://heckintechin.tech/posts/BandR-MSFT-Defender/)

Tamper protection essentially locks Microsoft Defender Antivirus to its secure, default values, and prevents your security settings from being changed through apps and methods such as:  

- Configuring settings in Registry Editor on your Windows device
- Changing settings through PowerShell cmdlets
- Editing or removing security settings through Group Policy

[Turn-of-tamper-protection forum](https://www.elevenforum.com/t/turn-on-or-off-tamper-protection-for-microsoft-defender-antivirus-in-windows-11.3973/)

![Pasted image 20230730194751.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230730194751.png)

Got to manage settings

Turn off Tamper Protection since win 11 its tough to disable via terminal

![Pasted image 20230730194840.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230730194840.png)

Now from terminal type gpedit.msc

Computer config > admin templates > windows components > msft defender antivirus > turn off msft defender antivirus > enabled

![Pasted image 20230730195157.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230730195157.png)

Real time protection folder > turn off real time protection and behavoir monitoring

![Pasted image 20230730195257.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230730195257.png)

---

## Installing Zeek

[Installing Zeek](https://docs.zeek.org/en/master/install.html)

SSH into your box using MobaXterm

	su root

Install curl

Might have to kill the lock bc if its held by a process you need to kill that process to unlock the lock

	kill -9 \<pid\>

Go to quick start 

[ZeekQuickStart](https://docs.zeek.org/en/master/quickstart.html)

cd to $PREFIX location

	cd /opt/zeek/etc/

Change networks.cfg to monitor whatever network range we want. 

Change node.cfg this is where we specify interface but first grab the interface 

	ip a | grep -A 4 -B 4 192

	sudo nano node.cfg

Add the node to interfaces

Can just type zeekctl so go to the PREFIX install location 

Then go to the bin directory

	sudo ./zeekctl

Proxmox has promiscuous mode enabled by default.

Add binary path to the shell

	export PATH=$PATH:/opt/zeek/bin

You will have to reload the profile before you run the command each time. 

	. .bashrc

Then set the zeek logs to ingest logs to the SIEM's parser using JSON

	nano /opt/zeek/share/zeek/site/local.zeek

Add the load policy if you are wanting to send these logs to your SIEM. JSON is easier to parse than most ingestion formats. 

	@load policy/tuning/json-logs

Save start zeek and run

	zeekctl
	deploy
	status

Need to turn off json-logs policy if we want to use  zeek-cut 

Will be coming back to setting up the configs to send zeek logs to the Splunk parsers in this video at a later date. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/Hdh4mOozsg8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>











