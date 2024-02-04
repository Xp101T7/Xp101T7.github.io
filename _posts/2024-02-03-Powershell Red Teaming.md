---
title: Powershell Red Teaming
date: 2024-02-03 12:00:00 -500
categories: [Pentest,Red]
tags: [PWSH,Powershell,Pentesting,RedTeam]


---

> He who conquers others is strong; He who conquers himself is mighty.
> — <cite>Laozi</cite>


## User Password Enabled set

```powershell
# Get all local users
$allUsers = Get-LocalUser

# List all users with their 'Enabled' status, 'PasswordRequired' status, and SID
$allUsers | Select-Object Name, Enabled, PasswordRequired, SID

# Check specifically for the Administrator account
$adminAccount = $allUsers | Where-Object { $_.Name -eq "Administrator" }

# Display the Administrator account with 'PasswordRequired' status
Write-Output "Administrator account details:"
$adminAccount | Select-Object Name, Enabled, PasswordRequired, SID

# Filter users where 'PasswordRequired' is set to False
$usersWithNoPasswordRequired = $allUsers | Where-Object { -not $_.PasswordRequired }

# List users where 'PasswordRequired' is False, excluding the Administrator account for separate display
Write-Output "Users with no password required (excluding Administrator):"
$usersWithNoPasswordRequired | Where-Object { $_.Name -ne "Administrator" } | Select-Object Name, Enabled, SID

# Count the number of users with 'PasswordRequired' set to False, excluding the Administrator account for separate count
$usersWithNoPasswordRequiredCount = ($usersWithNoPasswordRequired | Where-Object { $_.Name -ne "Administrator" }).Count

# Display the count
Write-Output "Number of users with 'Password Required' set to False (excluding Administrator): $usersWithNoPasswordRequiredCount"
```

## Enumerate Local Users

```powershell
# Get all local groups
$localGroups = Get-LocalGroup

# List all local groups
$localGroups | Select-Object Name

# Count the number of local groups
$groupCount = $localGroups.Count

# Display the count
Write-Output "Number of local groups: $groupCount"

```

## List IP Info

```powershell
# Get detailed IP configuration for all network interfaces Get-NetIPConfiguration | ForEach-Object { $interface = $_.InterfaceAlias $_.IPv4Address | Select-Object @{ Name = 'InterfaceAlias' Expression = { $interface } }, IPAddress, PrefixLength, @{ Name = 'SubnetMask' Expression = { $_.PrefixLength } }, @{ Name = 'DefaultGateway' Expression = { $_.IPv4DefaultGateway.NextHop } } }
```

## List Listening Ports

```Powershell
# Get all listening TCP ports
$listeningTCPPorts = Get-NetTCPConnection | Where-Object { $_.State -eq "Listen" }

# Get all listening UDP ports
$listeningUDPPorts = Get-NetUDPEndpoint

# Count the number of listening TCP and UDP ports
$totalListeningPortsCount = $listeningTCPPorts.Count + $listeningUDPPorts.Count

# Display the count
Write-Output "Total number of listening TCP and UDP ports: $totalListeningPortsCount"

```

```powershell
$system_ports = Get-NetTCPConnection -State Listen
$text_port = Get-Content -Path C:\Users\Administrator\Desktop\ports.txt
foreach($port in $text_port){
    if($port -in $system_ports.LocalPort){
        echo $port
    }
}
```


```powershell
# Get TCP connections for local port 445 and select relevant properties
Get-NetTCPConnection | Where-Object { $_.LocalPort -eq 445 } | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State

```

### System Info Enumeration 

```powershell
# Get all applied patches
$appliedPatches = Get-HotFix

# Count the number of applied patches
$patchCount = $appliedPatches.Count

# Display the count
Write-Output "Number of applied patches: $patchCount"
```

```powershell
# Get all applied patches and select the HotFixID (Patch ID)
$appliedPatches = Get-HotFix | Select-Object -Property InstalledOn, HotFixID

# Display the patch IDs
$appliedPatches
```

## Search Files for Common Strings

```powershell
# Define the root directory to start the search from
$rootDirectory = "C:\Path\To\Start\Search"

# Search for "API_KEY" in all files under the specified directory, recursively, and discard the output
Get-ChildItem -Path $rootDirectory -Recurse -File | Select-String -Pattern "API_KEY" | Select-Object Path, Line, LineNumber | Out-Null

```

## List Running Process

