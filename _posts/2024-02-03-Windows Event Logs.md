---
title: Windows Event Logs
date: 2024-02-03 12:00:00 -500
categories: [logs,logs]
tags: [logs,logs,logs,logs]

---

> Accept challenges, so that you may feel the exhilaration of victory.
> — <cite>George S. Patton</cite>

---

## Windows Event Logs

## Wevtutil

```powershell
wevtutil qe /?
```

Lognames on the machine

```powershell
(wevtutil el).Count
```

Path to a log-file using query-events

```powershell
wevtutil qe /lf:true "Path\To\Log\File.evtx"
```

XPATH Query 

```powershell
wevtutil qe LogName /q:"XPathQuery" /rd:true /f:Format /c:NumberOfEvents

#example 
wevtutil qe System /q:"Event[System[Level=2 or Level=3]]" /rd:true /f:Text /c:10
```


## Get-WinEvent

[https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.4&viewFallbackFrom=powershell-7.1](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.4&viewFallbackFrom=powershell-7.1)


Filtering by filterhashtable

```powershell
# Fetch PowerShell script block execution events (ID 4104) from the Operational log and search for 'SecureString' in their messages.
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-PowerShell/Operational'; ID=4104} | Select-Object -Property Message | Select-String -Pattern 'SecureString'
```


```powershell
Get-WinEvent -Listlog *
Get-WinEvent -ListProvider *Powershell*
  
(Get-WinEvent -ListProvider Microsoft-Windows-GroupPolicy).Events | Format-Table Id, Description
(Get-WinEvent -ListProvider Microsoft-Windows-Powershell).Events | Format-Table Id, Description | measure-object
```


```powershell
# Fetch the first N events of PowerShell script block execution (ID 4104) from the Operational log and search for 'SecureString' in their messages.
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-PowerShell/Operational'; ID=4104} -MaxEvents N | Select-Object -Property Message | Select-String -Pattern 'SecureString'
```


Filter Informational Events 

```powershell
Get-WinEvent -FilterHashtable @{LogName='YourLogNameHere'; Level=4}
```

## XPATHS

