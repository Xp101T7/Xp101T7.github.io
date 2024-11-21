---
title: Windows Server Lab Part 1 - Standing up the server and connecting
date: 2023-07-17 12:00:00 -500
categories: [AD,Lab]
tags: [Lab,Pentesting,Server,AD]


---

> Love demands infinitely less than friendship.
> — <cite>George Jean Nathan</cite>

## Windows Server Lab Part 1 - Standing Up the Server and Connecting




## Using RSAT to Create a New User with the help of GPT 4

First install RSAT tools on your host. Apps > new features > RSAT > Gotta Catch em all.

	Create a new user in active directory with the username you are under the yourdomain.tech domain

ChatGPT

I'm sorry for the misunderstanding, but as an AI developed by OpenAI, I am not able to perform system-level operations like creating new users in Active Directory. This operation requires privileged system access, which I don't have. I can, however, guide you through the process.

If you have access to a system with the appropriate permissions and tools installed, here are the general steps you'd follow:

1. Open Active Directory Users and Computers: You can do this by clicking on Start, typing `dsa.msc` into the search box, and pressing Enter.
    
2. In the Active Directory Users and Computers window, select the domain (in your case, heckintechin.tech) and then navigate to the organizational unit (OU) where you want to create the new user.
    
3. Right-click on the OU, select `New`, and then select `User`.
    
4. In the New Object - User dialog box, in the `First name`, `User logon name`, and `User logon name (pre-Windows 2000)` boxes, type the appropriate information (in your case, the username is hecki), and then click `Next`.
    
5. In the Password and Confirm password boxes, type the user's password, select the appropriate password options, and then click `Next`.
    
6. Click `Finish`.
    

Please consult your system or network administrator if you are unsure about any step in this process. Also, remember to always adhere to your organization's policies and procedures when creating new user accounts.


	can you write the powershell code for me

ChatGPT

Certainly, you can use PowerShell's `New-ADUser` cmdlet to create a new user. Here's an example of how to do it:



`# Import the Active Directory module Import-Module ActiveDirectory  # Create a new user 

```Powershell
New-ADUser -SamAccountName hecki -UserPrincipalName you@yourdomain.tech -Name "you" -GivenName "you" -Surname "are" -Enabled $True -DisplayName "you are" -Description "New user" -AccountPassword (ConvertTo-SecureString -AsPlainText "Passw0rd" -Force) -PassThru`
```

This script will create a new user with the username 'hecki' in the domain 'heckintechin.tech'. The user's first name is 'Hecki', and the last name is 'User'. The user is enabled, and the display name and description are set to 'Hecki User' and 'New user Hecki', respectively. The user's password is set to 'Passw0rd'.

Please replace "Passw0rd" with a secure password. This is just an example and should not be used as it does not follow password best practices.

Also, this script should be run with an account that has the necessary privileges to create users in Active Directory.

Remember, it's crucial to handle credentials in a secure way. I used plaintext here for simplicity's sake, but in a production environment, you should look into more secure ways of handling this, like using a credential object or reading the password from a secure file or service.


	when I import active directory i get WARNING: Error initializing default drive: 'Unable to find a default server with Active Directory Web Services running.'

ChatGPT

This warning indicates that PowerShell is unable to locate a domain controller with Active Directory Web Services (ADWS) running. ADWS is a Windows service that was introduced in Windows Server 2008 R2 and is required by several features of Active Directory, including the Active Directory module for PowerShell.

Here are a few things you can do to troubleshoot this issue:

1. **Check if Active Directory Web Services is running**: On your domain controller, open the Services console (services.msc), find Active Directory Web Services in the list, and make sure that it's running. If it's not, try to start it.
    
2. **Check network connectivity**: Make sure that your machine is able to communicate with the domain controller.
    
3. **Specify a domain controller**: If you have multiple domain controllers, it's possible that the one PowerShell is trying to connect to doesn't have ADWS running. You can specify a domain controller when you import the Active Directory module:


`Import-Module ActiveDirectory -Server "your-domain-controller"`

Replace "your-domain-controller" with the hostname or IP address of your domain controller.

4. **Update your system**: If you're running a version of Windows Server older than 2008 R2, you'll need to update to a newer version that supports ADWS.

Remember to run these commands in an elevated PowerShell session (run as Administrator).

If none of these solutions work, you may need to reach out to your network administrator or contact Microsoft support.

Have to setup connection to the DC first. 

Do NSLookup for IP if it resolves then thats good.

Now connect to the server by testing the connection. 

```powershell
$creds = Get-Credential
Get-ADUser -Filter * -Server "your-domain-controller" -Credential $credential
```

Then you can connect using the same method as above. 

```Powershell
New-ADUser -SamAccountName hecki -UserPrincipalName you@yourdomain.tech -Name "you" -GivenName "you" -Surname "are" -Enabled $True -DisplayName "you are" -Description "New user" -AccountPassword (ConvertTo-SecureString -AsPlainText "Passw0rd" -Force) -Server "IP or Hostname" -Credential $creds -PassThru`
```


---


