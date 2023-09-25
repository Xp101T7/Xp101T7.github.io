---
title: Installing XRDP Linux
date: 2023-09-24 12:00:00 -500
categories: [XRDP,Linux]
tags: [Ubuntu,Linux,XFCE,XRDP]

---

> My friends are my estate.
> — <cite>Emily Dickinson</cite>

---

## Installing XRDP Linux

**Update package lists and install the `xfce4` desktop environment (optional)**

```bash
sudo apt update sudo apt install xfce4 xfce4-goodies
```

Then auto switch to this at login.

```
sudo update-alternatives --config x-session-manager 
```

And choose `/usr/bin/startxfce4`

 **Install `xrdp`**

```bash
sudo apt install xrdp
```

### Configure xRDP

 **Allow xRDP to use your user's session**

This is an optional security command.

```bash 
sudo adduser xrdp ssl-cert
```

**Configure `xrdp` to use `xfce4` (optional)**

- Open the `.xsession` file with a text editor (create the file if it doesn't exist).

```bash 
nano ~/.xsession` 
```

Add the following line to the file:

```bash
xfce4-session
```

- Save and exit the text editor.

 **Restart the `xrdp` service**

```bash
sudo systemctl restart xrdp
```

### Firewall Configuration

**Allow RDP traffic through the firewall (if enabled)**

```bash
sudo ufw allow 3389/tcp
```

### Test Connection

**Connect to your Ubuntu machine**
- Use an RDP client like Microsoft's Remote Desktop Connection and connect to your Ubuntu machine by specifying its IP address and port 3389.

### Secure the Connection

Since you're interested in cybersecurity, it's worth mentioning that you should also take measures to secure your RDP connection. This can include:

- Using strong authentication methods.
- Limiting the users who can connect via RDP.
- Setting up a VPN tunnel for RDP connections.

This provides a basic setup. There are many more advanced configurations possible, including setting up SSL certificates for RDP or implementing 2FA (Two-Factor Authentication).

More info at:
[https://chat.openai.com/share/959374ff-0dcd-4992-b256-8b1d974df1a5](https://chat.openai.com/share/959374ff-0dcd-4992-b256-8b1d974df1a5)

