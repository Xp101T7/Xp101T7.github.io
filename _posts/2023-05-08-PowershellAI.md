---
title: Building Powershell AI
date: 2023-05-09 12:00:00 -500
categories: [PowershellAI,AI]
tags: [AI,PowershellAI,Terminal,Code]
---

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Had to add <a href="https://twitter.com/hashtag/PowerShellAI?src=hash&amp;ref_src=twsrc%5Etfw">#PowerShellAI</a> to this &quot;Generative AI Tools Landscape&quot; graphic 🙂. <a href="https://t.co/zxmDNm6JZ6">pic.twitter.com/zxmDNm6JZ6</a></p>&mdash; Heiko Brenn (@HeikoBrenn) <a href="https://twitter.com/HeikoBrenn/status/1652728071965798401?ref_src=twsrc%5Etfw">April 30, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


Installation was Easy follow the Installation Steps here:

https://github.com/dfinke/PowerShellAI

Create and API key from OpenAI which you should have done months ago. 😂😂

https://platform.openai.com/account/api-keys

Then make sure you store the creds on your local host. 

Use my ChatGPT generated Blog post to store your creds as secrets or use the store as environment variables via GUI tool via windows System Properties. 

	sysdm.cpl

OR

	setx API_key "sk-key”

Reload Terminal  or 

	refreshenv
	$env:API_Key

Now your off to the races 

	chat "How do I use powershellAI to become the best Cybersecurity Pro in all the lands"

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZX5gwzJe7eI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


