---
title: DevOps Pre-Requisite
date: 2025-01-05 12:00:00 -500
categories: [DevOps,Cloud]
tags: [Code,Engineering,Dev,DevOps]

---

> [!quote] Fortune favours the brave.
> — Virgil

---

## DevOps Pre-Requisite


![[Pasted image 20250105143307.png]]

![[Pasted image 20250105143256.png]]

![[Pasted image 20250105143314.png]]

![[Pasted image 20250105143325.png]]

![[Pasted image 20250105143351.png]]
![[Pasted image 20250105143430.png]]![[Pasted image 20250105143509.png]]

![[Pasted image 20250105143543.png]]

Deploy a multi tier lamb stack app, that uses a web front end is severed on a web server that connects to a DB backend. 

![[Pasted image 20250105143756.png]]

![[Pasted image 20250105144057.png]]

You write your code on Dev and run the code, we next build the code. 

Have build script to  build then deploy 

![[Pasted image 20250105144304.png]]

git pull and git push 

Github, Gitlab, Bitbucket

Its all manual and done once a week.

![[Pasted image 20250105144627.png]]

![[Pasted image 20250105144731.png]]

All packages need to be there

![[Pasted image 20250105144835.png]]

![[Pasted image 20250105144859.png]]![[Pasted image 20250105144921.png]]

![[Pasted image 20250105145024.png]]
Container Orchestration 

![[Pasted image 20250105145044.png]]

![[Pasted image 20250105145210.png]]

State can be tracked from terraform and if a change is made not in terraform then terraform will switch it back over bc its defualt. There is a terraform manifest file. 


![[Pasted image 20250105145432.png]]

Changes need to be run from the terraform then run `teraform apply`

Terraform is more of an IAC deployment and provisioning tool. 

Ansible is used for post config activities and uses playbooks. 

Monitoring the servers with Prometheus its collects metrics and stores them locally, Grafana helps make sense of the data using charts and graphs. 

All of this is CI/CD when completed. 












