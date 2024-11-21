---
title: <%tp.system.prompt("Title")%>
date: <%tp.date.now("YYYY-MM-DD")%> 12:00:00 -500
categories: [<%tp.system.prompt("Category1")%>,<%tp.system.prompt("Category2")%>]
tags: [<%tp.system.prompt("Tag1")%>,<%tp.system.prompt("Tag2")%>,<%tp.system.prompt("Tag3")%>,<%tp.system.prompt("Tag4")%>]
<%* let qcFileName = await tp.system.prompt("URL Title")
text = qcFileName.replace(":", " -") 
titleName = tp.date.now("YYYY-MM-DD") + "-" + text 
await tp.file.rename(titleName);-%>
<%await tp.file.move("/_posts/" + titleName)%>
---

<% tp.web.daily_quote() %>

---

## <%tp.system.prompt("Title")%>

