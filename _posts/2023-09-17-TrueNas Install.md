---
title: TrueNas Install
date: 2023-09-17 12:00:00 -500
categories: [NAS,TrueNAS]
tags: [NVME_Passthrough,PROXMOX,NAS,Truenas]

---

> Never pretend to a love which you do not actually feel, for love is not ours to command.
> — <cite>Alan Watts</cite>

---

## TrueNas Install on Proxmox with hardisk passthrough

<iframe width="560" height="315" src="https://www.youtube.com/embed/7lwqdVmCiI0?si=MPEwM0kAT91bY0g7" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Follow this guide to a T but use this guide to wipe the disks if needed.

[Wipe locked disk in proxmox](https://forum.proxmox.com/threads/pve7-wipe-disk-doesnt-work-in-gui.92198/page-2)

Also consider using this command instead if your using an NVME drive

```bash
qm set <VMID> -virtio0 /dev/disk/by-id/nvme-driveserial#
```

Also see:
[An Introduction to ZFS A Place to Start - ServeTheHome](https://www.servethehome.com/an-introduction-to-zfs-a-place-to-start/)

<iframe width="560" height="315" src="https://www.youtube.com/embed/Fg8wlRvGkK4?si=i6IAKsZhsNjVuMG9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/M3pKprTdNqQ?si=A2puIm84D0R_6lan" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Awesome Homelab setup video that goes over Proxmox, TrueNAS, Portainer, and Wireguard install.

<iframe width="560" height="315" src="https://www.youtube.com/embed/_sfddZHhOj4?si=9BQcFo9ttTf1tffj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/videoseries?si=n36TKlw_5r25h-6L&amp;list=PLXJCC3PRO2aCDQStpAYt9_SzHnVAmhq3_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

#Add_More_Later

