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

Insert link to remote site Sysmon Install Guide #LinkLater

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


### Printing XML Data Output to Before Creating Scripting

```powershell
# Define the path to the EVTX file
$evtxPath = "Sysmon\HuntingMimikatz.evtx"

# Use the provided XPath filter
$xPathFilter = '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'

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

        # Define an array to store objects in the current event
        $objects = @{}

        # Iterate through each EventData/Data element to extract objects
        foreach ($dataNode in $eventXml.Event.EventData.Data) {
            $objects[$dataNode.Name] = $dataNode.'#text'
        }

        # Print objects in the current event
        foreach ($key in $objects.Keys) {
            $value = $objects[$key]
            Write-Host "${key}: $value"
        }

        Write-Host "-------------------------"
    }
}
```

OR File out to HTML with CSS 

Also use the solution: Scroll to End


---

## Hunting Metasploit

For more information about how malware and payloads interact with the network check out the [Malware Common Ports Spreadsheet](https://docs.google.com/spreadsheets/d/17pSTDNpa0sf6pHeRhusvWG6rThciE8CsXTSlDUAZDyo). This will be covered in further depth in the Hunting Malware task.

Find the event and 

```Powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'
```

Then custom display the logs, all common fields can be found in the below script.

Also use the solution:
Scroll to End

To Get limited results of only values that are present 

```powershell
C:\Users\hecki\Labs\Sysmon\outfiles\Ratting.xml
```

---

## Detecting File Creation

Can look for file creation events specifically

```xml
<RuleGroup name="" groupRelation="or">  
	<FileCreate onmatch="include">  
		<TargetFileName condition="contains">mimikatz</TargetFileName>  
	</FileCreate>  
</RuleGroup>
```

## Hunting Abnormal LSASS Behavior

If LSASS is accessed by anything other than svchost.exe its SUS.

Add in to the above xml config 
```xml
<TargetImage condition="image">lsass.exe</TargetImage>
```


Exclude where is svchost.e3xe

```xml
<RuleGroup name="" groupRelation="or">
	<ProcessAccess onmatch="exclude">
		<SourceImage condition="image">svchost.exe</SourceImage>
	</ProcessAccess>
	<ProcessAccess onmatch="include">
		<TargetImage condition="image">lsass.exe</TargetImage>
	</ProcessAccess>
</RuleGroup>
```

```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'
```

Also use the solution:
Scroll to End


---

## Hunting Malware - Rats and C2

Example Rats: RATs are `Xeexe` and `Quasar`

Hypothesis based hunts work here, ID the malware you want to hunt or detect and ID ways that we can modify config files.  Look at open back connect ports example here. [MITRE Software](https://attack.mitre.org/software/)


Look at SUS ports using your config files, look at 1034 and 1604 while excluding onedrive. [Malware Back Connect Ports](https://docs.google.com/spreadsheets/d/17pSTDNpa0sf6pHeRhusvWG6rThciE8CsXTSlDUAZDyo/edit#gid=0)

The Ion-Storm config excludes 53 which malware now uses. So be careful to review configs and consider modern day exploits. Also Consider C2 over port 8080...

```xml
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
		<DestinationPort condition="is">1034</DestinationPort>
		<DestinationPort condition="is">1604</DestinationPort>
	</NetworkConnect>
	<NetworkConnect onmatch="exclude">
		<Image condition="image">OneDrive.exe</Image>
	</NetworkConnect>
</RuleGroup>
```


Open config for EventID=3 with event port needing to be changed for our case whatever the malware would be coming in on. 
```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=<Port>'
```

---

## Hunting Persistence

### StartUp Directory Logging

Look at registry mods along with startup scripts. Can find persistence with reg mod and file create events. 

Look for files placed in \\Startup\\ or \\Start Menu\\ 

```xml
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
		<TargetFilename name="T1023" condition="contains">\Start Menu</TargetFilename>
		<TargetFilename name="T1165" condition="contains">\Startup\</TargetFilename>
	</FileCreate>
</RuleGroup>
```

Can filter on Rule Name as well. Rule Name T1023

### Reg Key Modifications

Look in the CurrentVersion\\Windows\\Run

```xml
<RuleGroup name="" groupRelation="or">
	<RegistryEvent onmatch="include">
		<TargetObject name="T1060,RunKey" condition="contains">CurrentVersion\Run</TargetObject>
		<TargetObject name="T1484" condition="contains">Group Policy\Scripts</TargetObject>
		<TargetObject name="T1060" condition="contains">CurrentVersion\Windows\Run</TargetObject>
	</RegistryEvent>
</RuleGroup>
```

Like startup technique can look at RuleName T1060.

Look at File Registry and File Location as well. 

---

## Detection Evasion Techniques

Alternate Data Streams, Injections, Masquerading, Packing/Compression, Recompiling, Obfuscation, Anti-Reversing Techniques. Alt Data streams hide streams apart from $DATA.

Sysmon has event ID for alt streams. Injection techniques: Thread Hijacking, PE Injection, DLL Injection, and more.

DLL Injection and Backdooring DLLs here, taking an already used DLL by an app and overwriting or including your malicious code with the DLL. 

### Hunting ADS

Event ID 15 will hash and log any NTFS streams included in the sysmon config. SOS sysmon conf does a good starter pack conf. Temp and Startup as well as .hta and .bat extensions.

```xml
<RuleGroup name="" groupRelation="or">
	<FileCreateStreamHash onmatch="include">
		<TargetFilename condition="contains">Downloads</TargetFilename>
		<TargetFilename condition="contains">Temp\7z</TargetFilename>
		<TargetFilename condition="ends with">.hta</TargetFilename>
		<TargetFilename condition="ends with">.bat</TargetFilename>
	</FileCreateStreamHash>
</RuleGroup>
```

List Data streams `dir /r`

### Detecting Remote Threads

Remote threads are created using the Windows API `CreateRemoteThread` and can be accessed using `OpenThread` and `ResumeThread`. This is used in multiple evasion techniques including DLL Injection, Thread Hijacking, and Process Hollowing. We will be using the Sysmon event ID 8 from the SwiftOnSecurity configuration file.

```xml
<RuleGroup name="" groupRelation="or">
	<CreateRemoteThread onmatch="exclude">
		<SourceImage condition="is">C:\Windows\system32\svchost.exe</SourceImage>
		<TargetImage condition="is">C:\Program Files (x86)\Google\Chrome\Application\chrome.exe</TargetImage>
	</CreateRemoteThread>
</RuleGroup>
```

Powershell.exe is creating a remote thread and accessing notepad.exe is Reflective PE Injection.

Same as above but xPathFilter is changed. 

Scroll to End


---

## Real World Investigation 


[EVTX-ATTACK-SAMPLES](https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES/tree/master) and [SysmonResources](https://github.com/jymcheong/SysmonResources) Github repositories

<iframe width="560" height="315" src="https://www.youtube.com/embed/2xA5Sm0Xdd0?si=zA2tVn-lnpK_iih3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Bad USB

A malicious file was dropped onto a host by a malicious USB.

```powershell
Get-WinEvent -Path .\\Investigation-1.evtx -FilterXPath ‘*/System/EventID=12 or */System/EventID=13 or */System/EventID=14’ -Oldest | Select-Object -Property *
```

or 

```powershell
Get-WinEvent -Path C:\Users\THM-Analyst\Desktop\Scenarios\Investigations\Investigation-1.evtx -FilterXPath '*/System/EventID=13' | fl
```

```powershell
Get-WinEvent -Path Investigation-1.evtx -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name='TargetImage']="C:\WINDOWS\system32\svchost.exe"' -MaxEvents 2 -Oldest | fl
```

or


### RawAccessRead

```powershell
Get-WinEvent -Path Investigation-1.evtx -FilterXPath '*/System/EventID=9' -MaxEvents 3 -Oldest | fl -property *
```

### First EXE process
```powershell
Get-WinEvent -Path Investigation-1.evtx -FilterXPath '*/System/EventID=1' -MaxEvents 2 -Oldest | fl
```

### Malicious HTML Payload 
```powershell
Get-WinEvent -Path Investigation-2.evtx -FilterXPath '*/System/EventID=1' -MaxEvents 2 -Oldest | fl
```



### Full Path to Payload Mask

Best working code solution so far doesn't truncate values from Key:Value pairs. Changed to write custom HTML output instead of convert-to-html. 

Scroll to End

### Get Network Info 
```powershell
Get-WinEvent -Path Investigation-2.evtx -FilterXPath '*/System/EventID=3' -MaxEvents 3 -Oldest | fl
```

### Persistence and Foothold Investigation
```powershell
Get-WinEvent -Path Investigation-3.1.evtx -FilterXPath '*/System/EventID=3' -Oldest | fl
```


### Registry Settings for Payloads
```powershell
Get-WinEvent -Path Investigation-3.1.evtx -FilterXPath '*/System/EventID=13' -Oldest | fl
```

### IP Info for payloads

```powershell
Get-WinEvent -Path Investigation-3.2.evtx -FilterXPath '*/System/EventID=1' -Oldest | fl
```


```powershell
Get-WinEvent -Path Investigation-3.2.evtx -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name='TargetImage']="C:\WINDOWS\system32\schtasks.exe"' -MaxEvents 2 -Oldest | fl
```

### BOTNET 

Final Code where events are delineated by green marker separators.  

# FINAL_CODE

```powershell
# Define the path to the EVTX file
$evtxPath = "C:\Users\hecki\Labs\Sysmon\Investigation-4.evtx"

# Use the provided XPath filter
$xPathFilter = '\*/System/EventID=3'

# Retrieve the events based on the specified filter
$events = Get-WinEvent -Path $evtxPath -FilterXPath $xPathFilter

# Check if any events were found
if ($events.Count -eq 0) {
    Write-Host "No events found matching the criteria."
}
else {
    # Prompt the user for output choice
    $outputChoice = Read-Host "Do you want to output to a file? (Y/N)"

    # Check user choice and proceed accordingly
    if ($outputChoice -eq "Y" -or $outputChoice -eq "y") {
        # Set the default output directory
        $outputDirectory = "C:\Users\hecki\Labs\Sysmon\outfiles"

        # Prompt user for output file name
        $outputFileName = Read-Host "Enter the output file name (without extension)"

        # Construct the full output file path
        $outputFilePath = Join-Path -Path $outputDirectory -ChildPath "$($outputFileName).html"

        # Create an empty HTML string to store the formatted output
        $htmlOutput = "<html><head><style>body{font-family:Arial, sans-serif;}table{border-collapse:collapse;width:100%;}th, td{border:1px solid #ddd;padding:8px;text-align:left;}th{background-color:#f2f2f2;word-wrap:break-word;}</style></head><body><table>"

        # Add table headers
        $htmlOutput += "<tr><th>Key</th><th>Value</th></tr>"

        # Process each event and format the output
        foreach ($event in $events) {
            # Convert the event to XML to access all its properties and data
            $eventXml = [xml]$event.ToXml()

            # Define an array to store objects in the current event
            $objects = @{}

            # Add a header to delineate the start of a new event
            $htmlOutput += "<tr><th colspan='2' style='background-color:#4CAF50;color:white;'>Event: $($eventXml.Event.System.EventRecordID)</th></tr>"

            # Iterate through each EventData/Data element to extract objects
            foreach ($dataNode in $eventXml.Event.EventData.Data) {
                $objects[$dataNode.Name] = $dataNode.'#text'
            }

            # Append the formatted objects to the HTML output
            foreach ($key in $objects.Keys) {
                $value = $objects[$key]
                $htmlOutput += "<tr><td>$key</td><td>$value</td></tr>"
            }
        }

        # Close the HTML table and document
        $htmlOutput += "</table></body></html>"

        # Save the HTML output to the specified file
        $htmlOutput | Out-File -FilePath $outputFilePath -Encoding utf8
        Write-Host "Events have been successfully output to the file: $outputFilePath"
    }
    elseif ($outputChoice -eq "N" -or $outputChoice -eq "n") {
        # Process each event and print to terminal
        foreach ($event in $events) {
            # Convert the event to XML to access all its properties and data
            $eventXml = [xml]$event.ToXml()

            # Define an array to store objects in the current event
            $objects = @{}

            # Print a header to delineate the start of a new event
            Write-Host "Event: $($eventXml.Event.System.EventRecordID)"

            # Iterate through each EventData/Data element to extract objects
            foreach ($dataNode in $eventXml.Event.EventData.Data) {
                $objects[$dataNode.Name] = $dataNode.'#text'
            }

            # Print objects in the current event to the terminal
            foreach ($key in $objects.Keys) {
                $value = $objects[$key]
                Write-Host "${key}: $value"
            }
            Write-Host "-------------------------"
        }
    }
    else {
        Write-Host "Invalid input. Please enter 'Y' or 'N'."
    }
}
```


### Python Code Version For Extracting Sysmon Events



```python
import xml.etree.ElementTree as ET
import os
from pathlib import Path

# Define the path to the EVTX file
evtx_path = "C:\\Users\\hecki\\Labs\\Sysmon\\Investigation-2.evtx"

# Use the provided XPath filter
xpath_filter = '\*/System/EventID=3'

# Import the required library for EVTX parsing
import Evtx.Evtx as evtx
import Evtx.Views as ev

# Open the EVTX file
with evtx.ElfFile(evtx_path) as elf:
    # Apply the XPath filter
    xpath_events = elf.get_record_xml(ev.XML(xpath=xpath_filter))

    # Check if any events were found
    if not xpath_events:
        print("No events found matching the criteria.")
    else:
        # Prompt the user for output choice
        output_choice = input("Do you want to output to a file? (Y/N) ").upper()

        # Check user choice and proceed accordingly
        if output_choice == "Y":
            # Set the default output directory
            output_directory = "C:\\Users\\hecki\\Labs\\Sysmon\\outfiles"
            os.makedirs(output_directory, exist_ok=True)  # Create the directory if it doesn't exist

            # Prompt user for output file name
            output_filename = input("Enter the output file name (without extension): ")

            # Construct the full output file path
            output_file_path = os.path.join(output_directory, f"{output_filename}.html")

            # Create an empty HTML string to store the formatted output
            html_output = """
                <html>
                <head>
                    <style>
                        body {font-family: Arial, sans-serif;}
                        table {border-collapse: collapse; width: 100%;}
                        th, td {border: 1px solid #ddd; padding: 8px; text-align: left;}
                        th {background-color: #f2f2f2; word-wrap: break-word;}
                    </style>
                </head>
                <body>
                    <table>
                        <tr><th>Key</th><th>Value</th></tr>
            """

            # Process each event and format the output
            for event_xml in xpath_events:
                event = ET.fromstring(event_xml)
                objects = {}

                # Add a header to delineate the start of a new event
                html_output += f"<tr><th colspan='2' style='background-color:#4CAF50;color:white;'>Event: {event.find('System/EventRecordID').text}</th></tr>"

                # Iterate through each EventData/Data element to extract objects
                for data_node in event.findall("EventData/Data"):
                    objects[data_node.attrib["Name"]] = data_node.text

                # Append the formatted objects to the HTML output
                for key, value in objects.items():
                    html_output += f"<tr><td>{key}</td><td>{value}</td></tr>"

            # Close the HTML table and document
            html_output += """
                    </table>
                </body>
                </html>
            """

            # Save the HTML output to the specified file
            with open(output_file_path, "w", encoding="utf-8") as output_file:
                output_file.write(html_output)
            print(f"Events have been successfully output to the file: {output_file_path}")

        elif output_choice == "N":
            # Process each event and print to terminal
            for event_xml in xpath_events:
                event = ET.fromstring(event_xml)
                objects = {}

                # Print a header to delineate the start of a new event
                print(f"Event: {event.find('System/EventRecordID').text}")

                # Iterate through each EventData/Data element to extract objects
                for data_node in event.findall("EventData/Data"):
                    objects[data_node.attrib["Name"]] = data_node.text

                # Print objects in the current event to the terminal
                for key, value in objects.items():
                    print(f"{key}: {value}")
                print("-------------------------")

        else:
            print("Invalid input. Please enter 'Y' or 'N'.")
```


This is to read from local host log file path

```powershell
# Define the event log name and source
$logName = "Microsoft-Windows-Sysmon/Operational"

# Use the provided XPath filter
$xPathFilter = '*[System[(EventID=3)]]'

# Retrieve the events based on the specified filter
$events = Get-WinEvent -LogName $logName -FilterXPath $xPathFilter

# Check if any events were found
if ($events.Count -eq 0) {
    Write-Host "No events found matching the criteria."
}
else {
    # Prompt the user for output choice
    $outputChoice = Read-Host "Do you want to output to a file? (Y/N)"

    # Check user choice and proceed accordingly
    if ($outputChoice -eq "Y" -or $outputChoice -eq "y") {
        # Set the default output directory
        $outputDirectory = "C:\Users\hecki\Labs\Sysmon\outfiles"

        # Prompt user for output file name
        $outputFileName = Read-Host "Enter the output file name (without extension)"

        # Construct the full output file path
        $outputFilePath = Join-Path -Path $outputDirectory -ChildPath "$($outputFileName).html"

        # Create an empty HTML string to store the formatted output
        $htmlOutput = "<html><head><style>body{font-family:Arial, sans-serif;}table{border-collapse:collapse;width:100%;}th, td{border:1px solid #ddd;padding:8px;text-align:left;}th{background-color:#f2f2f2;word-wrap:break-word;}</style></head><body><table>"

        # Add table headers
        $htmlOutput += "<tr><th>Key</th><th>Value</th></tr>"

        # Process each event and format the output
        foreach ($event in $events) {
            # Define an array to store objects in the current event
            $objects = @{}

            # Add a header to delineate the start of a new event
            $htmlOutput += "<tr><th colspan='2' style='background-color:#4CAF50;color:white;'>Event: $($event.RecordId)</th></tr>"

            # Iterate through each property in the event and extract objects
            foreach ($property in $event.Properties) {
                $objects[$property.Name] = $property.Value
            }

            # Append the formatted objects to the HTML output
            foreach ($key in $objects.Keys) {
                $value = $objects[$key]
                $htmlOutput += "<tr><td>$key</td><td>$value</td></tr>"
            }
        }

        # Close the HTML table and document
        $htmlOutput += "</table></body></html>"

        # Save the HTML output to the specified file
        $htmlOutput | Out-File -FilePath $outputFilePath -Encoding utf8
        Write-Host "Events have been successfully output to the file: $outputFilePath"
    }
    elseif ($outputChoice -eq "N" -or $outputChoice -eq "n") {
        # Process each event and print to terminal
        foreach ($event in $events) {
            # Define an array to store objects in the current event
            $objects = @{}

            # Print a header to delineate the start of a new event
            Write-Host "Event: $($event.RecordId)"

            # Iterate through each property in the event and extract objects
            foreach ($property in $event.Properties) {
                $objects[$property.Name] = $property.Value
            }

            # Print objects in the current event to the terminal
            foreach ($key in $objects.Keys) {
                $value = $objects[$key]
                Write-Host "${key}: $value"
            }
            Write-Host "-------------------------"
        }
    }
    else {
        Write-Host "Invalid input. Please enter 'Y' or 'N'."
    }
}
```





---


## XPaths

```powershell
$xPathFilter = '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'
```

```powershell
$xPathFilter = '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=8080'
```


```powershell
$xPathFilter = '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'
```



---

# Resources: 

TryHackMe Room Sysmon: [Sysmon](https://tryhackme.com/room/sysmon)


