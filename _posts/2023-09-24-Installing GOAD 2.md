---
title: Installing GOAD 2 (Game of Active Directory)
date: 2023-09-24 12:00:00 -500
categories: [LABS,Pentesting]
tags: [Pentesting,Ubuntu,AD,GOAD]

---

> He who is contented is rich.
> — <cite>Laozi</cite>

---

## Installing GOAD 2 (Game of Active Directory)

### Getting Started

First decide how you want to role the lab out. I tried on proxmox and ran into some scripting issues that I'm still working out. I decided to pivot to installing the Lab on a secondary drive on my old PC. 

The PC is beefy enough to handle the load. 

Just install an Ubuntu, Parrot, or Kali VM. Flash iso to usb and fresh install on the NUC. 

I use rufus to flash iso's.
[https://rufus.ie/en/](https://rufus.ie/en/)

If your running into long linux host install times boot into safe graphics mode and install. 

Once the host is rebooted and is in a fresh install state. Run the following commands if you want to access the host from your main workstation via rdp. 



If your intrested search, "How to secure the RDP setup?"

More tips for spinning the lab up on a Linux host and other resources can be found here: 

[https://github.com/quincyntuli/GOAD-v2-Installation-Notes](https://github.com/quincyntuli/GOAD-v2-Installation-Notes)

[https://mayfly277.github.io/posts/GOADv2/](https://mayfly277.github.io/posts/GOADv2/)

[https://reconshell.com/goad-game-of-active-directory/](https://reconshell.com/goad-game-of-active-directory/)


If you want to use VMWare there is another guide here:



During the build I used 








