---
title: Favicon Changes for Jekyll Site
date: 2023-05-07 12:00:00 -500
categories: [Favicon,GitHub.io]
tags: [Website,Github.io,Favicon,jekyll]
---

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230506213738.png)


Local Instance fix but doesn't help when bulding remote. 
~~Bc the site > assets > img > favicons pull the gem bundle assets > img > favicons files the files on the Jekyll site do not perisist. You must change the favicons in the gem bundle gem files location for the files to persist the load.  

Grab the favicon file location from the terminal and drop that into a file explorer window.

	bundle info --path jekyll-theme-chirp

This only works on local site build
~~Then replace all the files as needed. 


Use the following sites to convert the favicons

https://manytools.org/http-html-text/apple-touch-icon-generator/

https://favicon.io/favicon-converter/

https://realfavicongenerator.net/ 

Or convert the Favicons using the power toys image resizer tool.  

Right click > image resizer

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted image 20230506223841.png)

See https://chirpy.cotes.page/posts/customize-the-favicon/ for what I believe to be the local instance favicon fix. Still working on the build fix for github pages. 

https://github.com/cotes2020/jekyll-theme-chirpy/issues/636

# GitHub Pages Build & Deploy Fix

Create folder in root directory for assets/img/favicons/ and drop the favicons in it push code and build. 

Finally found the solution here:

https://github.com/cotes2020/jekyll-theme-chirpy/issues/325

Closed on May 7th 2023

https://github.com/orgs/community/discussions/23990

https://github.com/orgs/community/discussions/22625

https://github.com/cotes2020/jekyll-theme-chirpy/issues/325





