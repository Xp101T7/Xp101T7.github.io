---
title: Sysmon Install Guide
date: 2024-03-14 12:00:00 -500
categories: [logging,Investigation]
tags: [Logs,Investigation,logging,Sysmon]

---

> By going beyond your own problems and taking care of others, you gain inner strength, self-confidence, courage, and a greater sense of calm.
> — <cite>Dalai Lama</cite>

---

## Sysmon Install Guide

To install Sysmon, a monitoring tool and utility that provides detailed information about process creations, network connections, and changes to file creation timestamps on Windows systems, follow these steps:

You can find the Sysmon binary from the [Microsoft Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) website. You can also download the Microsoft Sysinternal Suite or use the below command to run a PowerShell module download and install all of the Sysinternals tools. 

```powershell
PowerShell command: Download-SysInternalsTools C:\Sysinternals
```

1. **Download Sysmon:**
    
    - Go to the Microsoft Sysinternals webpage and locate Sysmon.
    - Download the latest version of Sysmon from the official site.
2. **Extract the downloaded package:**
    
    - Once the Sysmon.zip file is downloaded, extract its contents to a folder of your choice.
    
1. **Open Command Prompt as Administrator:**
    
    - Press the Windows key, type "cmd", then right-click on Command Prompt and select "Run as administrator".
4. **Navigate to the folder containing Sysmon:**
    
    - Use the `cd` command to change directories to where you extracted Sysmon. For example, if you extracted Sysmon to `C:\Sysmon`, you would type `cd C:\Sysmon` in the Command Prompt.
5. **Install Sysmon with default settings:**
    
    - Execute the command `sysmon.exe -i` to install Sysmon with default settings. This will monitor and log system activity to the Windows Event Log.
6. **(Optional) Install Sysmon with a configuration file:**
    
    - To customize Sysmon's behavior, you can use a configuration file. You can find various configuration templates online, tailored for different security needs.
    - To install Sysmon with a configuration file, use the command `sysmon.exe -i <configuration file>`. Replace `<configuration file>` with the path to your configuration file.
7. **Verify the installation:**
    
    - To ensure Sysmon is installed and running, you can check the Windows Event Viewer. Sysmon events are typically found under "Applications and Services Logs" > "Microsoft" > "Windows" > "Sysmon".
8. **Updating Sysmon:**
    
    - To update Sysmon to a newer version, first uninstall the current version using the command `sysmon.exe -u`, then install the new version by following the steps above.
9. **Viewing Sysmon Logs:**
    
    - Use the Windows Event Viewer to view Sysmon logs. This can be done by navigating to "Applications and Services Logs" > "Microsoft" > "Windows" > "Sysmon".

Sysmon is a powerful tool for system monitoring and can greatly enhance security monitoring and incident response efforts. Make sure to regularly update Sysmon and its configuration to keep up with the latest threats and to optimize its performance and utility.