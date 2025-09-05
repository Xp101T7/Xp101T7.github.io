---
title: Detection Engineering Automation CI/CD Pipeline
date: 2025-07-30 12:00:00 -500
categories: [Detection_Engineering,CI/CD]
tags: [Detections,Code,Automation,CI/CD Pipeline]

---

> [!quote] When it is obvious that the goals cannot be reached, don't adjust the goals, adjust the action steps.
> — Confucius

---

## Detection Engineering Automation CI/CD Pipeline

# Building a Dev Environment
- Focus on **prioritizing and automating collection** of threat‑focused artifacts to build detection use cases before full CI/CD deployment 
- Covers:
  - Scoping threat‑based inputs
  - Parsing indicators & payloads
  - Leveraging context enrichment 

## ✅ Technical Requirements
- Chrome (or Chromium‑based) browser, internet access (e.g. to Chronicle Lab)
- Python 3.9+ (with pip access) and code editor (e.g. VSCode)
- Ubuntu 22.04 Desktop VM (≈8 GB RAM, 4 vCPU)
- Wazuh EDR agent installation on supported OS
- pfSense Community Edition VM (≥2 GB RAM, 2 CPU cores, v2.7.x recommended)
- Optionally: CrowdStrike Falcon trial/admin access {index=3}
- Alternatively, use GCP VMs with IAP tunnels if resources constrained (cost warning) 

## 🎯 Chapter Goals
- Help team **scope high-value threat artifacts** (IOCs, payloads) aligned to business context
- Automate extraction and **parsing of indicators/payloads**
- Show how to **apply parsed IOCs** directly to security tooling (e.g. blocklists, agents)
- Enrich parsed outputs with threat Intel and business context to create prioritized use cases 

## 🧠 Core Topics & Labs
- **Scoping Threat-Based Inputs**
  - How to prioritize which artifacts to process for early detection use-case benefit
- **Parsing Indicators & Payloads**
  - Automate IOC and payload extraction via Python code (e.g. STIX2 JSON parser)
  - Labs:
    - Lab 2.1: Custom STIX2 JSON parser
    - Lab 2.2: Auto‑blocking domains via intel feed
    - Lab 2.3: Ingest malicious hashes into Wazuh EDR
    - Lab 2.4: Deploy custom IOCs to CrowdStrike 

- **Context Enrichment**
  - Augment IOCs with threat‑intel and business metadata
  - Lab 2.5: Analyze and develop custom detections in Google Chronicle 

## ✅ Key Outcomes
- Automated parsing of IOCs/payloads as usable building blocks
- Direct ingestion/deployment of indicators into defense tooling (Wazuh, CrowdStrike, Chronicle)
- Ability to create **enriched, high‑fidelity detection use cases** aligned to threat and business context
- Prepares the inputs and mindset required for downstream CI/CD pipeline automation

## 📚 Further Reading
- Risk scoring and prioritization techniques for use cases (e.g. scoring quick wins) 

---


