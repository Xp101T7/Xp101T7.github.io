---
title: PowerPlant Data Breach
date: 2025-09-04 12:00:00 -500
categories: [DFIR,Diagrams]
tags: [Diagrams,DFIR,Attack,Breach]

---

> [!quote] Do not follow where the path may lead. Go, instead, where there is no path and leave a trail.
> — Ralph Waldo Emerson

---

## PowerPlant Data Breach

-  DOH


![[Pasted image 20250904211848.png]]


1- An attacker sends a spear phishing message with the subject "Free Flaming Moe’s in the Cafeteria at after work: Details in Attachment" containing a malicious Microsoft Word attachment to Homer Simpson who opens the attachment and enables Macros when prompted to view the sweet, sweet Flaming Moe’s details.

The user clicked on the phishing email Microsoft 365 defense can sandbox malicious emails and scan them. Its default policy to block macro based emails if this is not set and should be then notify an administrator of this policy change recommendation.  Email security providers can also aid in detecting and scanning ingress emails for malicious contents and blocking attacks in real time. Run user awareness programs within your org at regularly annual cadences and on user phish test volitions from within your awareness program. 

https://learn.microsoft.com/en-us/microsoft-365-apps/security/internet-macros-blocked

2- Once opened, a macro is executed which runs a PowerShell command that establishes a command and control (C2) channel to a domain (https://d35fkdjh4gt99.cloudfront.net, 52.85.89.218) which ultimately resolves to a machine controlled by the attacker (Frankenstein Grimes) in Amazon's EC2 cloud.

Leverage Detections that look for child shell processes spawned from Office documents is a recommended detection strategy along with blocking the use of a files that hold the MOTW attributes that contain macros via group policies across your domain. Leverage SOAR tooling to malware scan macro word documents with MOTW attributes via a third party artifact scanning tool or flag the file and reverse engineer suspicious macros investigating potential attacker abuse. Enable sysmon logging to Identify malicious activity.

https://learn.microsoft.com/en-us/defender-endpoint/attack-surface-reduction-rules-reference?utm_source=chatgpt.com

Event ID 15: FileCreateStreamHash - zone.identifier MOTW stream
https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon  

https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_office_susp_child_processes/

3- Frank Grimes escalates his privileges on Homer Simpson's computer ( HS-CRBN BLB, 172.16.22.4) to gain administrative access and extracts password hashes using Mimikatz.

Leverage EDR protects to block execution of commonly known attacker tools like Mimikatz.  If none are available write detection rules that are tracking lsass.exe access from baseline. Machine learning models like freq_rare in Elastic ML can be setup here to look for anomaly based detections which will benefit other password stealing techniques.

https://www.elastic.co/docs/reference/data-analysis/machine-learning/ml-rare-functions

4- Frank Grimes then uses the shared local administrator password obtained from Homer Simpsons computer to move laterally on the network to Wayland Smithers' computer (WS-ULLMAN, 172.16.10.42).

VIA MSFT defender for endpoint you can block Block process creations originating from PsExec and WMI, if admin use is still needed leverage a allow list enablement via app control tooling. Where this isn't possible its recommended to detect on all outbound connection attempts via commandline tooling. 

https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_powershell_download_iex/

5- Wayland Smithers' computer contains an unprotected SSH private key file for an SSH jump box that grants access to the SCADA systems network within the power plant.

It is possible to inventory and enforce key hygiene with OSquery or secret scanning applications. Leverage SSH best practices like changes SSH default ports or enforcing password protection via a unix based systems by enforcing **PermitEmptyPasswords** in the /etc/ssh/sshd_config by commenting yes.  

6- Using those passwords, Frankenstein Grimes authenticates using PuTTY to the jump box (SCRATCHY, 10.253.65.85) 

If possible add MFA over SSH connections where applications allow. Monitor jump box activity for after hours connections. It's recommended to detect on SSH tunnels like plink.exe tunnel port forwarding to a local port such as 3389 over 443. Anomaly based rules can also spot execution of SSH clients outside of normal operations hours. 

https://cloud.google.com/blog/topics/threat-intelligence/bypassing-network-restrictions-through-rdp-tunneling

https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_plink_port_forwarding/

7- Then uses Nmap to scan for open ports on the SCADA network (1.1.0.0/23) for open port TCP/666 which controls the reactor.

Common EDR tooling will block all internal use of NMAP scanning. Where no endpoint EDR enable potential port scanning rules looking for a 1 to many ports action. Can also enable firewall rules for this activity if possible.  Block all ports on the egress firewall from the OT to IT DMZ and tunnel only necessary traffic over common OT ports. Allow Jump box provisions and OT access only during maintenance windows monitor for access outside of known maintenance windows. 

8- Frank identifies open port TCP/666 and connects to the reactor (SIDESHOW-90, 1.1.1.230) over Telnet without a password required.

Disable telnet usage where and enforce password protections if possible.  Though this is commonly used in OT environments so where not possible detect on all usage of telnet. Enforce SSH if possible. The detection above can spot host activity for usage of telnet and SSH plink.exe tunnel port forwarding if written with loose parameters. One variant of the rule specific to the execution with low risk scoring for correlation based activity and another set with a higher  risk threshold where execution is preformed by non-standard host, user, IP or outside normal operations hours. Detect telnet usage over non standard ports via network logs. 

9- Frank then places malware on the system designed to alter the core temperature of the reactor in the next 30 days.

Use EDR to block download and installation of malicious files/payloads. Leverage admin only execution on OT devices with a limited GPO object for admin users. Detect process injection activity on a host via sysmon event 25 logging.

https://medium.com/falconforce/sysmon-13-process-tampering-detection-820366138a6c

10 - Frank Grimes then steps back through his attack chain leaving ransomware along the way.

Leverage a DLP based tooling to monitor for mass deletion or encryption events. Also create detections rules to spot activity across windows event logs or host based EDR logs where mass file encryption or delete is occurring via rules like wmic shadowcopy delete or by monitoring O365 logs for delete, encrypt over a specific threshold. Also look for process termination followed by deletion events via windows or endpoint logs. Monitor for remote windows services installed to spread the malware and monitor anomalies outside of baseline normal execution. 

https://www.elastic.co/docs/reference/security/prebuilt-rules/rules/windows/lateral_movement_remote_service_installed_winlog

https://www.elastic.co/guide/en/security/8.19/process-termination-followed-by-deletion.html
