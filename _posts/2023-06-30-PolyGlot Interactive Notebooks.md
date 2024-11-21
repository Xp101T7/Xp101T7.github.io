---
title: Polyglot Interactive Notebooks, Powershell AI and ChatGPT
date: 2023-06-30 12:00:00 -500
categories: [Jupyter,Polyglot]
tags: [.net,VSCode,Jupter,Polyglot]


---

> Each man has his own vocation; his talent is his call. There is one direction in which all space is open to him.
> — <cite>Ralph Waldo Emerson</cite>

---

Install the dotnet-sdk file and check its hash 

[Download .NET 7.0 SDK (v7.0.305) - Windows x64 Installer](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-7.0.305-windows-x64-installer)
- .NET 7.0

```Powershell
$hash1 = Get-FileHash .\dotnet-sdk-7.0.305-win-x64.exe sha512 | select-object Hash

$hash2 = "d0f106f330e3f529499d5288ad8310d6cbd436787820236694866e147e3d8d8e0aeb6447e297f8fd2e961ec183c4602d39785c5322330d7503209193d188bf1c"

$hash2 = $hash2.ToUpper()

# or convert to another variable and perserve the first lowercase 
#$hash3 = $hash2.ToUpper()

if ($hash1.hash -eq $hash2) {
>     Write-Output "The variables are equal."
> } else {
>     Write-Output "The variables are not equal."
> }
The variables are equal
```

Get frustrated trying to figure out why dotnet isn't recognized and realize the x86 version in front of actual version. 

Use the **Move Up** and **Move Down** buttons to move the **C:\\Program Files\\dotnet\** entry above **C:\\Program Files (x86)\dotnet\\**.


[Announcing Polyglot Notebooks! Multi-language notebooks in Visual Studio Code - .NET Blog](https://devblogs.microsoft.com/dotnet/announcing-polyglot-notebooks-harness-the-power-of-multilanguage-notebooks-in-visual-studio-code/#getting-started)

  

## Creating Notebooks

To create a new polyglot notebook, open the Command Palette(`Ctrl+Shift+P`) on Windows or (`Cmd+Shift+P`) on MacOS, and select **Polyglot Notebook: Create new blank notebook**. You can also create a new notebook with `Ctrl+Shift+Alt+N` key combination on Windows.

## Running Code

Execute code by selecting the cell kernel in the bottom right of each cell, or use language magic commands to mix languages in one cell regardless of the chosen cell kernel.

![Running Code](https://github.com/dotnet/interactive/raw/main/images/RunningCodeExample.gif)

## Variable Sharing and Variables View

Share variables between languages using the `#!set` magic command. If you can't remember the syntax, you can always generate it using the `Share` action in the Polyglot Notebooks Variables View.

![Variable Sharing with the Variables View](https://github.com/dotnet/interactive/raw/main/images/variable-sharing-with-variables-view.gif)

## Examples

- Connect to a SQL database, share query results with JavaScript, and create your own custom visualizations.

![SQL and JavaScript Example](https://github.com/dotnet/interactive/raw/main/images/SQLJavaScript.gif)

- Create powerful diagrams and visualizations using code and text using [Mermaid](https://mermaid-js.github.io/mermaid/#/ "https://mermaid-js.github.io/mermaid/#/").

![Mermaid Example](https://github.com/dotnet/interactive/raw/main/images/MermaidExample.gif)


[Install .NET on Windows - .NET | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/install/windows?WT.mc_id=dotnet-35129-website&tabs=net70#install-with-windows-package-manager-winget)
- It was not possible to find any installed .NET Core SDKs
  
---



