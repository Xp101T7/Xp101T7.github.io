---
title: DevOps Pre-Reqs - Networking, DNS
date: 2025-01-08 12:00:00 -500
categories: [Networking,DNS]
tags: [Dev,DevOps,Networking,DNS]

---

> [!quote] The heart has eyes which the brain knows nothing of.
> — Charles Perkhurst

---

## DevOps Pre-Reqs - Networking, DNS


![[Pasted image 20250111204352.png]]

```BASH
ip link
```



```BASH
ip addr add 192.168.1.10/24
```



```BASH
ping 192.168.1.11
```



```BASH
ip addr add 192.168.1.0/24 via 192.168.2.1 
```



```BASH
ip addr add 172.217.194.0/24 via 192.168.2.1 
```



```BASH
route
```



```BASH
ip route add 192.168.1.9/24 via 192.168.2.1
```



```BASH
ip route add default via 192.168.2.1
```



```BASH
route
```



```BASH
ip route add 192.168.1.0/24 via 192.168.2.2
```



```BASH
route
```


Check if you can fwd packets to another FW and turn it on by setting it to `1`
```bash
cat /proc/sys/net/ipv4/ip_forward
```

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

```bash
ping <IP_4_FWD_NET>
```

Persist across reboots with 

```JSON
vi /etc/systctl.conf
```

net.ipv4.ip_forward = 1



---

## Key Commands


List and modify interfaces on the host
```bash
ip link
```

to see ip addr assigned to Interfaces
```bash
ip addr
```

Adding routes
```bash
ip addr add 10.0.0.0/24 dev eth0
```


```bash
route 
```


```bash
ip route add 10.0.0.0/24 via 10.0.0.1
```


`Note:` Please ensure to execute the commands on their respective app nodes.  
  
For example:-  
  
To assign new IPs for `app01` , the below command need to be executed on `app01`.  
  
For app01: `sudo ip addr add 172.16.238.15/24 dev eth0`

Please follow the same procedure for other app nodes as well:  
  
For app02: `sudo ip addr add 172.16.238.16/24 dev eth0`

For app03: `sudo ip addr add 172.16.239.15/24 dev eth0`

For app04: `sudo ip addr add 172.16.239.16/24 dev eth0`

```bash
ip route add 10.0.0.0/24 via 10.0.0.1
```

Added route across

---
## DNS

```bash
ping 192.168.1.11
```

```bash
ping db
```

DNS hostname storage
```bash
cat >> /etc/hosts
```
Map Host
```bash
<IP> host_mapping
```


```bash
ping db
```

```bash
ssh db
```

Name resolution
```bash
curl http://www.google.com
```

Check hostname
```bash
hostname
```


```bash
cat /etc/resolve.confg
```

```bash
nameserver <IP>
```

Usually you can look at etc/hosts then DNS server by default but you can edit 
```bash
cat /etc/nsswitch.conf
```
Add order for DNS looks into its host file then dns but can swap
```bash
hosts: files dns
```

So if no entry then you need to map trusted DNS nameservers
```bash
cat >> /etc/resolv.conf
```
Nameserver
```bash
nameserver 8.8.8.8
```

Top level domains

```bash
.com, .net, .org, .io, .gov
```

root = .
domain = google
sub domain = mail, drive, nfs, pay, sql, hr

DNS caching is occurring on the org side for a few seconds or minutes. 


To shortcut some cmd and apps you can add to the resolv.conf file a search paramater
```bash
cat >> /etc/resolv.conf
```

```bash
search myorg.tech prod.myorg.tech
```

to get to web.myorg.tech we can now just search the sub domain. 
```bash
ping web
```

CNAME = map one domain or sub to another

Can search for servers using 
```bash
nslookup hostname
```

```bash
dig hostname
```