[https://learn.microsoft.com/en-us/previous-versions/dotnet/netframework-4.0/ms256115(v=vs.100)](https://learn.microsoft.com/en-us/previous-versions/dotnet/netframework-4.0/ms256115(v=vs.100))


```powershell
Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=101 and */System/Provider[@Name="WLMS"]'
```

```powershell
Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"' -MaxEvents 1
```


WLMS Events at specific time 

```powershell
Get-WinEvent -LogName "Application" -FilterXPath '*/System/Provider[@Name="WLMS"] and */System/TimeCreated[@SystemTime="2020-12-15T01:09:08.940277500Z"]' -MaxEvents 1
```

Find a username and event id

```powershell
Get-WinEvent -LogName "Security" -FilterXPath '*/System[EventID=4720] and */EventData/Data[@Name="TargetUserName"]="Sam"' -MaxEvents 1
```


Monitor for downgrade attacks

```powershell
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" -FilterXPath '*[System[EventID=400]]'
```


Query against a pulled evtx file 

```powershell
Get-WinEvent -Path .\merged.evtx -FilterXPath '*/System[EventID=400]'
```


Display output from of xml logs

```powershell
# Retrieve events with a specific Event ID (e.g., 104) from the .evtx file
$events = Get-WinEvent -Path .\merged.evtx -FilterXPath '*[System[EventID=104]]'

# Loop through each event and display its XML data
foreach ($event in $events) {
    $eventXml = $event.ToXml()
    Write-Output "Event XML Data:"
    Write-Output $eventXml
    Write-Output "--------------------------------"
}
```

Extract elements 

```powershell
# Retrieve events with Event ID 104 from the .evtx file
$events = Get-WinEvent -Path .\merged.evtx -FilterXPath '*[System[EventID=104]]'

# Extract the computer name from the first event
if ($events.Count -gt 0) {
    $eventXml = [xml]$events[0].ToXml()
    $computerName = $eventXml.Event.System.Computer

    # Display the computer name
    $computerName
} else {
    Write-Host "No events found with Event ID 104."
}
```

Dig through logs to find ScriptBlock Text

```powershell
Get-WinEvent -Path .\merged.evtx -FilterXPath '*[System[EventID=4104]]' | Where-Object { $_.Properties | ForEach-Object { $_.Value -match "ScriptBlockText" } } | Format-List Message
```


Find time of events based on ScriptBlock ID.

```powershell
#AM PM Timecreated 

$scriptBlockId = "fdd51159-9602-40cb-839d-c31039ebbc3a"
$events = Get-WinEvent -Path .\merged.evtx -FilterXPath '*[System[EventID=4104]]' | Where-Object { $_.Message -match $scriptBlockId }

foreach ($event in $events) {
    $timeCreated = $event.TimeCreated.ToString("MM/dd/yyyy hh:mm:ss tt")
    Write-Output "ScriptBlock ID: $scriptBlockId was executed at: $timeCreated"
}

#24hr time

$scriptBlockId = "fdd51159-9602-40cb-839d-c31039ebbc3a"
$events = Get-WinEvent -Path .\merged.evtx -FilterXPath '*[System[EventID=4104]]' | Where-Object { $_.Message -match $scriptBlockId }

foreach ($event in $events) {
    $timeCreated = $event.TimeCreated
    Write-Output "ScriptBlock ID: $scriptBlockId was executed at: $timeCreated"
}

```

Find the execution id 

```powershell
Get-WinEvent -Path .\merged.evtx -FilterXPath '*[System[EventID=4104]]' |
    Where-Object { $_.Properties | ForEach-Object { $_.Value -match "ScriptBlockText" } } |
    ForEach-Object {
        $eventXml = [xml]$_.ToXml()
        $message = $_.Message
        $processId = $eventXml.Event.System.Execution.ProcessID
        Write-Output "Message: $message"
        Write-Output "Execution Process ID: $processId"
        Write-Output "--------------------------------"
    }
```


Find strings in EVTX files 

```powershell
Get-WinEvent -Path .\merged.evtx | 
    Where-Object { $_.Message -match 'C:\\Windows\\System32\\net1\.exe' } |
    Format-List TimeCreated, Id, Message
```

* [MITRE ATT&CK®](https://attack.mitre.org/)
* [Appendix L - Events to Monitor - Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor)
* [Download Windows 10 and Windows Server 2016 security auditing and monitoring reference from Official Microsoft Download Center](https://www.microsoft.com/en-us/download/confirmation.aspx?id=52630)
* [about Logging Windows - PowerShell - Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_logging_windows?view=powershell-7.4&viewFallbackFrom=powershell-7.1)
* [Greater Visibility Through PowerShell Logging - Mandiant](https://www.mandiant.com/resources/blog/greater-visibility)
* [Configure PowerShell logging to see PowerShell anomalies in Splunk UBA - Splunk Documentation](https://docs.splunk.com/Documentation/UBA/5.0.4/GetDataIn/AddPowerShell)
* [sbousseaden/EVTX-ATTACK-SAMPLES: Windows Events Attack Samples](https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES)
* [PowerShell ♥ the Blue Team - PowerShell Team](https://devblogs.microsoft.com/powershell/powershell-the-blue-team/)
* [Tampering with Windows Event Tracing: Background, Offense, and Defense - by Palantir - Palantir Blog](https://blog.palantir.com/tampering-with-windows-event-tracing-background-offense-and-defense-4be7ac62ac63)
* [TryHackMe - Splunk: Basics](https://tryhackme.com/room/splunk101)
* [TryHackMe - Sysmon](https://tryhackme.com/room/sysmon)
* [TryHackMe - Windows Internals](https://tryhackme.com/room/windowsinternals)
* [XML Path Language (XPath)](https://www.w3.org/TR/1999/REC-xpath-19991116/)
* [Consuming Events (Windows Event Log) - Win32 apps - Microsoft Learn](https://learn.microsoft.com/en-us/windows/win32/wes/consuming-events#xpath-10-limitations)
* [XPath Examples - Microsoft Learn](https://learn.microsoft.com/en-us/previous-versions/dotnet/netframework-4.0/ms256086(v=vs.100))
* [Windows+Logging+Cheat+Sheet_ver_Oct_2016.pdf](https://static1.squarespace.com/static/552092d5e4b0661088167e5c/t/580595db9f745688bc7477f6/1476761074992/Windows+Logging+Cheat+Sheet_ver_Oct_2016.pdf)
* [Wayback Machine](https://web.archive.org/web/20190115215749/https://apps.nsa.gov/iaarchive/customcf/openAttachment.cfm?FilePath=/iad/library/ia-guidance/security-configuration/applications/assets/public/upload/Spotting-the-Adversary-with-Windows-Event-Log-Monitoring.pdf&WpKes=aF6woL7fQp3dJiqyJL2LenrLxuHC7ztGtVNK3x)