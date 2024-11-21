---
title: Set env variables as secret strings using pwsh 
date: 2023-05-08 12:00:00 -500
categories: [powershell,env]
tags: [pwsh,env,pwshai,ChatGPT]
---

To set environment variables as secret strings using PowerShell, you can use the `Set-Item` cmdlet with the `-Value` parameter and the `ConvertTo-SecureString` cmdlet. Here's an example:

```
$securePassword = ConvertTo-SecureString "MySecretPassword" -AsPlainText -Force
Set-Item -Path Env:MySecretVariable -Value $securePassword
```

In this example, we first create a secure string using the `ConvertTo-SecureString` cmdlet and store it in the `$securePassword` variable. We then use the `Set-Item` cmdlet to set the value of the environment variable `MySecretVariable` to the secure string stored in `$securePassword`.

Note that when you run this code, you will be prompted to enter a password for the secure string. This password is used to encrypt the secure string and should be kept secret.

