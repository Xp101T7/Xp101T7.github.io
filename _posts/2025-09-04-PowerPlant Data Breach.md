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


![Pasted image 20250904211848.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020250904211848.png)


Here is an executive summary of the Springfield Power Plant's network breach. Contained within is a diagram of the breach depicting network zones, hosts, mitre attack tactics and techniques, attack stages, and attack descriptions which can be found at the end of the document in the Appendix. In the preceding sections of the document are the attack stage descriptions along with Resources to guide in implementing monitoring and controls to enhance security within the plant. It’s recommended to come up with a plan and prioritize work efforts for these controls within 7-30-60-90-180-365-day timelines.  

# Attack Stage 1

*T1566.001 - Spearphishing Attachment*

An attacker sends a spear phishing message with the subject "Free Flaming Moe’s in the Cafeteria after work: Details in Attachment" containing a malicious Microsoft Word attachment to Homer Simpson who opens the attachment and enables Macros when prompted to view the sweet, sweet Flaming Moe’s details.

#### **Monitoring and Controls**

Microsoft 365 defense can sandbox malicious emails and scan them. Its default policy is to block macro-based emails if this is not set, then it’s advised to notify administrators to evaluate this policy to change recommendation.  Purchasing and onboarding and Email security vendors can also aid in detecting and scanning emails for malicious content and blocking attacks in real time. Run user awareness programs within your organization at regular annual cadences and again on user phish test volitions flagged from within your awareness program.  It’s recommended to automate a process of notifying and deploy training via a SOAR or custom automation logic.

