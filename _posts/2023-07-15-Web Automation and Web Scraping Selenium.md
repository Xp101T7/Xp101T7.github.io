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

![Pasted image 20230715213116.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715213116.png)

Its working you can see that it recognizes the automated test software.

### Finding the Elements

Go to the website and inspect and in Dev Tools you will have all the HTML Elements.

press ctrl+shift+c while in dev tool or use the element selector top left of dev tools.

Look for a pattern in the elements to access them using XPath

once understood the elements you want to select press ctrl+f

![Pasted image 20230715214510.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715214510.png)

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
path = 'C:/Users/you/Downloads/chromedriver_win32'  # introduce path here

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
path = 'C:/Users/you/Downloads/chromedriver_win32'  # introduce path here

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
path = 'C:/Users/you/Downloads/chromedriver_win32'  # introduce path here

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


---

## Execute the Script Everyday

STRFTIME Cheatsheet
[strftime cheatsheet](https://strftime.org/)

We need to turn the PY file to exe. So we have to do importation.

Add the following code

```python 
from datetime import datetime #This helps us manipulate date and time to struct when script runs
import os
import sys

application_path = os.path.dirname(sys.executable) # Getting the path of the exe we are creating.
```

Sometimes we dont know where the exe is export.

Use datetime to change file so we can get new files for each day.  Can do this but / is not good practice between file path and file location from os to os. 

```python
month_day_year =  now.strftime("%m%d%y")
...
...
df_headlines.to_csv(f'{application_path}/{month_day_year}_headline.csv')
```


So change the app path with the filename so OS path join can take care of it.

We redid the code to set file_name to a variable and then set the final path to a variable to handle the slash issues.

```python 
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd
from datetime import datetime #This helps us manipulate date and time to struct when script runs
import os
import sys

application_path = os.path.dirname(sys.executable) # Getting the path of the exe we are creating.

now = datetime.now()
#String from time so we can format
#MMDDYYYY
month_day_year =  now.strftime("%m%d%y")


# Define website to open
web = 'https://www.thesun.co.uk/sport/football/'
path = 'C:/Users/you/Downloads/chromedriver_win32'  # introduce path here

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
file_name = f'{month_day_year}_headline.csv'
#os path join takes care of /\ os to os path problems 
final_path = os.path.join(application_path, file_name) 

# useing application_path for exe so the file outputs at that location and then set the strftime var to create unique header 
df_headlines.to_csv(final_path)

driver.quit() #quite after close.
```

---

## Covert PY to EXE

We need to convert this file to exe so we can schedule the py file to run whenever we want. 

	pip install pyinstaller


cd to the directory where you py file is located

dir or ls to check your files

	pyinstaller --onefile scrapersec.py

had to remove pathlib will need to recode those files from the file_folder automation with the error information in mind.

	The 'pathlib' package is an obsolete backport of a standard library package and is incompatible with PyInstaller.

	pip uninstall pathlib

EXE compiled and ran and everything loaded like a champ. 🥇

Now its time to pick a security related website.

Scrap bleepingcomputer for headlines 

used the following XPaths

	//div[@class="bc_latest_news_text"]/div/span/a
	//div[@class="bc_latest_news_text"]/h4
	//div[@class="bc_latest_news_text"]/p


New working code I tested using the Headless.py file instead of the app_path exe py file bc there is no app path yet to output the csv to.

Ran into some issues and found that walking back the XPath can help. Go to the element you want type that element and walk it back to parent element using ..

`//p/https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/..` This led to me discovering I was close before but I was accessing the elements incorrectly. I went up nodes and tried access things like `Message: no such element: Unable to locate element: {"method":"xpath","selector":"./div/div/div/div/ul/li/div/h4"}` but these doesn't work going up the nodes that far. You have to narrow as close to the element as possible. I noticed accessing div class is best.  I need to figure out how to create multiple iterable lists that access multiple elements and merge those losts after they are compiled.

The code below is working well on [bleepingcomputer](https://www.bleepingcomputer.com/)

```python 
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
import pandas as pd
from datetime import datetime #This helps us manipulate date and time to struct when script runs
import os
import sys

application_path = os.path.dirname(sys.executable) # Getting the path of the exe we are creating.

now = datetime.now()
#MMDDYYYY
month_day_year =  now.strftime("%m%d%y") #String from time so we can format


# Define website to open
web = 'https://www.bleepingcomputer.com/'
path = 'C:/Users/you/Downloads/chromedriver_win32'  # introduce path here

#add headless mode
options = Options()
options.add_argument('--headless')

#Set exe path to the chromedriver path
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service, options=options)

# open a driver
driver.get(web)

#Method called find element you can also use id or class but xpath is what we are using. add s to find elements as list(iterable) or just one element
containers = driver.find_elements(by="xpath", value='//div[@class="bc_latest_news_text"]')

titles = []
subtitles = []
links = []

for container in containers:
    title = container.find_element(by="xpath", value='./h4/a').text #With .text we only get text in the element
    subtitle = container.find_element(by="xpath", value='./p').text
    link = container.find_element(by="xpath", value='./h4/a').get_attribute("href")
    titles.append(title)
    subtitles.append(subtitle)
    links.append(link)

my_dict = {"title": titles, 'subtitle': subtitles, "link": links}
df_headlines = pd.DataFrame(my_dict)
file_name = f'{month_day_year}_headline.csv'
#os path join takes care of /\ os to os path problems 
final_path = os.path.join(application_path, file_name) 

# useing application_path for exe so the file outputs at that location and then set the strftime var to create unique header 
df_headlines.to_csv(final_path)

driver.quit() #quite after close.


```

I compiled the EXE and testing scraper 101 complete!!!

---

## Setting Cronjob 

[crontabguru](https://crontab.guru/)

Build your cron tab jobs at the link above. Open Crontab

	crontab -e

Paste the cron format from the site above. Check intervals closely.

press i for insert mode

drag the path from macos or add the path behind the cronjob save and exit vi

	0 9 * * * /Users/dude/Projects/automation/dist/scrape

check the job with 

	crontab -l

---

## Setting Schedule Task

To get it, open up a terminal, write either `which python`  or `which python3`  and then press enter.

Just for fun bc I dont have scoop installed > which

chat "how can i find the path to python via powershell"
To find the path to Python via PowerShell, you can use the `Get-Command` cmdlet. Here's how you can do it:

1. Open PowerShell by searching for "PowerShell" in the Start menu or by pressing `Win + X` and selecting "Windows PowerShell" or "Windows PowerShell (Admin)".

2. In the PowerShell prompt, type the following command and press Enter:
   ```
   Get-Command python
   ```

3. PowerShell will display the information about the Python executable, including the path. Look for the `Source` property, which will show the path to the Python executable.

For example, the output might look like this:
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Application     python.exe                                         3.9.7      C:\Python39\python.exe
```

In this example, the path to the Python executable is `C:\Python39\python.exe`.

Note that the output may vary depending on the installed version and location of Python on your system.

Create a text document open it up

write path where python is located and then add the script location

	"C:\path\python.exe" "C:\path\scriptfolder\the_script"

Then save as bat file

Not getting output hmmm????



Open Task Scheduler > Create Basic Task > name and descreptio > Daily > Select Time and Start time > Action - Start a program - Select the bat file > finish > Right Click job and test `Run` 

	chat "how to schedule a python exe to run on a scheduled task using a bat file on windows"

To schedule a Python executable to run on a scheduled task using a batch file on Windows, you can make use of the Task Scheduler utility. Here's an example of how you can do it:

1. Create a new text file and open it in a text editor.

2. Add the following lines to the file, replacing `C:\path\to\python.exe` with the actual path to your Python executable and `C:\path\to\script.py` with the actual path to your Python script:
   ```batch
   @echo off
   C:\path\to\python.exe C:\path\to\script.py
   ```

3. Save the file with a `.bat` extension, for example, `run_python.bat`.

4. Press `Win + R` to open the Run dialog, then type `taskschd.msc` and press Enter. This will open the Task Scheduler.

5. In the Task Scheduler, click on "Create Basic Task" or "Create Task" in the Actions pane, depending on your Windows version.

6. Follow the prompts to create a new task. Provide a name and description for the task.

7. In the "Actions" tab, click "New" to create a new action.

	chat "what is @echo off"

`@echo off` is a command used in batch files on Windows to disable the display of commands being executed. When `@echo off` is included at the beginning of a batch file, it prevents the command prompt from displaying each command as it is executed. This can make the output of the batch file cleaner and easier to read.

By default, when a batch file is executed, each command is displayed in the command prompt window before it is executed. This can be useful for troubleshooting or understanding the progress of the batch file. However, in some cases, you may want to hide the commands and only display the output or specific messages.

The `@` symbol before `echo off` is used to prevent the `echo off` command itself from being displayed. It ensures that only the output or messages specified in the batch file will be shown, without the commands themselves.

