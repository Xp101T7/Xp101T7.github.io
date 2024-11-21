---
title: XPath Guide
date: 2023-07-15 12:00:00 -500
categories: [XPath,Scraping]
tags: [Web,Scraping,Automation,XPath]


---

> Knowledge is knowing that a tomato is a fruit. Wisdom is knowing not to put it in a fruit salad.
> — <cite>Brian O'Driscoll</cite>

---

## XPath Guide

XML path language

Selects xml path selecting nodes from xml document but can use to select elements from html pages. Another way is CSS, its used to style web pages. We will use XPath cuz its easy.

//tagName = basic way to locate element or node. If you want to select all h1 elements you use //h1. pick a matching element that is at any position. 

Can select a position

//tagName\[1\]

Like //h1\[1\]

//tagname\[@attributeName="Value"\]
is used for average Xpath expression.

XPath functions to are used to find specific values 

starts-with() text at begging 
contain() contains text

To wrap the attribute name in attr value

//tagname\[@attributeName, "Value"\]

Can use and or 

//tagname\[(expression 1) and (expression 2)\]

---

## XPath Test Your XPath

[XPath Playground](https://scrapinghub.github.io/xpath-playground/)

Can test you html code and scrape it as examples.

![Pasted image 20230715205028.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715205028.png)

Here are some other examples of Xpath

	//h1/text()
	//p
	//p[1]
	//div
	//div/text()
	//div[@class="full-script"]
	//div[@class="full-script"]/text()
	//p[(@class="plot") or (@class="plot2")]
	//p[(@class="plot") and (@class="plot2")]
	//p[contains(@class="plot")]
	//p[contains(@class, "plot")]
	//div[contains(@class, "script")]

---

## XPath Special Characters and Syntax

![Pasted image 20230715205544.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715205544.png)

	//article/h1
	//article/p
	//article/div
	//article/text()
	//article/h1/text()
	//article//text() NOTE- Double // access any element at any position

![Pasted image 20230715205804.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715205804.png)

If you found a children element then you can look for hard to find parent element

	//h1/.
	//h1/..

inspect page ctrl+f

	//h1/..

![Pasted image 20230715210207.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715210207.png)

	//article/*
	//article/*/text()

![Pasted image 20230715210310.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715210310.png)







