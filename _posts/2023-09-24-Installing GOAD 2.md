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

See my other article here:

#LinkLater

If your interested search, "How to secure the RDP setup?"

RDP into the host and then 

More tips for spinning the lab up on a Linux host and other resources can be found here: 

[https://github.com/quincyntuli/GOAD-v2-Installation-Notes](https://github.com/quincyntuli/GOAD-v2-Installation-Notes)

[https://mayfly277.github.io/posts/GOADv2/](https://mayfly277.github.io/posts/GOADv2/)

[https://reconshell.com/goad-game-of-active-directory/](https://reconshell.com/goad-game-of-active-directory/)


If you want to use VMWare there is another guide here:

[https://github.com/lkarlslund/deploy-goad](https://github.com/lkarlslund/deploy-goad)

For proxmox (Needs a more thorough and detailed guide with fix examples or implementation considerations. That is not a dig at the current guide, props to Mayfly but there is just so many things that can go wrong with this lab in proxmox.) 

[https://mayfly277.github.io/posts/GOAD-on-proxmox-part1-install/](https://mayfly277.github.io/posts/GOAD-on-proxmox-part1-install/)

Currently I have an open issue to help solve a Shell Elevation problem with winRM when creating the template builds with packer. I'm hoping to find the time to rebuild this lab here or in VMware so I can spin it up without having to kill my 3rd proxmox node just so I can have the secondary harddrive method host this lab. 

[https://github.com/Orange-Cyberdefense/GOAD/issues/123](https://github.com/Orange-Cyberdefense/GOAD/issues/123)


During the build I used Ubuntu 22.04 and when i ran the `apt install virtualbox` command vagrant up failed. 

So the fix was to install an older version of virtualbox which can be done by downloading the version from the site below. 

[https://www.virtualbox.org/wiki/Download_Old_Builds_6_1](https://www.virtualbox.org/wiki/Download_Old_Builds_6_1)

Then run deb package build with apt install:

```bash
sudo apt install <~/Downloads/<debinstall>>
```

Then the rest of the build process when well except for one portion. 

I ran into an issue at the [Lets Play](https://github.com/quincyntuli/GOAD-v2-Installation-Notes#13-lets-play) stage of the build where `VBOXMANAGE` was not able to deploy DC03 my fix was to lower VM resources in the vagrantfile to: (Shout out to @gr4sku11 over on discord.)

```bash
v.memory = 1024
v.cpus = 1
```

I also had to remove these already created conflicting VM resource directories: (Shout out to @phr0zengh0st2020 on discord) 

```basb
rm -r ~/.vagrant.d/boxes/StefanScherer-VAGRANTSLASH-windows_2016/
rm -r /home/user/GOAD/.vagrant/machines/DC03/
rm -r "/home/user/VirtualBox VMs/WindowsServer2016*"
```

You can do this for any VM box that does not spin up and throws the `VBoxManage` error when appliance import fails.

After that just follow the rest of the guides linked above. 

If your using Ubuntu you can test the lab with the crackmapexec command by installing it from:

[https://snapcraft.io/install/crackmapexec/ubuntu](https://snapcraft.io/install/crackmapexec/ubuntu)

Also consider following qdada video series located here:

<iframe width="560" height="315" src="https://www.youtube.com/embed/videoseries?si=hDitx0MCP15zF0kr&amp;list=PLMl69W33-fZVO15OG1p_3RtSS8Bes4DHB" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

I modified the VBOX networking to map it to a separate subnet on my network so i could ship the logs and detect all the things. Also, I can now pentest this from any flavor of pentest distro I want. 

GOAD is legit, but fuck is it not an easy setup on proxmox locally. 

I love the Pentesting potential this lab has...

The global technics available on the lab are: 
(Copied this from Mayfly's GOADv2 Install Guide.)

- Password reuse between computers (PTH)
- Spray User = Password
- Password in description
- SMB share anonymous
- SMB not signed
- Responder
- Zerologon
- Windows defender
- ASREPRoast
- Kerberoasting
- AD Acl abuse (forcechangepassword, genericall, genericwrite,…)
- Unconstraint delegation
- Ntlm relay
- Constrained delegation
- MSSQL exec
- MSSQL trusted link
- MSSQL impersonate
- IIS service to Upload malicious asp
- Multiples forest
- Anonymous RPC user listing
- Child parent domain escalation
- Certificate and ldaps avaiable
- ADCS - ESC 1/2/3/8
- Certifry
- Samaccountname/nopac
- Petitpotam unauthent
- Printerbug
- Drop the mic
- Shadow credentials
- Printnightmare
- Krbrelayup
- …














