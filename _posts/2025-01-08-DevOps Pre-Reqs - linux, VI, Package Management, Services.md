---
title: DevOps Pre-Reqs - linux, VI, Package Management, Services
date: 2025-01-08 12:00:00 -500
categories:
  - VI
  - Dev
tags:
  - Code
  - Dev
  - DevOps
  - VI
---

> [!quote] Iron rusts from disuse; water loses its purity from stagnation... even so does inaction sap the vigour of the mind.
> — Leonardo da Vinci

---

## DevOps Pre-Reqs - linux, VI, Package Management, Services

## Linux Basic commands

```bash
touch filename.txt
```

```bash
echo "content of the file" > filename.txt
```

```bash
cp file/folder/file.txt /folder/
```

```bash
rm -r /dir/
```

```bash
mkdir -p /long/dir/here/forever/tiered/
```


---

## VI Editor

Move around the console:

left, right, up, down OR H,L,K,J  < same order as arrow keys

Delete:
x OR dd

Copy & Paste:
yy OR p

scroll page up or down:
ctrl+u OR ctrl+d

Command: 
:

Save
:w OR :w filename

quite(discard):
:q

Save+quit:
:wq

Find:
/of to find of and they will be highlighted and move using 'n'

`yy` - copy a line  
`dd`- delete  
`p` - paste  
`x` - delete a single character


`u` - undo the change  
`ctrl + r` - redo the change

Basically, cut(`dd`) and paste(`p`) the line.


---

## More Linux Commands

```bash
whoami
```

Show the users system info
```bash
id
```

```bash
su aparna
```

```bash
ssh user@192.168.0.1
```

Must be in the /etc/sudoers file
```bash
sudo ls /root
```

Save results to a file from curl
```bash
curl url -o
```


```bash
wget url -O file.txt
```

wildcard and find OS info on linux servers
```bash
ls /etc/*release*
```
```bash
cat /etc/*release*
```

Run `cd /home/thor; curl -O url/dummy.pdf`

or

`wget url dummy.pdf -O /home/thor/dummy.pdf` to download file.

---

## Package Management

### RPM 

Install
```bash
rpm -i telnet.rpm
```

Uninstall
```bash
rpm -e telnet
```

Query Package
```bash
rpm -q telnet
```

### YUM
Uses RPM underneath and install all dependencies

install from whatever package management server you set found at `/etc/yum.repos.d` sometimes you need to update so yum can find what you need. 
```bash
yum install ansible
```

Yum Repos check
```bash
yum repolist
```

```bash
ls /etc/yum.repos.d/
```

See URL of packages
```bash
cat /etc/yum.repos.d/CentOS-Base.repo
```

```bash
yum list ansible
```

```bash
yum remove ansible
```

Find all repos to install
```bash
yum --showduplicates list ansible
```

Install only the repo using package name and version number no need to include the .el7 
```bash
yum install ansible-2.4.2.0
```

Run `rpm -q openssh-server python3 ansible telnet` . Alternatively run `rpm -qa` to list all installations.

---

## Services

These services need to follow order of startup operations. 

```bash
service httpd start
```

OR
```bash
systemctl start httpd
```

```bash
systemctl stop httpd
```

```bash
systemctl status httpd
```

Configure to start at startup
```bash
systemctl enable httpd
```

Disable at startup
```bash
systemctl disable httpd
```

Start a webserver on port 5000 is the my_app.py file
```bash
/usr/bin/python3 /opt/code/my_app.py
```

Then check it 
```bash
curl http://localhost:5000
```

Then run start or enable it
```bash
systemctl start my_app
```

systemd unit files are located at `/etc/systemd/system`

my_app.service
```bash
[Service]
ExecStart=/user/bin/python3 /opt/code/my_app.py
```

```bash
systemctl deamon-reload
```

```bash
systemctl start my_app
```

my_app.service

```bash
[Unit]
Description=My python web app

[Service]
ExecStart=/user/bin/python3 /opt/code/my_app.py
ExecStartPre=/opt/code/configure_db.sh
ExecStartPost=/opt/code/email_status.sh
Restart=Always


[Install]
WantedBy=multi-user.target
```


/lib/systemd/system/docker.service
```bash
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
BindsTo=containerd.service
After=network-online.target firewalld.service containerd.service
Wants=network-online.target
Requires=docker.socket

[Service]
Type=notify
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/ run/containerd/containerd.sock
ExecReload=/bin/ki11 -s HUP $MAINPID
Restart=always
StartLimitBurst=3
StartLimitInterva1=60s
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity

[Install]
WantedBy=mu1ti-user.target
```

You can check status of `app` service by running `systemctl status app`, where you can also find location for service file under `/usr/lib/systemd/system/`.

Check the value for the directive `ExecStartPre` in the file `/usr/lib/systemd/system/app.service`  
  
OR  
  
simply run the command - `systemctl cat app.service` and see the value.