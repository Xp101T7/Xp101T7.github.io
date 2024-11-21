---
title: GOAD - Domain Join New Win11 Host
date: 2023-10-05 12:00:00 -500
categories: [DC,ActiveDirectory]
tags: [ActiveDirectory,Admin,DC,Domain]

---

> I am a man of fixed and unbending principles, the first of which is to be flexible at all times.
> — <cite>Everett Dirksen</cite>

---

## GOAD - Domain Join New Win11 Host


Remote to the host DC01 or login to the DC01 leave this terminal session open as admin. You may be able to connect using remoting script.

```powershell
# Establish a remote session
$session = New-PSSession -ComputerName 192.168.2.10
# Run the command remotely
Invoke-Command -Session $session -ScriptBlock {
    Get-DnsServerZone -Name 'sevenkingdoms.local'
}
# When done, remove the session
Remove-PSSession -Session $session
```

---
### Add User to the Domain

If you cant just use ISE admin session and then setup a new user and follow the below commands to setup a user and join to the domain. Don't add the PC to the domain unless you have to. 

Adding a User:
Open PowerShell as an administrator

```powershell
Import-Module ActiveDirectory
New-ADUser -Name "John Doe" -GivenName John -Surname Doe -SamAccountName jdoe -UserPrincipalName jdoe@example.com -Path "CN=Users,DC=sevenkingdoms,DC=local" -AccountPassword (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force) -Enabled $true
```

Adding a Computer: Open PowerShell as an administrator

```powershell
Import-Module ActiveDirectory
New-ADComputer -Name "Computer01" -SamAccountName Computer01 -Description "Description of Computer01" -Path "CN=Computers,DC=sevenkingdoms,DC=local" -Enabled $true
```
In these snippets:
- Name specifies the name of the user or computer.
- GivenName and -Surname specify the first and last name of the user.
- SamAccountName specifies the SAM account name of the user or computer.
- UserPrincipalName specifies the user principal name (UPN) of the user.
- Path specifies the distinguished name (DN) of the container or organizational unit (OU) where the user or computer object will be created.
- AccountPassword sets the user's password.
- Enabled $true enables the user or computer account.

Ensure you replace placeholders like John Doe, jdoe@example.com, P@ssw0rd, Computer01, and DC=example,DC=com with your actual values.

These commands will create a new user and computer object in AD, after which the computer can be domain-joined to the AD domain either by an administrator or by the user, assuming they have the necessary permissions.

Open PowerShell as an administrator
```powershell
Import-Module ActiveDirectory
Get-ADUser -Identity jdoe
```

Replace jdoe with the username you want to check. If the user is found, it means they are in the domain. If the command returns an error stating that the user cannot be found, it means the user is not in the domain.

Alternatively, to check the domain membership of the computer that a user is logged onto, you can use the following command:

Open PowerShell as an administrator

```powershell
[System.Environment]::UserDomainName
```

This command will return the domain name if the computer is domain-joined or the computer name if it's not.

To verify if a workstation object is created and exists in Active Directory, you can utilize PowerShell with the Get-ADComputer cmdlet. Here's how you can do it:

Open PowerShell as an administrator
```powershell
Import-Module ActiveDirectory
Get-ADComputer -Identity Computer01
```

Replace Computer01 with the name of the workstation you want to check.

If the workstation is found in Active Directory, this command will return details about the workstation. If the workstation is not found, PowerShell will return an error stating that the object could not be found on the domain controller.

---

### Connect Win PC to the Domain

Install a fresh version of win eval in your hypervisor of choice. 

Navigate to the system properties by right click windows icon > select system > scroll down to domain join > drill into system properties pop up > drill into comp Name/Domain Change > Change the domain. #note Make sure you create a unique PC name before you do this.  Another method to pull up the system properties menu, My fav:

To access System Properties from the Run menu, follow these steps:

1. Press `Win + R` on your keyboard to open the Run dialog box.
2. Type  ``` sysdm.cpl ``` into the text box, and then press `Enter` or click `OK`.  

![Pasted image 20231005193046.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020231005193046.png)

---

### Troubleshooting

If you cant reach the domain you may need to set your DNS address to resolve to the domain controller. 

Check that you can resolve the domain using:


Run the Command on the DNS Server:
If possible, run the Get-DnsServerZone cmdlet directly on the DNS server where the DNS Server role is installed.

Run this command on the DNS server
```powershell
Get-DnsServerZone -Name 'sevenkingdoms.local'
```

or run 

```powershell
test-Connection -ComputerName "kingslanding" -Count 2
```

If you are resolving the host you can also test from your kali box by running 

```bash
cme smb <your.ip.range.1/24>
```