#### Resources
-  [https://learn.microsoft.com/en-us/microsoft-365-apps/security/internet-macros-blocked](https://learn.microsoft.com/en-us/microsoft-365-apps/security/internet-macros-blocked)

# Attack stage 2-3

*T1204.002 – User Execution: Malicious File*
*T1059.001 – Command and Scripting Interpreter: PowerShell*
*T1105 – Ingress Tool Transfer*

Once opened, a macro is executed which runs a PowerShell command that establishes a command and control (C2) channel to a domain (https://d35fkdjh4gt99[.]cloudfront[.]net, 52.85.89.218) which ultimately resolves to a machine controlled by the attacker (Frankenstein Grimes) in Amazon's EC2 cloud.

#### **Monitoring and Controls**

Leverage Detections that look for child shell processes spawned from Office documents. For a detection strategy. Also, block the use of files that hold the MOTW attributes that contain macros via group policies across your domain. Leverage SOAR tooling to malware scan macro word documents with MOTW attributes via a third-party artifact scanning tool or flag the file and reverse engineer suspicious macros investigating potential attacker abuse. Enable Sysmon Event ID 15 logging to Identify malicious activity as seen in the resources section below.

#### Resources
- https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon 
- https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_office_susp_child_processes/
- https://learn.microsoft.com/en-us/defender-endpoint/attack-surface-reduction-rules-reference?utm_source=chatgpt.com
- Event ID 15: FileCreateStreamHash - zone.identifier MOTW stream

# Attack stage 4

*T1078 — Valid Accounts*
*T1068 — Exploitation for Privilege Escalation*
*T1003.001 — OS Credential Dumping: LSASS*

Frank Grimes escalates his privileges on Homer Simpson's computer (HS-CRBN BLB, 172.16.22.4) to gain administrative access and extracts password hashes using Mimikatz.

#### **Monitoring and Controls**

Leverage EDR protections to block execution of commonly known attacker tools like Mimikatz.  If no protections are available write detection rules that are tracking lsass.exe access from baseline. Machine learning models like freq_rare in Elastic ML can be set up here to look for anomaly-based detections for lsass or other password related process monitoring which will benefit other password stealing techniques.

#### Resources
-  https://www.elastic.co/docs/reference/data-analysis/machine-learning/ml-rare-functions

# Attack Stage 5

*T1021.002 — SMB/Windows Admin Shares*

Frank Grimes then uses the shared local administrator password obtained from Homer Simpsons computer to move laterally on the network to Wayland Smithers' computer (WS-ULLMAN, 172.16.10.42).
#### **Monitoring and Controls**

VIA MSFT defender for endpoint it’s possible to block process creations originating from PsExec and WMI. If admin use is still needed leverage a allow list enablement via app control tooling. Where this isn't possible, it’s recommended to detect on all outbound connection attempts via command line tooling.
#### Resources
-  https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_powershell_download_iex/

# Attack Stage 6

*T1552.004 — Private Keys*

Wayland Smithers' computer contains an unprotected SSH private key file for an SSH jump box that grants access to the SCADA systems network within the power plant.
#### **Monitoring and Controls**

Enforce inventory and key hygiene with OSquery or secret scanning applications. Leverage SSH best practices like changes SSH default ports or enforcing password protection via a unix based system by enforcing **PermitEmptyPasswords** in the /etc/ssh/sshd_config by commenting yes. 

# Attack stage 7

*T1021.004 — SSH (Sub-technique of Remote Services)*
*T1572 — Protocol Tunneling (Command and Control)*

Using those passwords, Frankenstein Grimes authenticates using PuTTY to the jump box (SCRATCHY, 10.253.65.85)
#### **Monitoring and Controls**

If possible, add MFA over SSH connections where applications are allowed. Monitor jump box activity for after-hours connections. It's recommended to detect SSH tunnels like plink.exe tunnel port forwarding to a local port such as 3389 over 443. Anomaly based rules can also spot execution of SSH clients outside of normal operations hours.

#### Resources
- https://cloud.google.com/blog/topics/threat-intelligence/bypassing-network-restrictions-through-rdp-tunneling
- https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_plink_port_forwarding/

# Attack stage 8

*T1046 - Network Service Discovery*
*T0846 — Remote System Discovery*

Then uses Nmap to scan for open ports on the SCADA network (1.1.0.0/23) for open port TCP/666 which controls the reactor.

#### **Monitoring and Controls**

Common EDR tooling will block all internal use of NMAP scanning. Where no endpoint EDR enables potential port scanning rules looking for a 1 too many ports action. Robust machine learning modules can be set up looking for anomaly’s connection requests over multiple sliding time ranges.  It can also enable firewall rules for blocking port scanning activity if possible.  Block all ports on the egress firewall from the OT to IT DMZ and tunnel only necessary traffic over common OT ports. Allow Jump box provisions and OT access only during maintenance windows monitor for access outside of known maintenance windows.

# Attack Stage 9

*T0866 — Exploitation of Remote Services (ICS)*
*T1021 — Remote Services*

Frank identifies open port TCP/666 and connects to the reactor (SIDESHOW-90, 1.1.1.230) over Telnet without a password required.
#### **Monitoring and Controls**

Disable telnet usage where and enforce password protections if possible.  Though this is commonly used in OT environments so where not possible the SOC should detect all usage of telnet. Enforce SSH if possible. The detection above can spot host activity for usage of telnet and SSH plink.exe tunnel port forwarding if written with loose parameters. One variant of the rule is specific to the execution with low-risk scoring for correlation-based activity and another set with a higher risk threshold where execution is performed by non-standard host, user, IP or outside normal operations hours. Detect telnet usage over nonstandard ports via network logs.

# Attack Stage 9

*T0889 — Modify Program (ICS)*

Frank then places malware on the system designed to alter the core temperature of the reactor in the next 30 days.
#### **Monitoring and Controls**

Use EDR to block download and installation of malicious files/payloads. Leverage admin only execution on OT devices with a limited GPO object for admin users. Detect process injection activity on a host via sysmon event 25 logging. Monitor the network activity at the IT OT DMZ for behavioral net new access.

Resources
-  https://medium.com/falconforce/sysmon-13-process-tampering-detection-820366138a6c

# Attack stage 10

*T1486 — Data Encrypted for Impact*
*T1489 — Service Stop*
*T1490 — Inhibit System Recovery*

Frank Grimes then steps back through his attack chain leaving ransomware along the way.  
#### **Monitoring and Controls**

Leverage a DLP based tooling to monitor mass deletion or encryption events. Also create detections rules to spot activity across windows event logs or host based EDR logs where mass file encryption or delete is occurring via rules like wmic shadowcopy delete or by monitoring O365 logs for delete, encrypt over a specific threshold. Also look for process termination followed by deletion events via windows or endpoint logs. Monitor for remote windows services installed to spread the malware and monitor anomalies outside of baseline normal execution.

Resources
- https://www.elastic.co/docs/reference/security/prebuilt-rules/rules/windows/lateral_movement_remote_service_installed_winlog
- https://www.elastic.co/guide/en/security/8.19/process-termination-followed-by-deletion.html