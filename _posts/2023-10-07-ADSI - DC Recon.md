---
title: ADSI - DC Recon
date: 2023-10-07 12:00:00 -500
categories: [Recon,ADSI]
tags: [ADSI,Admin,Pentesting,Recon]

---

> Give me a lever long enough and a fulcrum on which to place it, and I shall move the world.
> — <cite>Archimedes</cite>

---

## ADSI - DC Recon

## ADSISearcher

![Pasted image 20231007143347.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/
Media/Pasted%20image%2020231007143347.png)


```powershell
# Create a DirectoryEntry object for the rootDSE of the domain
$rootDSE = New-Object DirectoryServices.DirectoryEntry("LDAP://192.168.2.10/RootDSE", "domain\randle", "Crackedlog-in1@")

# Retrieve the rootDomainNamingContext property
$rootDomainNamingContext = $rootDSE.rootDomainNamingContext

# Now create a DirectorySearcher object with the correct search base
$adsiSearcherObj = New-Object DirectoryServices.DirectorySearcher($rootDSE)
$adsiSearcherObj.SearchRoot = New-Object DirectoryServices.DirectoryEntry("LDAP://192.168.2.10/$rootDomainNamingContext", "domain\randle", "Crackedlog-in1@")

# Now you can use $adsiSearcherObj to perform searches in the domain
```

List all the Member objects for DirectorySearcher

```powershell
$adsiSearcherObj | Get-Member
```

Search Root Domain

```powershell
$adsiSearcherObj.SearchRoot
```

There are two functions of interest from the searcher objects. (FindOne(), FindAll())


![Pasted image 20231007144126.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/
Media/Pasted%20image%2020231007144126.png)

```powershell
$adsiSearcherObj.FindAll()
```

```powershell
$adsiSearcherObj.FindOne()
```

Narrow our focus to specific Objects

![Pasted image 20231007144355.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/
Media/Pasted%20image%2020231007144355.png)

```powershell
$adsiSearcherObj.Filter = "samAccountName=tywin.lannister"
$adsiSearcherObj.FindOne().properties
```

Can use wildcards

```powershell
$adsiSearcherObj.Filter = "cn=*lannister*"
```

Search on OU

```powershell
$adsiSearcherObj.Filter = "ou=*"
```

In this case, you can use a `ForEach-Object` cmdlet to iterate through the results and extract the `distinguishedname` property. Here's how you could do it:

```powershell
$adsiSearcherObj.FindAll() | ForEach-Object { $_.Properties['distinguishedname'][0] }
```

& is and. cn & objectcategory

```powershell
$adsiSearcherObj.Filter = "(&(cn=*admin*)(objectCategory=group))"
$adsiSearcherObj.FindAll() | ForEach-Object { $_.Properties['distinguishedname'][0] }
```

| is or. cn or objectcategory

```powershell
$adsiSearcherObj.Filter = "(|(cn=*sql*)(objectCategory=computer))"
```

! is not. not name and is ObjectCategory

```powershell
$adsiSearcherObj.Filter = "(&(!name=*lanister*)(objectCategory=user))"
```

