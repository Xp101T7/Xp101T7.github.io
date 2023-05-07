---
title: Favicon Changes for Jekyll Site
date: 2023-05-06 12:00:00 -500
categories: [Favicon,GitHub.io]
tags: [Website,Github.io,Favicon,jekyll]
---

![[](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230506213738.png)

Bc the _site > assets > img > favicons pull the gem bundle assets > img > favicons files the files on the Jekyll site do not perisist. You must change the favicons in the gem bundle gem files location for the files to persist the load.  

Grab the favicon file location from the terminal and drop that into a file explorer window.

	bundle info --path jekyll-theme-chirp

Then replace all the files as needed. 

Use the following sites to convert the favicons

https://manytools.org/http-html-text/apple-touch-icon-generator/

https://favicon.io/favicon-converter/

Or convert the Favicons using the power toys image resizer tool.  

Right click > image resizer

![[](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted image 20230506223841.png]]






