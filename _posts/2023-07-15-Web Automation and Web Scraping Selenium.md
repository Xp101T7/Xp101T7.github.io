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

---


## Creating the Driver


Import the web drivers and make another import for options and service

have to do this extra import in selenium 4

Write the code and test its working.

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd

# Define website to open
web = 'https://www.thesun.co.uk/sport/football/'
path = 'C:/Users/dude/Downloads/chromedriver_win32'  # introduce path here

#Set exe path to the chromedriver path
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service)
# open a driver
driver.get(web)
```

![[Pasted image 20230715213116.png]]

Its working you can see that it recognizes the automated test software.

### Finding the Elements

Go to the website and inspect and in Dev Tools you will have all the HTML Elements.

press ctrl+shift+c while in dev tool or use the element selector top left of dev tools.

Look for a pattern in the elements to access them using XPath

once understood the elements you want to select press ctrl+f

![[Pasted image 20230715214510.png]]

	//div[@class="teaser__copy-container"]

Navigate through the elements you are accessing.

Copy the XPath and go back to VSCode

Use the elements to find all elements instead of element

```python 
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd

# Define website to open
web = 'https://www.thesun.co.uk/sport/football/'
path = 'C:/Users/dude/Downloads/chromedriver_win32'  # introduce path here

#Set exe path to the chromedriver path
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service)
# open a driver
driver.get(web)

#Method called find element you can also use id or class but xpath is what we are using. add s to find elements or just one element
containers = driver.find_elements(by="xpath", value='//div[@class="teaser__copy-container"]')
```

We want to get to the elements nested below under the parent node

	//div[@class="teaser__copy-container"]/
	//div[@class="teaser__copy-container"]/a/h2

had to change to h3 to get all the titles

	//div[@class="teaser__copy-container"]/a/h3

This is sequenced access if we want.

Add the code and this works as is but we will change this container variable.

```python 
driver.find_element(by="xpath", value='//div[@class="teaser__copy-container"]/a/h3')
```

or set the container to the list of containers and use a for loop to iterate over that.

```python 
for container in containers:
    container.find_element(by="xpath", value='./a/h3')
```

Get the subtitles 

	//div[@class="teaser__copy-container"]/a/p


	chat "Duplicate lines of code shortcut in VSCode"

In Visual Studio Code (VSCode), you can duplicate lines of code using the following shortcut:

- `Shift + Alt + Down Arrow` (Windows/Linux)
- `Option + Shift + Down Arrow` (macOS)

Here's how you can use this shortcut to duplicate lines of code:

1. Place your cursor on the line(s) of code you want to duplicate.
2. Press the appropriate shortcut mentioned above.
3. The selected line(s) will be duplicated below the original line(s).

You can also duplicate multiple lines of code by selecting multiple lines before using the shortcut.

```python 
for container in containers:
    title = container.find_element(by="xpath", value='./a/h3').text #With .text we only get text in the element
    subtitle = container.find_element(by="xpath", value='./a/p').subtext
```

Now we want to scrap the links of each news.


```python 
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd

# Define website to open
web = 'https://www.thesun.co.uk/sport/football/'
path = 'C:/Users/hecki/Downloads/chromedriver_win32'  # introduce path here

#Set exe path to the chromedriver path
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service)
# open a driver
driver.get(web)

#Method called find element you can also use id or class but xpath is what we are using. add s to find elements as list(iterable) or just one element
containers = driver.find_elements(by="xpath", value='//div[@class="teaser__copy-container"]')


for container in containers:
    title = container.find_element(by="xpath", value='./a/h3').text #With .text we only get text in the element
    subtitle = container.find_element(by="xpath", value='./a/p').text

    link = container.find_element(by="xpath", value='./a').get_attribute("href")
```

When you want to get links of a site look for \<a ref or href 

for container in containers:  the container is an element and containers is a list.


---

## Exporting Data to a CSV File

How do we export all the elements we extract to a CSV file.

We have to create empty lists.

```python 
titles = []
subtitles = []
links = []
```

Then append the elements to the list

```python
    titles.append(title)
    subtitles.append(subtitle)
    links.append(link)
```

Fill the empty list with all the elements from the loop append to list.  Now that we have all the elements in the list you have to create the dictionary. It has key:value the key you can choose but the value is the lists. The dict then lets us built in  data frame in pandas.

CSV file was made I had to comment out the refrence to ./a/p bc it was throwing an error. I think it was the sequence changed for the site. 

	selenium.common.exceptions.NoSuchElementException: Message: no such element: Unable to locate element: {"method":"xpath","selector":"./a/p[0]"}

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd

# Define website to open
web = 'https://www.thesun.co.uk/sport/football/'
path = 'C:/Users/hecki/Downloads/chromedriver_win32'  # introduce path here

#Set exe path to the chromedriver path
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service)
# open a driver
driver.get(web)

#Method called find element you can also use id or class but xpath is what we are using. add s to find elements as list(iterable) or just one element
containers = driver.find_elements(by="xpath", value='//div[@class="teaser__copy-container"]')

titles = []
#subtitles = []
links = []

for container in containers:
    title = container.find_element(by="xpath", value='./a/h3').text #With .text we only get text in the element
    #subtitle = container.find_element(by="xpath", value='./a/p[0]').text
    link = container.find_element(by="xpath", value='./a').get_attribute("href")
    titles.append(title)
    #subtitles.append(subtitle)
    links.append(link)

my_dict = {"title": titles, "link": links}
df_headlines = pd.DataFrame(my_dict)
df_headlines.to_csv('headline-headless.csv')

driver.quit() #quite after close.
```

---

## Headless Mode


Set import options as seen above add in headless below the path variable

```python
options = Options()
options.headless = True
```

Add options=options to webdriver

	driver = webdriver.Chrome(service=service, options=options)

I get a warning about headless syntax deprecation

	chat "DeprecationWarning: headless property is deprecated, instead use add_argument('--headless') or add_argument('--headless=new')"

The warning message you mentioned is related to the `headless` property in Selenium. Starting from Selenium 4, the `headless` property is deprecated, and you should use the `add_argument('--headless')` or `add_argument('--headless=new')` method instead.

Here's an example of how you can update your code to address the deprecation warning:

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument('--headless')  # or options.add_argument('--headless=new')

driver = webdriver.Chrome(options=options)
```

By using the `add_argument` method with the `--headless` option, you can achieve the same functionality as the deprecated `headless` property. This will enable the headless mode for your Selenium WebDriver.

Working code for headless browser mode

```python 
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd

# Define website to open
web = 'https://www.thesun.co.uk/sport/football/'
path = 'C:/Users/hecki/Downloads/chromedriver_win32'  # introduce path here

#add headless mode
options = Options()
options.add_argument('--headless')

#Set exe path to the chromedriver path
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service, options=options)
# open a driver
driver.get(web)

#Method called find element you can also use id or class but xpath is what we are using. add s to find elements as list(iterable) or just one element
containers = driver.find_elements(by="xpath", value='//div[@class="teaser__copy-container"]')

titles = []
#subtitles = []
links = []

for container in containers:
    title = container.find_element(by="xpath", value='./a/h3').text #With .text we only get text in the element
    #subtitle = container.find_element(by="xpath", value='./a/p[0]').text
    link = container.find_element(by="xpath", value='./a').get_attribute("href")
    titles.append(title)
    #subtitles.append(subtitle)
    links.append(link)

my_dict = {"title": titles, "link": links}
df_headlines = pd.DataFrame(my_dict)
df_headlines.to_csv('headline-headless.csv')

driver.quit() #quite after close.
```


