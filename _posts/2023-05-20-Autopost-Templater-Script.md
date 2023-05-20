---
title: Creating Automated Template for Swift Blog Posting 
date: 2023-05-20
categories: [Templater,Obsidian]
tags: [Code,Templater,Obsidian,GitHub]
---

> The most technologically efficient machine that man has ever invented is the book.
> — <cite>Northrop Frye</cite>

## Automated New Note Template for Blog Posts


This quick article will allow you to create new templates in Obsidian on the fly while having the jekyll front matter and filename syntax auto populate. We are using Templater plugin for obsidian.

```
---
title: <%tp.system.prompt("Title")%>
date: <%tp.date.now("YYYY-MM-DD")%>
categories: [<%tp.system.prompt("Category1")%>,<%tp.system.prompt("Category2")%>]
tags: [<%tp.system.prompt("Tag1")%>,<%tp.system.prompt("Tag2")%>,<%tp.system.prompt("Tag3")%>,<%tp.system.prompt("Tag4")%>]
<%* let qcFileName = await tp.system.prompt("Note Title")
text = qcFileName.replace(":", " -") 
titleName = tp.date.now("YYYY-MM-DD") + "-" + text 
await tp.file.rename(titleName);-%>
<%await tp.file.move("/_posts/" + titleName)%>
---

<%tp.web.daily_quote()%>
```

The above code is as follows:

Set the code start  by using <% and end with %>

To prompt the user use the system prompt function and give the prompt window a title. Set the title specific to all the fields you will input. 

	<%tp.system.prompt("Your prompt title here!")%>

Use the date function next specify your date format to fetch that days date you create the post automatically.

	<%tp.date.now("YYYY-MM-DD")%>

Then lastly move the file name you specify in the file name prompt, add the date and text from the prompt, rename the defualt filename to the specified prompt, and then automatically move the new file to the \_posts folder using the new titleName

```
<%* let qcFileName = await tp.system.prompt("Note Title")
text = qcFileName.replace(":", " -") 
titleName = tp.date.now("YYYY-MM-DD") + "-" + text 
await tp.file.rename(titleName);-%>
<%await tp.file.move("/_posts/" + titleName)%>
```

Then show me the quote of the day.

	<%tp.web.daily_quote()%>


Follow these youtubes about templater setups in obsidian for more information:

<iframe width="560" height="315" src="https://www.youtube.com/embed/5j9fAvJCaig" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/QpJbeP8f55A" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

---

## Links and resources


