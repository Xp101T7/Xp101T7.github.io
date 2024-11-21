---
title: Yara-L Basics
date: 2024-03-27 12:00:00 -500
categories: [Yara,SIEM]
tags: [Google,SIEM,Chronicle,Yara]

---

> [!quote] To dare is to lose ones footing momentarily. To not dare is to lose oneself.
> — Søren Kierkegaard

---

## Yara-L Basics

1. Meta - Describes the rule
2. Events - Defines the Criteria to trigger on
3. Condition - Event Grouping
	Bonus Sections
	1. Match
	2. outcome
	3. options

Check out the site, [Yara-L Basics](https://www.googlecloudcommunity.com/gc/Chronicle-Best-Practices/Getting-to-know-Chronicle-SIEM-YARA-L-basics/ta-p/635468/jump-to/first-unread-message)

The **meta** section describes the rule. While users often keep this section brief, adding values like author, severity, description, and priority can be surfaced when reviewing the rule and used in alerts, which can provide valuable context. Based on our example, we can see that this rule is for Windows events, addresses a MITRE ATT&CK technique of OS credential dumping, and uses data sets like Microsoft Sysmon and Microsoft Windows events. 

The **events** section contains fields and values of interest that we want to observe. Functions, joins, and other comparisons can occur here as well. This is similar to a WHERE clause in a SQL statement. For this rule, we’re looking for the value, “PROCESS_LAUNCH” in our metadata.event_type field and the string, “mimikatz” in the target.process.command_line field. 

The last mandatory section includes event variables that are needed to be met for the rule to trigger. In our example, there’s only one **condition,** so there’s only one variable, which is part of the $process variable we used in events. 

We have two optional sections in this rule. **Match** aggregates values over a time window and will be used extensively with rules that rely on more than one event to trigger. They’re aggregated over a time window that the rule writer defines.

Finally, we have the **outcome** section. This section provides the ability to add additional context and scoring to a triggered rule. These values can be leveraged within Chronicle for dashboarding and alerting, as well as into a SOAR or ticketing system.
