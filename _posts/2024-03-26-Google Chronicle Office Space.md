---
title: Google Chronicle Office Space
date: 2024-03-26 12:00:00 -500
categories: [Chronicle,SIEM]
tags: [Investigation,Google,Chronicle,SIEM]

---

> [!quote] Fine words and an insinuating appearance are seldom associated with true virtue
> — Confucius

---

## Google Chronicle Office Space


Entering the regex match for everything wildcard `.*` then selecting the Raw Log Dropdown, we can modify the start time and see the Scan Data amount for that range. 

![Pasted image 20240326114029.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326114029.png)

We can limit the log sources by selecting the sources from the dropdown. 

- **User Context** from Azure AD, providing things like Employee Names, Titles, Managers, etc.
- **Network telemetry** from Corelight, Infoblox, and Extrahop.
- **Endpoint telemetry** from a combination of Tanium, Crowdstrike, and Windows Event Logs.

GCP data sources allows a couple click GCP integration into chronicle. Found in GCP Console > Security > Chronicle.

[https://cloud.google.com/chronicle/docs/ingestion/cloud/ingest-gcp-logs?hl=en](https://cloud.google.com/chronicle/docs/ingestion/cloud/ingest-gcp-logs?hl=en)

## Rules Dashboard

Search for **unexpected_connection_to_pci_environment** with severity of critical. 

![Pasted image 20240326114732.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326114732.png)

Drill into the rule itself by clicking on it. 

Notice the rules description: 

`A successful low prevalence ssh connection from a non PCI environment to a PCI environment`

![Pasted image 20240326115130.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326115130.png)

This would require network telemetry Corelight or Zeek logging is the solution here. 

![Pasted image 20240326115230.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326115230.png)

Corelight Raw Log

```json
{
  "_node": "worker-35",
  "_path": "ssh",
  "_system_name": "sensor-21",
  "_write_ts": "2024-03-25T13:05:43.604+00:00",
  "auth_attempts": 1,
  "auth_success": true,
  "cipher_alg": "aes128-  ctr",
  "client": "SSH-2.0-dropbear_2016.74",
  "compression_alg": "none",
  "cshka": "ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-rsa,ssh-dss",
  "hassh": "c1c596caaeb93c566b8ecf3cae9b5a9e",
  "hasshAlgorithms": "curve25519-sha256@libssh.org,ecdh-  sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1;aes128-ctr,aes256-ctr,aes128-cbc,aes256-cbc,twofish256-cbc,twofish-cbc,twofish128-cbc,3des-ctr,3des-cbc;hmac-sha1-96,hmac-sha1,hmac-sha2-256,hmac-sha2-512,hmac-md5;none",
  "hasshServer": "ba6d3d2aecbd0d91b01dfa7828110d70",
  "hasshServerAlgorithms": "curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-  group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1;aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se;hmac-md5-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-  etm@openssh.com,hmac-ripemd160-etm@openssh.com,hmac-sha1-96-etm@openssh.com,hmac-md5-96-etm@openssh.com,hmac-md5,hmac-sha1,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96;none,zlib@openssh.com",
  "hasshVersion": "1.1",
  "host_key": "97:d8:13:39:c9:25:a2:b1:e5:e6:96:f5:d0:88:49:14",
  "host_key_alg": "ecdsa-sha2-nistp256",
  "id.orig_h": "10.0.32.228",
  "id.orig_p": 54811,
  "id.resp_h": "10.16.42.14",
  "id.resp_p": 22,
  "kex_alg": "curve25519-sha256@libssh.org",
  "mac_alg": "hmac-sha1-96",
  "server": "SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.10",
  "sshka": "ssh-rsa,ssh-dss,ecdsa-sha2-nistp256,ssh-ed25519",
  "ts": "2024-03-25T13:05:43.604+00:00",
  "uid": "CeRP5EbYZjTgFqsq0",
  "version": 2
}
```

There is nothing related to Hostname or  **PCI environment** in these logs but Chronicle is enriching this data. 

See UDM Event and normalized fields for a better understanding.  Google considers **context enrichment** (or **aliasing**) to there super power. This IMO every SIEM should have. 


Corelight has given us IP address (**principal.ip** and **target.ip)**, which is enriched with other sources to extract that data. Enriched fields are marked with an E. 

![Pasted image 20240326115848.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326115848.png)

This enrichment allows for the creation of non-PCI to PCI activity Alerting.

## Yara-L 

Drill into the Rule Options > Edit Rules

![Pasted image 20240326122420.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326122420.png)

![Pasted image 20240326122450.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326122450.png)

Rules are written with UDM so there is no mention of underlying data source fields. 

Normalized data is the ultimate goal here. 

```json
rule unexpected_connection_to_pci_environment {
    meta:
        author = "Google Cloud Security"
        description = "A successful low prevalence ssh connection from a non PCI environment to a PCI environment"
        assumption = "A list called sensitive_labels has been created to match events labels of specific types"
        type = "alert"
        data_source = "corelight zeek"
        severity = "Critical"
        priority = "Critical"

    events:
        $ssh.metadata.event_type = "NETWORK_CONNECTION"
        $ssh.metadata.product_event_type = "ssh"
        $ssh.security_result.action = "ALLOW"
        // Does NOT match event label value to value in list - ie non-sensitive asset
        not $ssh.principal.labels.value in %sensitive_labels
        $ssh.principal.hostname = $source_host
        // Connecting to a "sensitive" asset
        $ssh.target.hostname = $dest_host
        $ssh.target.hostname != /test/
        // Match event label value to value in list - ie sensitive asset
        $ssh.target.labels.value in %sensitive_labels

    match:
        $source_host, $dest_host over 24h

    outcome:
        // Calculate risk score based on the value of the sensitive label
        $risk_score = sum(
            if ($ssh.target.labels.value = "PCI", 40) +
            if ($ssh.target.labels.value = "ITAR", 50) +
            if ($ssh.target.labels.value = "SuperSecretSauce", 30) +
            if ($ssh.target.labels.value = "ArguablyTheMostSecretOfSauces", 100)
        )

        // Populate alert graph with additional context
        $principal_hostname = array_distinct($ssh.principal.hostname)
        $target_hostname = array_distinct($ssh.target.hostname)
        $principal_process_pid = array_distinct($ssh.principal.process.pid)
        $principal_process_command_line = array_distinct($ssh.principal.process.command_line)
        $principal_process_file_md5 = array_distinct($ssh.principal.process.file.md5)
        $principal_process_file_sha256 = array_distinct($ssh.principal.process.file.sha256)
        $principal_process_file_full_path = array_distinct($ssh.principal.process.file.full_path)
        $principal_process_product_specfic_process_id = array_distinct($ssh.principal.process.product_specific_process_id)
        $principal_process_parent_process_product_specfic_process_id = array_distinct($ssh.principal.process.parent_process.product_specific_process_id)
        $target_process_pid = array_distinct($ssh.target.process.pid)
        $target_process_command_line = array_distinct($ssh.target.process.command_line)
        $target_process_file_md5 = array_distinct($ssh.target.process.file.md5)
        $target_process_file_sha256 = array_distinct($ssh.target.process.file.sha256)
        $target_process_file_full_path = array_distinct($ssh.target.process.file.full_path)
        $target_process_product_specfic_process_id = array_distinct($ssh.target.process.product_specific_process_id)
        $target_process_parent_process_product_specfic_process_id = array_distinct($ssh.target.process.parent_process.product_specific_process_id)
        $principal_user_userid = array_distinct($ssh.principal.user.userid)
        $target_user_userid = array_distinct($ssh.target.user.userid)

    condition:
        // Trigger a detection if there are one or more SSH connections matching the criteria
        $ssh and $risk_score > 39
}
```

## Reference Lists

These lookup lists/reference lists can be used for positive or negative matching within an alert. 

![Pasted image 20240326123535.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326123535.png)

They can be modified by drilling into the rule. 

Allow/Block lists of things like IP's, Domains, etc. 

But you can put in any plaintext you want and even update them programmatically!
#DoThis

![Pasted image 20240326123934.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326123934.png)

Pivot back to investigations by clicking `View Rule Detections`

Look into the Production VM asset an pivot by Selecting Columns in the UDM-enabled view. 

- Click on **Columns**
- Click on **Explore Fields within UDM**
- Check the box for **target -> asset -> hostname**

![Pasted image 20240326124450.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326124450.png)

Data Flexibility and Integration: Google Chronicle allows users to integrate any data source that provides the required information, as long as it aligns with our established data model. This flexibility enables organizations to leverage their existing data sources and seamlessly incorporate them into our system.

Unleashing the Power of Rules: Once the data is successfully integrated into our model, the true potential of our platform shines through. Users can harness the real power of the system by creating sophisticated rules that intelligently utilize the available information. These rules enable automated analysis, alerting, and decision-making based on the specific criteria and conditions defined by the user.

By combining the flexibility of data integration with the advanced rule-writing capabilities, Google Chronicle empowers organizations to extract maximum value from their data, streamline processes, and make informed decisions based on comprehensive insights.

## Office Space Attack

Again no data for 3 month timeframe at this point. Searched first seen time frame and timestamps in the screenshot below with no events available. 

Find events of interest on the target host. 

![Pasted image 20240326125702.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326125702.png)


### Insider Threat
- Before the alert, everything seems like business as usual.
- An SSH connection was allowed from a non-sensitive environment and a suspicious looking Python file was copied onto the machine.
- After the alert, the production transactions remain, but there is a second transaction which is rounding up value into a secret account!

The enrichment of sensitive to non-sensitive actions in an environment shouldn't be happening. 

 Dig into the **suspicious looking Python file** by looking for the hash. Click on the **File Move** event, then copy the **File Hash** within the embedded view as shown.
 
 ![Pasted image 20240326130106.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326130106.png)

Michael Bolton PC is seen when I search the event window of Oct 23 to Dec 23. 

![Pasted image 20240326130510.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326130510.png)

File Mod > File Move > File Create

Pivot to the File Move PC origin. 

Here are some areas to focus on:

- Is this another Cloud Asset? NO 
	- How do/don't we know?
	  1- Principle Asset IP and mac are the same as principle asset ip and mac.  1- There is no principle.asset.attribute.cloud.* data in the UDM Fields

- What data sources are used on this view?

![Pasted image 20240326143250.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326143250.png)


- How did Peter transfer the file to the VM? SSH 
	- What hardware/software was involved?

```json
{
  "_node": "worker-96",
  "_path": "ssh",
  "_system_name": "sensor-96",
  "_write_ts": "2023-12-14T13:05:42.813+00:00",
  "auth_attempts": 1,
  "auth_success": true,
  "cipher_alg": "aes128-  ctr",
  "client": "SSH-2.0-dropbear_2016.74",
  "compression_alg": "none",
  "cshka": "ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-rsa,ssh-dss",
  "hassh": "c1c596caaeb93c566b8ecf3cae9b5a9e",
  "hasshAlgorithms": "curve25519-sha256@libssh.org,ecdh-  sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1;aes128-ctr,aes256-ctr,aes128-cbc,aes256-cbc,twofish256-cbc,twofish-cbc,twofish128-cbc,3des-ctr,3des-cbc;hmac-sha1-96,hmac-sha1,hmac-sha2-256,hmac-sha2-512,hmac-md5;none",
  "hasshServer": "ba6d3d2aecbd0d91b01dfa7828110d70",
  "hasshServerAlgorithms": "curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-  group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1;aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se;hmac-md5-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-  etm@openssh.com,hmac-ripemd160-etm@openssh.com,hmac-sha1-96-etm@openssh.com,hmac-md5-96-etm@openssh.com,hmac-md5,hmac-sha1,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96;none,zlib@openssh.com",
  "hasshVersion": "1.1",
  "host_key": "97:d8:13:39:c9:25:a2:b1:e5:e6:96:f5:d0:88:49:14",
  "host_key_alg": "ecdsa-sha2-nistp256",
  "id.orig_h": "10.0.32.228",
  "id.orig_p": 52236,
  "id.resp_h": "10.16.42.14",
  "id.resp_p": 22,
  "kex_alg": "curve25519-sha256@libssh.org",
  "mac_alg": "hmac-sha1-96",
  "server": "SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.10",
  "sshka": "ssh-rsa,ssh-dss,ecdsa-sha2-nistp256,ssh-ed25519",
  "ts": "2023-12-14T13:05:42.813+00:00",
  "uid": "CMGC7HpVNSWFCyAM9",
  "version": 2
}
```

Copy UDM's

```json
metadata.base_labels.allow_scoped_access = true
metadata.base_labels.log_types = "CORELIGHT"
metadata.enrichment_labels.allow_scoped_access = true
metadata.enrichment_labels.log_types = "CS_EDR"
metadata.enrichment_labels.log_types = "GCP_COMPUTE_CONTEXT"
metadata.enrichment_labels.log_types = "UDM"
metadata.enrichment_labels.namespaces = ""
metadata.event_timestamp.seconds = 1702559142
metadata.event_timestamp.nanos = 813000000
metadata.event_type = "NETWORK_CONNECTION"
metadata.id = "AAAAAMIaclWQXwR9Au9OXN1q57UAAAAABQAAAAAAAAA="
metadata.ingested_timestamp.seconds = 1702566907
metadata.ingested_timestamp.nanos = 202673000
metadata.log_type = "CORELIGHT"
metadata.product_event_type = "ssh"
metadata.product_log_id = "CMGC7HpVNSWFCyAM9"
metadata.product_name = "Zeek"
metadata.vendor_name = "Corelight"
observer.hostname = "sensor-96"
principal.asset_id = "CS:b05e841708e6417d8851b6ff73d92525"
principal.asset.asset_id = "CS:b05e841708e6417d8851b6ff73d92525"
principal.asset.hostname = "peter-gibbons-pc"
principal.asset.ip = "10.0.32.228"
principal.asset.mac = "b5:0c:9c:30:63:4d"
principal.hostname = "peter-gibbons-pc"
principal.ip = "10.0.32.228"
principal.labels.key = "Environment"
principal.labels.value = "Corporate"
principal.mac = "b5:0c:9c:30:63:4d"
principal.platform_version = "SSH-2.0-dropbear_2016.74"
principal.port = 52236
principal.process.file.md5 = "c1c596caaeb93c566b8ecf3cae9b5a9e"
security_result.action = "ALLOW"
security_result.description = "1 successful SSH authentication attempts were observed"
security_result.summary = "SSH authentication success"
target.asset.attribute.cloud.availability_zone = "us-central1-a"
target.asset.attribute.cloud.environment = "GOOGLE_CLOUD_PLATFORM"
target.asset.attribute.cloud.project.name = "demo-vm"
target.asset.attribute.cloud.project.resource_type = "CLOUD_PROJECT"
target.asset.attribute.cloud.vpc.name = "opc-vpc"
target.asset.attribute.cloud.vpc.resource_type = "VPC_NETWORK"
target.asset.attribute.creation_time.seconds = 1702213218
target.asset.attribute.creation_time.nanos = 986000000
target.asset.attribute.labels.key = "ancestors"
target.asset.attribute.labels.value = "projects/1092746024689"
target.asset.attribute.labels.key = "ancestors"
target.asset.attribute.labels.value = "folders/84481780135"
target.asset.attribute.labels.key = "ancestors"
target.asset.attribute.labels.value = "organizations/499750689351"
target.asset.category = "compute.googleapis.com/Instance"
target.asset.deployment_status = "ACTIVE"
target.asset.hardware.cpu_platform = "Intel Haswell"
target.asset.hardware.model = "e2-medium"
target.asset.hostname = "prod-payments-sync-vm"
target.asset.ip = "10.16.42.14"
target.asset.last_boot_time.seconds = 1702213218
target.asset.last_boot_time.nanos = 986000000
target.asset.location.name = "us-central1"
target.asset.mac = "b5:0c:9c:aa:bb:c4"
target.asset.nat_ip = "10.16.42.14"
target.asset.product_object_id = "8912356166603966979"
target.cloud.environment = "GOOGLE_CLOUD_PLATFORM"
target.hostname = "prod-payments-sync-vm"
target.ip = "10.16.42.14"
target.labels.key = "Environment"
target.labels.value = "PCI"
target.mac = "b5:0c:9c:aa:bb:c4"
target.platform_version = "SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.10"
target.port = 22
target.process.file.md5 = "ba6d3d2aecbd0d91b01dfa7828110d70"
```


Drag the time range to narrow window. 

![Pasted image 20240326145638.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326145638.png)

We can pivot to user view simply with 

```sql
user = "peter"
```

Chronicle automatically surfaces **User context data** from Azure AD Context which is giving us information such as Peter's Title, Manager, multiple email addresses (hover over the field), and much more. This data can be enriched from other sources as well. 

Additional investigation doesn't match what google had shown. There are simply missing logs where there lab automation is looping and the chronicle team hasn't updated the loop script for the lab activity or the course content hasn't been updated to reflect the new automation script to mimic the activity. 

- What data sources are shown here?
	- We're looking at a combination of at least Microsoft AD, Azure AD Context, Tanium, Crowdstrike, and Corelight data.
- Does each data source contain a username? If not, how else would Chronicle know it applies to this user?
	- Each data source DOES NOT contain a username - but Chronicle is still able to map these events to the user via aliasing.
- Which event is most likely being used to alias events to this timeline?
	- It's the NETWORK_CONNECTION event to download.windowsupdate[.]com which is used for mapping since Tanium provides the hostname and username.
- Who is Peter emailing? And why is this important?
	- Peter is responding to Milton - who is on a quest to find his missing red stapler.



---
## MetaData.Enrichment_Labels.log_types

Add in the MetaData and save it as a view. 

#DoThis 

![Pasted image 20240326145326.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326145326.png)


![Pasted image 20240326145406.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020240326145406.png)