```powershell
# Export the list of running processes to a text file
Get-Process | Out-File -FilePath C:\Path\To\processes.txt
```

```powershell
# List detailed information for all running processes
Get-Process | Format-List -Property *
```

## List Scheduled Tasks

```powershell
# Get the scheduled task named 'new-sched-task'
$task = Get-ScheduledTask -TaskName "new-sched-task"

# Display the path of the scheduled task
$task | Select-Object -Property TaskPath, TaskName
```

## Drive Owner Enum

```powershell
# Get the owner of the C:\ drive
$owner = (Get-Acl -Path C:\).Owner

# Display the owner
Write-Output "The owner of C:\ is: $owner"
```




---
Powershell Scripting 

[https://learnxinyminutes.com/docs/powershell/](https://learnxinyminutes.com/docs/powershell/)

## Password Text String Search

```powershell
# Define the path to the 'emails' folder on the Desktop
# Replace 'YourUsername' with the actual username or use $env:USERNAME for the current user
$emailsFolderPath = "C:\Users\$env:USERNAME\Desktop\emails"

# Search for files containing the word 'password'
$filesContainingPassword = Get-ChildItem -Path $emailsFolderPath -Recurse | Select-String -Pattern "password"

# Check if any files were found and display the results
if ($filesContainingPassword) {
    Write-Output "Files containing 'password':"
    foreach ($file in $filesContainingPassword) {
        Write-Output $file.Path
    }
} else {
    Write-Output "No files containing 'password' were found in the 'emails' folder."
}

```

Display the contents of the file if password is found

```powershell
# Define the directory where the files are located
$directoryPath = "C:\Path\To\Your\Directory"

# Define the string to search for
$searchString = "password"

# Search for the string in all files in the specified directory and display their contents
Get-ChildItem -Path $directoryPath -Recurse -File | Select-String -Pattern $searchString | ForEach-Object {
    Write-Output "Found '$searchString' in $($_.Path):"
    Get-Content -Path $_.Path | Out-String | Write-Output
    Write-Output "---------------------------------"
}
```

Use Regex to find a URL in a file

```powershell
# Define the directory where the files are located
$directoryPath = "C:\Path\To\Your\Directory"

# Define the regex pattern to search for HTTPS links
$searchString = 'https:\/\/[\w\.-]+(?:\.[\w\.-]+)+[\w\.,@?^=%&:\/~\+#-]*[\w@?^=%&\/~\+#-]'

# Search for the regex pattern in all files in the specified directory and display their contents
Get-ChildItem -Path $directoryPath -Recurse -File | Select-String -Pattern $searchString -AllMatches | ForEach-Object {
    Write-Output "Found HTTPS link in $($_.Path):"
    Get-Content -Path $_.Path | Select-String -Pattern $searchString -AllMatches | ForEach-Object {
        $_.Matches | ForEach-Object {
            Write-Output $_.Value
        }
    }
    Write-Output "---------------------------------"
}
```

## Port Scanner

```powershell
# Prompt for scanning localhost
$ip = Read-Host "Enter IP range to scan (for localhost, enter 127.0.0.1)"
if ($ip -ne "127.0.0.1") {
    Write-Host "This script is configured to scan localhost only."
    exit
}

# Prompt for port range and cast to int
$startPort = [int](Read-Host "Enter the starting port number")
$endPort = [int](Read-Host "Enter the ending port number")

# Validate port input
if ($startPort -lt 1 -or $startPort -gt 65535 -or $endPort -lt 1 -or $endPort -gt 65535 -or $startPort -gt $endPort) {
    Write-Host "Invalid port range. Please enter valid port numbers between 1 and 65535."
    exit
}

Write-Host "Scanning ports $startPort to $endPort on $ip..."

# TCP Connect Scan using raw TCP sockets
for ($port = $startPort; $port -le $endPort; $port++) {
    $socket = New-Object System.Net.Sockets.Socket(
        [System.Net.Sockets.AddressFamily]::InterNetwork,
        [System.Net.Sockets.SocketType]::Stream,
        [System.Net.Sockets.ProtocolType]::Tcp)
    
    $address = [System.Net.IPAddress]::Parse($ip)
    $endpoint = New-Object System.Net.IPEndPoint($address, $port)
    
    $result = $socket.BeginConnect($endpoint, $null, $null)
    $success = $result.AsyncWaitHandle.WaitOne(100, $false)
    
    if ($success -and $socket.Connected) {
        Write-Host "Port $port is open."
    }
    
    $socket.Close()
}


```