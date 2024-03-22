---
title: Aurora EDR
date: 2024-03-22 12:00:00 -500
categories: [Aurora,EDR]
tags: [ThreatHunt,Sigma,Aurora,EDR]

---

> [!quote] Ethics change with technology.
> — Larry Niven

---

## Aurora EDR

[Aurora EDR](https://tryhackme.com/room/auroraedr)


Event Tracing for Windows
Event Tracing for Windows (ETW) is a Windows OS logging feature that provides a mechanism to trace and log events raised by user-mode applications and kernel-mode drivers. ETW provides the capability for applications and drivers to write events. For cybersecurity defenders, this becomes a vital source of detection information.

ETW is made up of three distinct parts:

- Controllers: These applications are used to configure event tracing sessions. They also initiate the providers. An example of a Controller is logman.exe.
- Providers: These are the applications that produce event logs.
- Consumers: These applications subscribe and listen to events in real-time or from a file.

he event information is categorised under these types of levels:

- **Information:** Describes the successful operation of a driver, application or service. Basically, a service is calling home.
- **Warning:** Describes an event that may not be a present issue but can cause problems in the future.
- **Error:** Describes a significant problem with a service or application.
- **Success Audit:** Outlines that an audited security access operation was successful. For example, a user’s successful login to the system.
- **Failure Audit:** Outlines that an audited security access operation failed. For example, a failed access to a network drive by a user.

Windows logs are placed under different categories, with three major ones used for system troubleshooting and investigations:

- **Application:** Records log events associated with system components such as drivers and interface components that run an app.
- **System:** Records events related to programs installed and running on the system.
- **Security:** Records events associated with security, such as logon attempts and resource access.

---

# Aurora

Using Sigma rules this EDR will audit or prevent events it triggers on which will display in Win Event Log Viewer or use code at [Sysmon: See Final Code](https://heckintechin.tech/posts/Sysmon-Install-Guide/) and update XPath and read from local host instead of calling the file. 

