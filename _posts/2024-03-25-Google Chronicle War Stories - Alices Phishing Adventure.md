---
title: "Google Chronicle War Stories: Alices Phishing Adventure"
date: 2024-03-25 12:00:00 -500
categories:
  - SIEM
  - Detections
tags:
  - Detections
  - SIEM
  - Goolge
  - Chronicle
---

> [!quote] Wisdom and penetration are the fruit of experience, not the lessons of retirement and leisure. Great necessities call out great virtues.
> — Abigail Adams

---

## Google Chronicle War Stories: Alices Phishing Adventure


When running searches there are options for UDM and Raw Log Searching.

![Pasted image 20240325114851.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325114851.png)
See all Assets which accessed this domain, VT Context, Relevant IOC's from threat intel, Resolved IP's, Sub-domains, and Sibling Domains and associated entities of internal assets. 

![Pasted image 20240325115641.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325115641.png)

IOC Domain matches are correlated by leveraging googles threat intel. It searching all the data for threat intel matching. IOC Ingest time displays First/Last seen 

Alerts tab shows Rules Engine for creating custom rules.  

![Pasted image 20240325120022.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325120022.png)

![Pasted image 20240325120229.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325120229.png)

Rules are written in YARA-L (Yara for Logs). Widely used detection languages.

We're looking for a combination of events where an EXCEL process made a network connection to download an executable or a large file, then grouping by the hostname over a 5 minute window.

```sql

rule suspicious_download_office {
  // Meta section provides metadata about the rule
  meta:
    author = "Google Cloud Security"  // Author of the rule
    description = "Office Application downloading an executable(.exe in URL) or a suspiciously large file(>100KB)"  // Description of what the rule detects
    type = "alert"  // Type of rule (alert)
    data_source = "tanium, zscalar, crowdstrike"  // Data sources used by the rule
    reference = "https://attack.mitre.org/software/S0160/"  // Reference to MITRE ATT&CK framework
    severity = "Critical"  // Severity level of the alert
    priority = "Critical"  // Priority level of the alert

  // Events section defines the conditions that trigger the rule
  events:
    $edr.metadata.event_type = "PROCESS_LAUNCH"  // Looks for a process launch event in EDR data
    $edr.target.process.file.full_path = /excel/ nocase  // Checks if the process file path contains "excel" (case-insensitive)
    $edr.principal.hostname = $hostname  // Captures the hostname from the EDR event

    $proxy.metadata.event_type = "NETWORK_HTTP"  // Looks for a network HTTP event in proxy data
    ($proxy.target.url = /\\.exe$/ nocase or $proxy.network.received_bytes > 102400)  // Checks if the URL ends with ".exe" or if the received bytes exceed 102400 (100KB)
    $proxy.principal.hostname = $hostname  // Captures the hostname from the proxy event
    $proxy.metadata.event_timestamp.seconds > $edr.metadata.event_timestamp.seconds  // Ensures the proxy event timestamp is greater than the EDR event timestamp

  // Match section specifies the criteria for correlating the events
  match:
    $hostname over 5m  // Matches events based on the hostname within a 5-minute window

  // Outcome section defines the actions or results when the rule conditions are met
  outcome:
    $risk_score = max(95)  // Sets the risk score to a maximum value of 95

    // Populates various fields with additional context for the alert graph
    $principal_process_pid = array_distinct($edr.principal.process.pid)  // Captures the principal process PID
    $principal_process_command_line = array_distinct($edr.principal.process.command_line)  // Captures the principal process command line
    $principal_process_file_sha256 = array_distinct($edr.principal.process.file.sha256)  // Captures the principal process file SHA256
    $principal_process_file_full_path = array_distinct($edr.principal.process.file.full_path)  // Captures the principal process file full path
    $principal_process_product_specfic_process_id = array_distinct($edr.principal.process.product_specific_process_id)  // Captures the principal process product-specific process ID
    $principal_process_parent_process_product_specfic_process_id = array_distinct($edr.principal.process.parent_process.product_specific_process_id)  // Captures the principal process parent process product-specific process ID
    $target_process_pid = array_distinct($edr.target.process.pid)  // Captures the target process PID
    $target_process_command_line = array_distinct($edr.target.process.command_line)  // Captures the target process command line
    $target_process_file_sha256 = array_distinct($edr.target.process.file.sha256)  // Captures the target process file SHA256
    $target_process_file_full_path = array_distinct($edr.target.process.file.full_path)  // Captures the target process file full path
    $target_process_product_specfic_process_id = array_distinct($edr.target.process.product_specific_process_id)  // Captures the target process product-specific process ID
    $principal_user_userid = array_distinct($edr.principal.user.userid)  // Captures the principal user ID
    $target_hostname = array_distinct($proxy.target.hostname)  // Captures the target hostname
    $target_url = array_distinct($proxy.target.url)  // Captures the target URL

  // Condition section specifies the logical expression that must be satisfied for the rule to trigger
  condition:
    $edr and $proxy  // Both the EDR and proxy conditions must be met
}
```


