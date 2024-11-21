---
title: DNS Enumeration Tools - (Created by ME)
date: 2023-10-16 12:00:00 -500
categories: [Recon,DNS]
tags: [RedTeam,Enumeration,Recon,DNS]

---

> The wise man does not lay up his own treasures. The more he gives to others, the more he has for his own.
> — <cite>Laozi</cite>

---

## DNS Enumeration Tools - (Created by ME)

I have created some python DNS enumeration tools. There is a DNS recon tool and Sub Domain Recon Tool.

In order to run these tools all you have to install python from python.org or from the Microsoft store.

Then navigate to my repository and download the zip files > extract > run > allow the file to run > follow directions below > results.

[https://github.com/Xp101T7/DNSEnumpy](https://github.com/Xp101T7/DNSEnumpy)

I will code sign these executables soon.

\###WARNING NOTICE###
The tools and products in this repository are for educational and testing purposes only. Unauthorized use of these tools for attacking targets without prior mutual consent is illegal. The repository owner is not responsible for any illegal use of these tools or products. Please use responsibly.
 

---

### DNS Enumerator

Navigate to `%USERPROFILE%\Downloads\DNSEnumpy-main\RedTeam\Recon\DNS`

To Enumerate the DNS Records for a domain add the domain name to the text box labeled, "Enter domain here", Fetch DNS Records and save the file. 

![Pasted image 20231016211849.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020231016211849.png)

![Pasted image 20231016211923.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020231016211923.png)

---

### Sub Domain Finder

Navigate to `%USERPROFILE%\Downloads\DNSEnumpy-main\RedTeam\Recon\SubDomain`

To Enumerate the DNS Records for a domain add the domain name to the text box, labeled, "Enter Domain",  add the subdomain file you want to pass into the sub domain finder, Run, and Save the file. 

![Pasted image 20231016212436.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020231016212436.png)

![Pasted image 20231016212502.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020231016212502.png)

If all sub domains are PWN_ABLE from your list then the domain is enumrating wonky!!! I'm looking into making the tools output errors when this is occuring... Future Release...

Also adding a Completed Notification!!! Coming Soon...

---

The Code can be found by Reversing the files...


