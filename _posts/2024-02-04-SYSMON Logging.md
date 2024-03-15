---
title: Sysmon Logging
date: 2024-02-04 12:00:00 -500
categories: [sysmon,logs]
tags: [SIEM,Logging,Logs,Sysmon]

---

> One should not hurt others if one loves oneself.
> — <cite>The Buddha</cite>

---

## Sysmon Logging


Here is an example of a high-quality config that works well for identifying anomalies created by SwiftOnSecurity: [Sysmon-Config](https://github.com/SwiftOnSecurity/sysmon-config). Sysmon includes 29 different types of Event IDs, all of which can be used within the config to specify how the events should be handled and analyzed.

[Sysmon Logging](https://github.com/SwiftOnSecurity/sysmon-config)

Insert link to remote site Sysmon Install Guide []()

To fully utilize Sysmon you will also need to download a Sysmon config or create your own config. We suggest downloading the [SwiftOnSecurity sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config). A Sysmon config will allow for further granular control over the logs as well as more detailed event tracing. In this room, we will be using both the SwiftOnSecurity configuration file as well as the [ION-Storm config file](https://github.com/ion-storm/sysmon-config/blob/develop/sysmonconfig-export.xml).


Command Used: `Sysmon.exe -accepteula -i ..\Configuration\swift.xml`

Monitor Events Under `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`

**Note: Replace the old config with the new config every time you update it. See Sysmon Help.

---
## Sysmon Removes Noise

Noise is excluded and filtered with Sysmon, it's easier to focus on meaningful events. 

### Best Practices

Exclude before Include (Exclude introduces less false negatives.)

CLI Give you more control, use Get-WinEvent, wevutil.exe, EventLog Viewer, Windows Event Log Explorer.

Know before you go. (Get familiar before you implement.)

### Filtering Events with Event Viewer

EventID and keywords, can also use XML but this is tedious and doesn't scale.

Open filter menu select Filter Current Log from the actions menu. 

### Filtering Events with PowerShell

Using Get-WinEvent with XPATH again here like in the [Windows-Event-Logs](https://heckintechin.tech/posts/Windows-Event-Logs/)

Once filtered we use wevutil.exe

Filter by Event ID: `*/System/EventID=<ID>`

Filter by XML Attribute/Name: `*/EventData/Data[@Name="<XML Attribute/Name>"]`

Filter by Event Data: `*/EventData/Data=<Data>`

Look for Network connections coming from port 4444

```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'
```

Count the events for and eventID in sysmon evtx files

```powershell
Get-WinEvent -Path "C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx" -FilterXPath '*/System/EventID=3' | Measure-Object | Select-Object -ExpandProperty Count
```

Get the UTC event time of the first event seen

```powershell
(Get-WinEvent -LogName Microsoft-Windows-Sysmon/Operational | Where-Object { $_.Id -eq 3 } | Select-Object -First 1).TimeCreated

```

This command breaks down as follows:

- `Get-WinEvent -LogName Microsoft-Windows-Sysmon/Operational`: Fetches events from the Sysmon operational log.
- `Where-Object { $_.Id -eq 3 }`: Filters the events to only include those with an Event ID of 3, which corresponds to network events in Sysmon.
- `Select-Object -First 1`: Selects the first event that matches the criteria.
- `.TimeCreated`: Extracts the creation time of the selected event.

The output will be the UTC creation time of the first Sysmon network event with Event ID 3.

OR 

```powershell
try {
    $firstNetworkEvent = Get-WinEvent -Path "C:\Users\Heckin\Desktop\Scenarios\Practice\Filtering.evtx" -FilterXPath "*[System[EventID=3]]" -MaxEvents 1 | Select-Object TimeCreated
    $formattedTime = $firstNetworkEvent.TimeCreated.ToString("M/d/yyyy h:mm:ss tt")
    Write-Output $formattedTime
} catch {
    Write-Error "An error occurred: $_"
}
```

Or 

### To get the last 10 events in descending order:

powershellCopy code

```powershell
try {
    $lastNetworkEvents = Get-WinEvent -Path "C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx" -FilterXPath "*[System[EventID=3]]" | Sort-Object TimeCreated -Descending | Select-Object -First 10
    foreach ($event in $lastNetworkEvents) {
        $utcTime = $event.TimeCreated.ToUniversalTime()
        $formattedTime = $utcTime.ToString("M/d/yyyy HH:mm:ss.fff 'UTC'")
        Write-Output "Time: $formattedTime, Event ID: $($event.Id)"
    }
} catch {
    Write-Error "An error occurred: $_"
}
```


### Converted to get the first 10 events seen (in ascending order):

powershellCopy code

```powershell
try {
    $firstNetworkEvents = Get-WinEvent -Path "C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx" -FilterXPath "*[System[EventID=3]]" | Sort-Object TimeCreated | Select-Object -First 10
    foreach ($event in $firstNetworkEvents) {
        $utcTime = $event.TimeCreated.ToUniversalTime()
        $formattedTime = $utcTime.ToString("mm/dd/yyyy HH:mm:ss.fff 'UTC'")
        Write-Output "Time: $formattedTime, Event ID: $($event.Id)"
    }
} catch {
    Write-Error "An error occurred: $_"
}
```

In this updated script, the key changes are:

- We use `Sort-Object TimeCreated` without `-Descending` to sort the events in ascending order (oldest first).
- We still use `Select-Object -First 10` to get the first 10 events after sorting, which now corresponds to the first 10 events chronologically.
- The loop iterates through each of these events, converts their time to UTC, formats the time, and outputs it along with the event ID.


---

## Hunting Metasploit

For more information about how malware and payloads interact with the network check out the [Malware Common Ports Spreadsheet](https://docs.google.com/spreadsheets/d/17pSTDNpa0sf6pHeRhusvWG6rThciE8CsXTSlDUAZDyo). This will be covered in further depth in the Hunting Malware task.

Find the event and 

```Powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'
```

Then custom display the logs, all common fields can be found in the below script.

```powershell
# Define the path to the EVTX file
$evtxPath = "Sysmon\HuntingMetasploit.evtx"

# Use the provided XPath filter
$xPathFilter = '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'

# Retrieve the events based on the specified filter
$events = Get-WinEvent -Path $evtxPath -FilterXPath $xPathFilter

# Check if any events were found
if ($events.Count -eq 0) {
    Write-Host "No events found matching the criteria."
} else {
    # Process each event
    foreach ($event in $events) {
        Write-Host "Event Found - ID: $($event.Id), Time: $($event.TimeCreated)"
        
        # Convert the event to XML to access all its properties and data
        $eventXml = [xml]$event.ToXml()

        # Extracting general event metadata
        $logName = $eventXml.Event.System.Channel.'#text'
        $source = $eventXml.Event.System.Provider.Name.'#text'
        $date = $eventXml.Event.System.TimeCreated.SystemTime.'#text'
        $eventId = $eventXml.Event.System.EventID.'#text'
        $taskCategory = $eventXml.Event.System.Task.'#text'
        $level = $eventXml.Event.System.Level.'#text'
        $keywords = $eventXml.Event.System.Keywords.'#text'
        $user = $eventXml.Event.System.Security.UserID.'#text'
        $computer = $eventXml.Event.System.Computer.'#text'
        $description = $eventXml.Event.RenderingInfo.Message.'#text'

        # Extracting specific event details
        $utcTime = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "UtcTime" } | Select-Object -ExpandProperty '#text'
        $processGuid = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "ProcessGuid" } | Select-Object -ExpandProperty '#text'
        $processId = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "ProcessId" } | Select-Object -ExpandProperty '#text'
        $image = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "Image" } | Select-Object -ExpandProperty '#text'
        $protocol = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "Protocol" } | Select-Object -ExpandProperty '#text'
        $initiated = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "Initiated" } | Select-Object -ExpandProperty '#text'
        $sourceIsIpv6 = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourceIsIpv6" } | Select-Object -ExpandProperty '#text'
        $sourceIp = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourceIp" } | Select-Object -ExpandProperty '#text'
        $sourceHostname = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourceHostname" } | Select-Object -ExpandProperty '#text'
        $sourcePort = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourcePort" } | Select-Object -ExpandProperty '#text'
        $sourcePortName = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourcePortName" } | Select-Object -ExpandProperty '#text'
        $destinationIsIpv6 = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationIsIpv6" } | Select-Object -ExpandProperty '#text'
        $destinationIp = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationIp" } | Select-Object -ExpandProperty '#text'
        $destinationHostname = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationHostname" } | Select-Object -ExpandProperty '#text'
        $destinationPort = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationPort" } | Select-Object -ExpandProperty '#text'
        $destinationPortName = $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationPortName" } | Select-Object -ExpandProperty '#text'

        # Output extracted details
        Write-Host "Log Name: $logName"
        Write-Host "Source: $source"
        Write-Host "Date:$date"
        Write-Host "Event ID: $eventId"
        Write-Host "Task Category: $taskCategory"
        Write-Host "Level: $level"
        Write-Host "Keywords: $keywords"
        Write-Host "User: $user"
        Write-Host "Computer: $computer"
        Write-Host "Description: $description"
        Write-Host "UtcTime: $utcTime"
        Write-Host "ProcessGuid: $processGuid"
        Write-Host "ProcessId: $processId"
        Write-Host "Image: $image"
        Write-Host "Protocol: $protocol"
        Write-Host "Initiated: $initiated"
        Write-Host "SourceIsIpv6: $sourceIsIpv6"
        Write-Host "SourceIp: $sourceIp"
        Write-Host "SourceHostname: $sourceHostname"
        Write-Host "SourcePort: $sourcePort"
        Write-Host "SourcePortName: $sourcePortName"
        Write-Host "DestinationIsIpv6: $destinationIsIpv6"
        Write-Host "DestinationIp: $destinationIp"
        Write-Host "DestinationHostname: $destinationHostname"
        Write-Host "DestinationPort: $destinationPort"
        Write-Host "DestinationPortName: $destinationPortName"
        Write-Host "-------------------------"
    }
}
```

To Get limited results of only values that are present 

```powershell
# Define the path to the EVTX file
$evtxPath = "Sysmon\HuntingMetasploit.evtx"

# Use the provided XPath filter
$xPathFilter = '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'

# Retrieve the events based on the specified filter
$events = Get-WinEvent -Path $evtxPath -FilterXPath $xPathFilter

# Check if any events were found
if ($events.Count -eq 0) {
    Write-Host "No events found matching the criteria."
} else {
    # Process each event
    foreach ($event in $events) {
        Write-Host "Event Found - ID: $($event.Id), Time: $($event.TimeCreated)"
        
        # Convert the event to XML to access all its properties and data
        $eventXml = [xml]$event.ToXml()

        # Define all properties you're interested in
        $properties = @{
            "LogName" = $eventXml.Event.System.Channel.'#text';
            "Source" = $eventXml.Event.System.Provider.Name.'#text';
            "Date" = $eventXml.Event.System.TimeCreated.SystemTime.'#text';
            "EventID" = $eventXml.Event.System.EventID.'#text';
            "TaskCategory" = $eventXml.Event.System.Task.'#text';
            "Level" = $eventXml.Event.System.Level.'#text';
            "Keywords" = $eventXml.Event.System.Keywords.'#text';
            "User" = $eventXml.Event.System.Security.UserID.'#text';
            "Computer" = $eventXml.Event.System.Computer.'#text';
            "Description" = $eventXml.Event.RenderingInfo.Message.'#text';
            "UtcTime" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "UtcTime" }).'#text';
            "ProcessGuid" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "ProcessGuid" }).'#text';
            "ProcessId" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "ProcessId" }).'#text';
            "Image" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "Image" }).'#text';
            "Protocol" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "Protocol" }).'#text';
            "Initiated" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "Initiated" }).'#text';
            "SourceIsIpv6" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourceIsIpv6" }).'#text';
            "SourceIp" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourceIp" }).'#text';
            "SourceHostname" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourceHostname" }).'#text';
            "SourcePort" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourcePort" }).'#text';
            "SourcePortName" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "SourcePortName" }).'#text';
            "DestinationIsIpv6" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationIsIpv6" }).'#text';
            "DestinationIp" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationIp" }).'#text';
            "DestinationHostname" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationHostname" }).'#text';
            "DestinationPort" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationPort" }).'#text';
            "DestinationPortName" = ($eventXml.Event.EventData.Data | Where-Object { $_.Name -eq "DestinationPortName" }).'#text';
        }

        # Iterate through each property and check for null or empty values before printing
        foreach ($key in $properties.Keys) {
            $value = $properties[$key]
            if (![string]::IsNullOrEmpty($value)) {
                Write-Host "${key}: $value"  # Encapsulate variable in ${} for complex references
            }
        }

        Write-Host "-------------------------"
    }
}
```

