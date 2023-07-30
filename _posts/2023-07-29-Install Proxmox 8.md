---
title: Install Proxmox 8 
date: 2023-07-29 12:00:00 -500
categories: [Install,Proxmox]
tags: [Proxmox8,Hypervisor,OS,Proxmox]

---

> Knowledge has three degrees: opinion, science, illumination. The means or instrument of the first is sense; of the second, dialectic; of the third, intuition.
> — <cite>Plotinus</cite>

---

## Install Proxmox 8 

---

## Create Bootable Media

Download proxmox latest install version 

check the hash 
	Get-FileHash .\proxmox-ve_8.0-2.iso SHA256

Install rufus latest version
[Rufus](https://rufus.akeo.ie/)

Create bootable media with defaults plug it in and reboot the host to bios. Set the boot order to the USB. 

Install Proxmox with the following settings
- Select Disk Drive of your choice installing on SD over NVME is smart so you can have all the NVME for VM's
- Select region
- Select static IP, make a name in FQDN that will not change, changing node name is a pain. If clustering make sure you think of a name that fits in your cluster group that is unique. 
- Finalize and install
- Reboot 
- Login to browser at set ip addr.

---

## Scripts for Streamlining Your Homelab with Proxmox VE

Now run post install scripts as the video below describes. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/5axVd19Jris?start=790" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

[Proxmox VE Helper Scripts](https://tteck.github.io/Proxmox/)

Login to MobaXterm and open ssh sessions to all your nodes. 

Run the MultiExec feature to perform the same tasks across all your servers. 

![Pasted image 20230729212802.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230729212802.png)


I selected yes to all except test repo and no to disabling High Availability 

![Pasted image 20230729213034.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230729213034.png)
![Pasted image 20230729213055.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230729213055.png)

Once rebooted its time to setup drives and cluster. 

---

## Setting up Clusters and Drives

Years back I watched one of my favorite tech youtubers Craft Computing. He talks about the setting up local storage at the link below. This is also a good pivot into clustering but I have additional links to clustering below. 


<iframe width="560" height="315" src="https://www.youtube.com/embed/azORbxrItOo?start=285" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


[Learn Linux TV - Proxmox Full Course](https://www.youtube.com/playlist?list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo)

<iframe width="560" height="315" src="https://www.youtube.com/embed/QWyLilz1RqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Click on Datacenter for you most robust host to set it to the 0 node in the cluster. The lower the node number in the cluster the first to fail-over to. 

Click cluster and create new cluster. name pve-cluster IP should be the server ip.

Click join information and copy. 

Head to the second server join cluster. 

### Note:
If you had your port set to a different network range and your node ip needed to be changed in order to sync and join the cluster, go to the host the IP addr assignment needs to be changed and run the below. 

### Changing the IP with a One-Liner

> **TL;DR: here's a one-liner:**  
> `sed -i 's/oldip/newip/g' /etc/network/interfaces /etc/hosts && systemctl restart networking`

The above UI changes are essentially just updating these files:

- `/etc/network/interfaces` - This updates the actual NIC/interface address
- `/etc/hosts` - If a server hostname is in the hosts file, a system won't perform a DNS lookup for that address. Therefore, host/IP pairings in the hosts file _need_ to be correct.

[Change Proxmox IP](https://techbits.io/change-proxmox-ip/#:~:text=First%2C%20go%20to%20System%20%3E%20Network,the%20hostname%20and%20click%20Save.)

Then you need to delete the old cluster files as show in the video below.

<iframe width="560" height="315" src="https://www.youtube.com/embed/xTpaCAq1ruM?start=345" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Remove Node from cluster turn off node you want to remove

```Bash
pvecm nodes
pvecm delnode (name of the node)
reboot
```

Delete Cluster

```Bash
pvecm expected 1
```

Stop the running cluster

```Bash
Systemctl stop pve-cluster
```

Force the nodes to run in local mode

```Bash
pmxcfs -l
```

Delete all the cluster configuration files

```Bash
rm -f /etc/pve/cluster.conf /etc/pve/corosync.conf 
rm -f /etc/cluster/cluster.conf /etc/corosync/corosync.conf 
rm /var/lib/pve-cluster/.pmxcfs.lockfile
```

Stop cluster from running

```Bash
systemctl stop pve-cluster
reboot
```

Maybe need to remove corosync authkey

```Bash
rm /etc/corosync/authkey
```

Once done recreate cluster and join the others. 

![Pasted image 20230729223927.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230729223927.png)

Okey after reboot the nodes clustered and joined. I had to refresh browser on each node and relogin. Use a password manager in browser to make life easier for you when logging into local services or any site. 

All is well and fresh install of network storage replicating across the datacenter with all nodes conneted.

![Pasted image 20230729225100.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230729225100.png)

---

## Installing Windows Server 


[Windows Server 2019 Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)

Download and store the ISO



<iframe width="560" height="315" src="https://www.youtube.com/embed/fupuTkkKPDU?start=44" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


Follow along use the Video as a guide.

Download - [Stable virtio-win ISO](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso)

After Creating the VM like Tim does in the section, `Create our Windows 11 VM and all settings`

Then before firing it up 🔥

You need to mount the virtio driver disk to the VM



