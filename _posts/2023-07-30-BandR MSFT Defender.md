---
title: Blue and Red MSFT Defender Commands
date: 2023-07-30 12:00:00 -500
categories: [Pentesting,Defender]
tags: [EDR-Bypass,Pentesting,Defender,MSFT]

---

> We gain the strength of the temptation we resist.
> — <cite>Ralph Waldo Emerson</cite>

---

## Blue and Red MSFT Defender Commands


When attempting to disable tamper protection so I could run commands and pentesting tools against my host I found several errors associated to the trusty ol Set-MpPreference command.

	Set-MpPreference -DisableTamperProtection $true

As a result of troubleshooting I decided to make a guide to how disable defender. This will be modified over time as I find new interesting techniques. 

## Enumerating windows defender settings on the host. 

Check if Defender is enabled
	Get-MpComputerStatus
	Get-MpComputerStatus | Select AntivirusEnabled

Check if defensive modules are enabled
	Get-MpComputerStatus | Select RealTimeProtectionEnabled, IoavProtectionEnabled,AntispywareEnabled | FL

Check if tamper protection is enabled
	Get-MpComputerStatus | Select IsTamperProtected,RealTimeProtectionEnabled | FL

## Check alternative AV products

	Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct

Decode hex value of product state to see what is enabled [Pentest Everything](https://viperone.gitbook.io/pentest-everything/everything/everything-active-directory/defense-evasion/disable-defender)

	'0x{0:x}' -f <ProductState>
	'0x{0:x}' -f 393472

## Turning off features

**Note:** Disabling `UAC` is advisable before attempting to turn features off. In testing some changes prompted for user confirmation before allowing change.

	cmd.exe /c "C:\Windows\System32\cmd.exe /k %windir%\System32\reg.exe ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA /t REG_DWORD /d 0 /f"

**Note:** If Tamper protection is enabled you will not be able to turn off Defender by CMD or PowerShell. You can however, still create an exclusion.

## Disables realtime monitoring

Set-MpPreference -DisableRealtimeMonitoring $true

## Disables scanning for downloaded files or attachments

	Set-MpPreference -DisableIOAVProtection $true

## Disable behaviour monitoring

	Set-MPPreference -DisableBehaviourMonitoring $true

## Make exclusion for a certain folder

	Add-MpPreference -ExclusionPath "C:\Windows\Temp"

## Disables cloud detection

	Set-MPPreference -DisableBlockAtFirstSeen $true

## Disables scanning of .pst and other email formats

	Set-MPPreference -DisableEmailScanning $true

## Disables script scanning during malware scans

	Set-MPPReference -DisableScriptScanning $true

## Exclude files by extension

	Set-MpPreference -ExclusionExtension "ps1"

## Turn off everything and set exclusion to "C:\Windows\Temp"

	Set-MpPreference -DisableRealtimeMonitoring $true;Set-MpPreference -DisableIOAVProtection

---
## Blue Team AV Commands

Here are a few blue team tips for interacting with defender with powershell:

## Updating the signatures

	Update-MpSignature

## Perform a quick virus scan

	Start-MpScan -ScanType QuickScan

## Full Virus scan 

	Start-MpScan -ScanType FullScan

## Custom Virus scan 
Start-MpScan -ScanType CustomScan -ScanPath PATH\TO\FOLDER-FILES

	Start-MpScan -ScanType CustomScan -ScanPath "C:\Users\user\Downloads"

## Offline Virus Scan MSFT Defender

	Start-MpWDOScan

## Delete Active Threat

	Remove-MpThreat

## List preferences

	Get-MpPreference

## Exclude a location
Set-MpPreference -ExclusionPath PATH\TO\FOLDER

	Set-MpPreference -ExclusionPath C:\Users

## Remove a folder from exclusions

```powershell
Remove-MpPreference -ExclusionPath &#34;PATH\TO\FOLDER-FILES&#34;
```

## Exclude file type
Set-MpPreference -ExclusionExtension EXTENSION

	Set-MpPreference -ExclusionExtension docx

## Remove File Type Exclusion 

```powershell
Remove-MpPreference -ExclusionExtension EXTENSION
```

## Quarantine time before deletion
Set-MpPreference -QuarantinePurgeItemsAfterDelay DAYS

	Set-MpPreference -QuarantinePurgeItemsAfterDelay 30

## Schedule quick scan
Set-MpPreference -ScanScheduleQuickScanTime SCAN-TIME

	Set-MpPreference -ScanScheduleQuickScanTime 06:00:00

You can specify no time value or set the time to two hours after midnight to reset the settings to their defaults.

## Schedule full scan

	Set-MpPreference -ScanParameters 2

The number "2" in the parameter is what specifies Microsoft Defender a full scan.
Set-MpPreference -RemediationScheduleDay SCAN-DAY

Change Scan Day with the numeric value
- **0 –** Everyday
- **1 –** Sunday
- **2 –** Monday
- **3 –** Tuesday
- **4 –** Wednesday
- **5 –** Thursday
- **6 –** Friday
- **7 –** Saturday
- **8 –** Never

	Set-MpPreference -RemediationScheduleDay 1

## Disable antivirus

	Set-MpPreference -DisableRealtimeMonitoring $true

This doesn't seem to work on my eval VM?

Re-enable 

```powershell
Set-MpPreference -DisableRealtimeMonitoring $false
```

## Enable external drive scanning

	Set-MpPreference -DisableRemovableDriveScanning $false

Revert 

```powershell
Set-MpPreference -DisableRemovableDriveScanning $true
```

## Disable archive scanning

	Set-MpPreference -DisableArchiveScanning $true

Revert

```powershell
Set-MpPreference -DisableArchiveScanning $false
```

## Enable network drive scanning

	Set-MpPreference -DisableScanningMappedNetworkDrivesForFullScan $false

Revert

```powershell
Set-MpPreference -DisableScanningMappedNetworkDrivesForFullScan $true
```

## Disabling Windows Defender

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true; New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender" -Name DisableAntiSpyware -Value 1 -PropertyType DWORD -Force;
```

```powershell
powershell -command "Set-MpPreference -DisableRealtimeMonitoring $true" && reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD_LITTLE_ENDIAN /d 1 /f
```

