---
title: Create Files from JSON Output
date: 2023-07-01 12:00:00 -500
categories: [JSON,powershell]
tags: [pwsh,vscode,powershell,JSON]


---

> It takes courage to grow up and become who you really are.
> — <cite>E. E. Cummings</cite>

---

# Create Files from JSON Output

chat "Write powershell to take json start and end marks and create new files where this occurs"
Sure! Here's a PowerShell script that takes a JSON file and creates new files based on the start and end marks specified:

```powershell
# Define the JSON start and end marks
$startMark = "START"
$endMark = "END"

# Read the JSON file
$jsonContent = Get-Content -Raw -Path "path\to\input.json"

# Split the JSON content based on the start and end marks
$jsonSections = $jsonContent -split "(?s)(?<=\b$startMark\b).*?(?=\b$endMark\b)"

# Iterate through each JSON section and create new files
for ($i = 0; $i -lt $jsonSections.Count; $i++) {
    $section = $jsonSections[$i]
    $fileName = "output_$i.json"

    # Write the section content to a new file
    $section | Out-File -FilePath "path\to\$fileName" -Encoding UTF8

    Write-Host "Created file: $fileName"
}
```

Make sure to replace `"path\to\input.json"` with the actual path to your JSON file. The script splits the JSON content based on the start and end marks using a regular expression pattern. It

Apologies for the incomplete response. Here's the continuation of the script:

```powershell
# continues from the previous script

# checks if the output directory exists, if not, create it
$outputDirectory = "path\to\output"
if (-not (Test-Path -Path $outputDirectory -PathType Container)) {
    New-Item -Path $outputDirectory -ItemType Directory | Out-Null
}

# Iterate through each JSON section and create new files
for ($i = 0; $i -lt $jsonSections.Count; $i++) {
    $section = $jsonSections[$i]
    $fileName = "output_$i.json"
    $filePath = Join-Path -Path $outputDirectory -ChildPath $fileName

    # Write the section content to a new file
    $section | Out-File -FilePath $filePath -Encoding UTF8

    Write-Host "Created file: $filePath"
}
```

In this updated version, the script checks if the output directory exists. If it doesn't, it creates the directory using the `New-Item` cmdlet. The `Join-Path` cmdlet is used to construct the full file path for each output file by combining the output directory path with

---

Apologies for the confusion. Here's the complete script with the missing part:

```powershell
# Define the JSON start and end marks
$startMark = "START"
$endMark = "END"

# Read the JSON file
$jsonContent = Get-Content -Raw -Path "path\to\input.json"

# Split the JSON content based on the start and end marks
$jsonSections = $jsonContent -split "(?s)(?<=\b$startMark\b).*?(?=\b$endMark\b)"

# Create the output directory if it doesn't exist
$outputDirectory = "path\to\output"
if (-not (Test-Path -Path $outputDirectory -PathType Container)) {
    New-Item -Path $outputDirectory -ItemType Directory | Out-Null
}

# Iterate through each JSON section and create new files
for ($i = 0; $i -lt $jsonSections.Count; $i++) {
    $section = $jsonSections[$i]
    $fileName = "output_$i.json"
    $filePath = Join-Path -Path $outputDirectory -ChildPath $fileName

    # Write the section content to a new file
    $section | Out-File -FilePath $filePath