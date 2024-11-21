---
title: Google Chronicle Phishing Challenge
date: 2024-03-25 12:00:00 -500
categories: [Chronicle,SIEM]
tags: [SIEM,Chronicle,Detections,Phishing]

---

> [!quote] A friend is one who knows you and loves you just the same.
> — Elbert Hubbard

---

## Google Chronicle Phishing Challenge

We've been targeted by a **Phishing** campaign! An employee has reported signin[.]office365x24[.]com as suspicious. That user probably hasn't been phished, but who has?

Pricing isn't based on data volume, so enrichment of additional security telemetry is a google strong suite. 

Search the reported event IOA.
`[signin.office365x24.com]`

Drill into legacy View and you can see six assets accessed this domain. 

Leverage DHCP, DNS and Web Proxy Data. Hostnames are displayed instead of IP bc they likely have changed already.

Domain View contains context into WHOIS, Resolved IP's, Sibling Domains, and VT and Threat Intel Feed data. 

Found this handy blog post bc my instance was displaying associated threat intel data:

[https://medium.com/@cavan.fowler/perform-a-query-with-splunk-49c2eda9b333](https://medium.com/@cavan.fowler/perform-a-query-with-splunk-49c2eda9b333)
My instance of the Warstory did not have the "**ET Intelligence Rep List**" Data displaying that the site was a "**Drop site for logs or stolen credentials**." Luckily the blog post above displayed the true working form of the Warstory. 

![Pasted image 20240326094536.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326094536.png)

The assets first accessed the cred stealer domain on 7-09-2023. This could be Phishing and now we have the Scope of users that where impacted. 

In Timeline view you see all the Status and request type information. 

![Pasted image 20240326094804.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326094804.png)


You can use the search bar to search and highlight data within the timeline data. 

To get additional context into backstory of the phishing campaign we will pivot into IP view to check the resolved IP's. NOTE: IP's will often switch domains once they are burned and the hosting provider re-assigns the dynamic Public IP to another VM instance/node in that same cluster.  

Drilling into the IP we see 2 more assists. 

![Pasted image 20240326095455.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326095455.png)

Enrichment of past data is important, this is key because the amount of time an attacker is in your environment before detection can be a long time. 

Drill into the domains... Notice the q in goo*q*le 

![Pasted image 20240326095706.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326095706.png)

We can also get visibility into this information by looking into the IOC Matches inside the IOC Matches dashboard. 

The threat intel feeds are coming from **ET Intelligence** feed, we have feeds from the **US Department of Homeland Security**, **ESET**, an **OSINT Watchlist** currently.

Zoom into IOC Ingest Times 

![Pasted image 20240326101555.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326101555.png)

![Pasted image 20240326101635.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326101635.png)

Unlike other SIEM's there aren't **schedule searches** to look for retroactive matches. **Chronicle** does this for you **dynamically**.


The summary of the information provided is as follows:

- A user reported a phishing attack.
- The investigation revealed several compromised assets related to the attack.
- Further analysis based on the IP address associated with the compromised assets led to the discovery of:
	- Additional affected assets
    - Two separate domains involved in the attack
    - An IP address that requires further examination

In essence, what started as a single user-reported phishing incident has expanded into a larger security issue involving multiple assets, domains, and an IP address that needs to be investigated further.

## Further Incident Response Actions

Further digging and following IR would be warranted here: 
1- Block IP's.
2- Scan Hosts for Malware.
3- Reverse Malware.
4- Look for signs of priv esc, persistence, lateral movement, or exfiltration.
5- Contain the device if necessary.
6- Force users to change there password on next login. 