View Rule Detections will show you why a detection triggered off. There is a timeline which corresponds to detection trigger history. 


![Pasted image 20240325121031.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325121031.png)

![Pasted image 20240325121024.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325121024.png)

Click on the Alert select raw logs:

![Pasted image 20240325122112.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325122112.png)


Tanium Stream Raw Logs
```yaml
{
  "metadata": {
    "logType": "PROCESS_LAUNCH",
    "productVersion": "1.2",
    "vendorName": "Tanium",
    "productName": "Tanium Event Emitter",
    "eventTimestamp": "2024-03-25T09:17:22.888+00:00"
  },
  "principal": {
    "process": {
      "pid": "37382",
      "parentPid": "14418",
      "productSpecificProcessId": "37382",
      "productSpecificParentProcessId": "14418",
      "file": {
        "fullPath": "C:\\Program Files\\Microsoft Office\\Office16\\EXCEL.exe",
        "md5": "34d5ea586a61b0aba512c0cb1d3d8b15"
      }
    },
    "hostname": "alice-benjamin-pc",
    "assetId": "alice-benjamin-pc",
    "user": {
      "userid": "alice",
      "groupid": null
    }
  }
}
```

Zscaler Raw Logs
```sql
2024-03-25 09:17:30	reason=Allowed	event_id=4916164375500306918	protocol=HTTP	action=Allowed	transactionsize=469208	responsesize=160767	requestsize=1092	urlcategory=Internet Services	serverip=10.114.227.104	clienttranstime=8230	requestmethod=GET	refererURL=Unknown	useragent=Unknown	product=NSS	location=Corp	status=200	url=http://manygoodnews.com/dow/Client%20Update.exe	vendor=Zscaler	hostname=manygoodnews.com	clientpublicip=173.207.25.160	threatcategory=None	threatname=Malicious URL	filetype=None	appname=Common Applications	pagerisk=68	department=Default Department	urlsupercategory=Internet	appclass=Business	dlpengine=None	urlclass=Business Use	threatclass=None	dlpdictionaries=None	fileclass=None	bwthrottle=NO	servertranstime=2571	event_timestamp=2024-03-25 09:17:30	clientIP=10.0.30.228	user=alice
```

Drill Into the Alice-Benjamin-PC

![Pasted image 20240325123048.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325123048.png)

Asset view > Events Tab displays all detections or events for this alert. Alerts are more context into this alerts fired for this asset.  Can also see overview and swap to legacy view.

![Pasted image 20240325123416.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325123416.png)


Low Prevalence domains are more interesting. Dig in by clicking the bubbles.
![Pasted image 20240325123652.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325123652.png)


![Pasted image 20240325123855.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325123855.png)

Security Data Sources are fused together for robust enrichment. 

Get to Raw Log View by clicking on the Timeline event and clicking the move to window on the right. 

![Pasted image 20240325124208.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325124208.png)

IP/MAC is fused with Hostname becuase DHCP logging is the dynamic source of truth. 


## The Phishing Investigation

A user **logged in** to the machine, launched **Outlook**, which then launched **Excel** to open a **spreadsheet**, likely received in an email...

Within seconds of opening the sheet, a **network connection** downloaded an **executable** file which ran shortly after it was created...

It all makes sense now! It looks like this process opened and read from Chrome's **Login Data** file in order to **steal credentials**!

![Pasted image 20240325124626.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325124626.png)

Enrichment is done and more info can be seen on enriched data in the insight panels, 

For example, our **VirusTotal** integration shows how the executable has been **detected** by multiple AVs.

Drill into the user login event and click username to pivot to Alice's activity.

Since SSO is enabled we can visualize timelines for suspicious activity.

Filter down using add filter or drill into the events in timeline. 

![Pasted image 20240325130825.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240325130825.png)

So there you have it we detected an attack using simple rules which checked for Excel downloading executables.


---

## Resources

[https://goo.gle/m/chronicle](https://goo.gle/m/chronicle)
