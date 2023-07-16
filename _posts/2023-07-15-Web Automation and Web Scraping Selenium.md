---
title: Web Automation and Web Scraping - Selenium and ChromeDriver
date: 2023-07-15 12:00:00 -500
categories: [Selenium,Python]
tags: [Python,Automation,Scraping,Selenium]


---

> Excellence is to do a common thing in an uncommon way.
> — <cite>Booker T. Washington</cite>

## Web Automation and Web Scraping - Selenium and ChromeDriver

Find your chrome driver go to three dot menu > Help > about google chrome

Find the same version on the chrome driver site

Install chromeDriver
[ChromDriver ](https://chromedriver.chromium.org/downloads)

[Security Consideration ChromeDriver](https://chromedriver.chromium.org/security-considerations)

```txt
ChromeDriver is a powerful tool, and it can cause harms in the wrong hands. While using ChromeDriver, please follow these suggestions to help keeping it safe:

- - By default, ChromeDriver only allows local connections. If you need to connect to it from a remote host, use --allowed-ips switch on the command line to specify a list of IP addresses that are allowed to connect to ChromeDriver.
        
    - If possible, run ChromeDriver with a test account that has no access to sensitive local or network data. ChromeDriver should never be run with a privileged account.
        
    - If possible, run ChromeDriver in a protected environment such as Docker or virtual machine.
        
    - Use firewall to prevent unauthorized remote connection to ChromeDriver.
        
    - If you are using ChromeDriver through third-party tools such as Selenium Server, be sure to protect the network ports of those tools as well.
        
    - Use the latest versions of ChromeDriver and Chrome.
```


