---
title: Connect My GitHub Pages Site to my domain. 
date: 2023-07-04 12:00:00 -500
categories: [Github,Pages]
tags: [Website,WebDomain,Pages,Pages-Site-Connect]


---

> Winners never quit and quitters never win.
> — <cite>Vince Lombardi</cite>

---

# Connect Github pages site to my domain name


Bought a domain years back. 
Just pointed my A records to GitHubs A apex domains

[Managing a custom domain for your GitHub Pages site - GitHub Docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
- Configuring an apex domain
  
Then Go go settings > pages > add your custom domain. 

Then run a check before and after committing your website pointer record file. 

```shell
dig yourwebsite.com +noall +answer -t A
```

Commit the file locally using pull request from your repo back down to the main site.

Give about 30 minutes before going back into the github pages site and force HTTPS. 

Test your site by navigating to your domain and then test again after 
forcing HTTPS. 






