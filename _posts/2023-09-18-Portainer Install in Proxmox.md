---
title: Portainer Install in Proxmox
date: 2023-09-18 12:00:00 -500
categories: [Portainer,Home Lab]
tags: [Home Lab,Proxmox,Container,Portainer]

---

> The best and most beautiful things in the world cannot be seen, nor touched... but are felt in the heart.
> — <cite>Helen Keller</cite>

---

## Portainer Install in Proxmox


Link to the install location for community templates for containers for home labs.
[https://raw.githubusercontent.com/Qballjos/portainer_templates/master/Template/template.json](https://raw.githubusercontent.com/Qballjos/portainer_templates/master/Template/template.json)

Default App Templates location for portainer
[https://raw.githubusercontent.com/portainer/templates/master/templates-2.0.json](https://raw.githubusercontent.com/portainer/templates/master/templates-2.0.json)

Another great repo for portainer installs
[https://github.com/HomelabTim/YouTube-Code/blob/main/portanier](https://github.com/HomelabTim/YouTube-Code/blob/main/portanier)

---

### Installing Portainer Containers - Homer Dashboard

<iframe width="560" height="315" src="https://www.youtube.com/embed/KO9oMXkz0QA?si=O4Gu6MmtrdfrWLjg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

After install there is no port link available so drill into the container and look under logs. 

No configuration found, installing default config & assets

Assets directory not writable. Check assets directory permissions & docker user or skip default assets install by setting the INIT_ASSETS env var to 0

```bash
    1  ls
    2  cd ..
    3  ls
    4  cd portainer/
    5  ls
    6  cd Files/AppData/Config/Homer/
    7  ls
    8  ls -la
    9  sudo chown -R 1000:1000 assets/
   10  ls -l
   11  history
```

Link is up now

![Pasted image 20230918193943.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230918193943.png)

Change the config files and add SSH keys to the server

Add png files over SFTP to the path
```bash
/portainer/Files/AppData/Config/Homer/assets/icons/
```

---



