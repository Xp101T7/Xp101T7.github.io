---
title: Tinder Automation
date: 2023-07-16 12:00:00 -500
categories: [Automation,Python]
tags: [Python,Selenium,Automation,Tinder]


---

> Champions keep playing until they get it right.
> — <cite>Billie Jean King</cite>

---

## Tinder Automation

First login to your tinder account if you get banned you just need to create another account using google voice and another email address. Do not use the same photos you did before. 

Once logged in navigate to the heart button and inspect element

Should be XPath of 
	like_button = driver.find_element(by="xpath", value='//button//span[text()="Like"]') 


Now we need to import selenium, set web and path to driver variables, then point selenium to those variables to have it access the elements in the site using the chromedriver. 

Then we need to set the elements of interest considering popups that may occur. We want to click through any popups. Also we want to send opening lines to things like say something nice in an automated fashion. 

We set a variable and reference it as an attribute in the selenium send_keys function.

```python 
from selenium import webdriver
from selenium.webdriver.chrome.service import Service

opening_line = "Whats your spirit animal?"

# Define website to open and start driver object
web = 'https://www.tinder.com'
path = 'C:/Users/hecki/Downloads/chromedriver_win32'  # introduce path here

#Open the browser driver at this service path, use chorme and then navigate to the web address stored in the we variable
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service)
driver.get(web)
driver.maximize_window()

# Locating the elements we're going to interact with in this automation
like_button = driver.find_element(by="xpath", value='//button//span[text()="Like"]')

its_match_window = driver.find_element(by="xpath", value='//textarea[@placeholder="Say something nice!"]')
its_match_window.send_keys(opening_line)

send_message_button = driver.find_element(by='xpath', value='//button/span[text()="Send"]')
send_message_button.click()

close_its_match_window = driver.find_element(by='xpath', value='//button[@title="Back to Tinder"]')
close_its_match_window.click()

# XPath refrences to changing button elements
# //button/span[text()="Maybe Later"]
# //button/span[text()="Not interested"]
# //button/span[text()="No Thanks"]
```

---

## Adding for Loops, Try and Except Blocks, and Waits


Now that we have the elements of interest we can go ahead and add the pop windows to a find_element function where value = leverages the pipe character `|` as an or operator. 

	        box = driver.find_element(by='xpath', value='//button/span[text()="Maybe Later"] | //button/span[text()="Not interested"] | //button/span[text()="No Thanks"]')

We need to set the like_button element variable to leverage driver.execute_script() instead of the common .click() method that works most often. JavaScript code complexity seems to be the issue here. 

	driver.execute_script("arguments[0].click();", like_button)

We then need to add a range for the elements in a for loop for swipes/likes on the elements on tinder. 

```Python
for i  in range(number_of_swipes):
    try:
        #Locating the elements we're going to interact with in this automation
        like_button = driver.find_element(by="xpath", value='//button//span[text()="Like"]')
        driver.execute_script("arguments[0].click();", like_button)
        
        its_match_window = driver.find_element(by="xpath", value='//textarea[@placeholder="Say something nice!"]')
        its_match_window.send_keys(opening_line)

        send_message_button = driver.find_element(by='xpath', value='//button/span[text()="Send"]')
        send_message_button.click()

        close_its_match_window = driver.find_element(by='xpath', value='//button[@title="Back to Tinder"]')
        close_its_match_window.click()
        # Pop Up windows elements
		box = driver.find_element(by='xpath', value='//button/span[text()="Maybe Later"] | //button/span[text()="Not interested"] | //button/span[text()="No Thanks"]')
        
    except:
	    pass
```

We will take this further thought because we want to handle the exceptions if there is no match and a pop up occurs.

So if there is no match we will get no such element exception, so lets import the exception and then add this error  handling step to the except block.

If there isnt a pop up try pop elements or value's if still exception except NoSuchElement pass

```python 
    except NoSuchElementException:
        try:
            # Pop-ups: If there's a new pop-up, use one of the 3 xpath below and edit the value in the text attribute
            # box = driver.find_element(by='xpath', value='//div[@data-testid="dialog"]//span[text()="Maybe Later"] | //div[@data-testid="dialog"]//span[text()="Not interested"] | //div[@data-testid="dialog"]//span[text()="No Thanks"]')
            box = driver.find_element(by='xpath', value='//button/span[text()="Maybe Later"] | //button/span[text()="Not interested"] | //button/span[text()="No Thanks"]')
            box.click()
        except NoSuchElementException:
            pass
```

We need to import "by", "WebDriverWait", and "expected_conditions as EC"

So we add sleep timer before the for loop for the element swiping. 
We then need to add a wait to the execute_script block for the click like button.

wait 5 seconds until EC.element is clickable in the execute_script. Then add miminal waits. 

use randint from 1,5 to just wait randomly 1-5 seconds. We can  Webdriverwait explicit waits or implicit waits with time.sleep or use randint.


```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException
#Create wait time
import time
#Create random numbers 
from random import randint

#Create a list of phrases I like later and have do random number generated select a phrase.
opening_line = "Whats your spirit animal?"
number_of_swipes = 50
##Test swipes
#number_of_swipes = 5

#Define website to open and start driver object
web = 'https://www.tinder.com'
path = 'C:/Users/hecki/Downloads/chromedriver_win32'  # introduce path here

#Open the browser driver at this service path, use chorme and then navigate to the web address stored in the we variable
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service)
driver.get(web)
driver.maximize_window()


time.sleep(10)
for i  in range(number_of_swipes):
    try:
        #Locating the elements we're going to interact with in this automation
        #like_button = driver.find_element(by="xpath", value='//button//span[text()="Like"]')
        like_button = WebDriverWait(driver, 5).until(EC.element_to_be_clickable((By.XPATH, )))
        driver.execute_script("arguments[0].click();", like_button)
        time.sleep(randint(1, 3))
        
        its_match_window = driver.find_element(by="xpath", value='//textarea[@placeholder="Say something nice!"]')
        its_match_window.send_keys(opening_line)
        time.sleep(1)
        
        send_message_button = driver.find_element(by='xpath', value='//button/span[text()="Send"]')
        send_message_button.click()
        time.sleep(1)
        
        close_its_match_window = driver.find_element(by='xpath', value='//button[@title="Back to Tinder"]')
        close_its_match_window.click()
        time.sleep(1)
        
        # Pop Up windows elements
        
    except NoSuchElementException:
        try:
            # Pop-ups: If there's a new pop-up, use one of the 3 xpath below and edit the value in the text attribute
            box = driver.find_element(by='xpath', value='//button/span[text()="Maybe Later"] | //button/span[text()="Not interested"] | //button/span[text()="No Thanks"]')
            box.click()
        except NoSuchElementException:
            pass

    # Update
    # //button/span[text()="Maybe Later"]
    # //button/span[text()="Not interested"]
    # //button/span[text()="No Thanks"]
```

---

## Connect Selenium to the Existing Browser No Sign-in Process in Live ongoing session.


Add import options from selenium

	from selenium.webdriver.chrome.options import Options

add the options to the the code and use add_experimental_option to use debuggeraddress. Check the ports in use on your hosts. 

copilot "how to see the ports in use on my pc using powershell?"
	Get-NetTCPConnection

	Get-NetTCPConnection | findstr 22

Select a port range not in use for the localhost server connection for the chromedriver and selenium debugger.

	options = Options()
	options.add_experimental_option("debuggerAddress", "localhost:9222")

Create a folder where we are going to save changes to google chrome. 

--remote-debugging-port=<your_port_choice> --user-data-dir="your\custom\folder \path"

now find the chrome.exe application default location is at:

	C:\Program Files\Google\Chrome\Application\chrome.exe

Here is the powershell terminal code we need to use 

	 & "C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222 --user-data-dir="C:\Users\you\project\chrome_profile"

	chat "im getting this error when trying to use chrome debugger mode,Unexpected token 'remote-debugging-port=9222' in expression or statement."

Apologies for the confusion. It seems like you are trying to run the command in a PowerShell environment, which requires a slightly different syntax. Here's the correct command to open Chrome in debugger mode using PowerShell:

```
& "C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222
```

Make sure to replace the path with the correct location of the chrome.exe file on your system. The `&` symbol is used to execute the command.

Once you open chrome debugger window run your code set a lower swipe limit before you get started then crank it up. 

Working tender automation code

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException
#Create wait time
import time
#Create random numbers 
from random import randint

#Create a list of phrases I like later and have do random number generated select a phrase.
opening_line = "Whats your spirit animal?"
#number_of_swipes = 50
##Test swipes
number_of_swipes = 20

#Define website to open and start driver object
web = 'https://www.tinder.com'
path = 'C:/Users/you/Downloads/chromedriver_win32'  # introduce path here

#Open the browser driver at this service path, use chorme and then navigate to the web address stored in the we variable
# Connecting Selenium to an existing browser
options = Options()
options.add_experimental_option("debuggerAddress", "localhost:9222")
service = Service(executable_path=path)
driver = webdriver.Chrome(service=service, options=options)
driver.get(web)
# driver.maximize_window()


time.sleep(10)
for i  in range(number_of_swipes):
    try:
        #Locating the elements we're going to interact with in this automation
        #like_button = driver.find_element(by="xpath", value='//button//span[text()="Like"]')
        like_button = WebDriverWait(driver, 5).until(EC.element_to_be_clickable((By.XPATH, '//button//span[text()="Like"]')))
        driver.execute_script("arguments[0].click();", like_button)
        time.sleep(randint(1, 3))
        
        its_match_window = driver.find_element(by="xpath", value='//textarea[@placeholder="Say something nice!"]')
        its_match_window.send_keys(opening_line)
        time.sleep(1)
        
        send_message_button = driver.find_element(by='xpath', value='//button/span[text()="Send"]')
        send_message_button.click()
        time.sleep(1)
        
        close_its_match_window = driver.find_element(by='xpath', value='//button[@title="Back to Tinder"]')
        close_its_match_window.click()
        time.sleep(1)
        
        # Pop Up windows elements
        
    except NoSuchElementException:
        try:
            # Pop-ups: If there's a new pop-up, use one of the 3 xpath below and edit the value in the text attribute
            box = driver.find_element(by='xpath', value='//button/span[text()="Maybe Later"] | //button/span[text()="Not interested"] | //button/span[text()="No Thanks"]')
            box.click()
        except NoSuchElementException:
            pass

    # Update
    # //button/span[text()="Maybe Later"]
    # //button/span[text()="Not interested"]
    # //button/span[text()="No Thanks"]
```


---

## Creating EXE and Setting Task to taskschd.msc

First create the EXE using the following commands that leverage pyinstaller

```python
pip install pyinstaller
```

```python
pyinstaller --onefile .\tinderauto1.1.0.py
```

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

**Run debugger if not working 

When running a `.bat` file, if you want to debug or see what's happening step-by-step, you can use the `echo` command to print out messages or variable values at different stages of your script. You can also remove the `@echo off` line from the top of your script, if it's present, to see each command as it's executed.

Here is an example of a `.bat` file with debug information:

```bat
echo Starting the script...
python C:\path\to\your\script.py
if %ERRORLEVEL% NEQ 0 (
    echo Script exited with error %ERRORLEVEL%.
) else (
    echo Script completed successfully.
)
pause
```

This script will:

- Print "Starting the script..." at the beginning.
- Run your Python script.
- Check the exit code of your Python script. If it's anything other than 0 (which indicates success), it will print "Script exited with error" followed by the error code. If it's 0, it will print "Script completed successfully."
- Pause at the end so you can see the output before the command window closes.

Replace `"C:\path\to\your\script.py"` with the actual path to your Python script.

Remember to run your `.bat` file from a command prompt so you can see the output. If you just double-click the `.bat` file in Windows Explorer, the command window might close before you can see what happened. To run the `.bat` file from a command prompt, navigate to the directory containing the `.bat` file, and type the name of the `.bat` file.

Might only need to call the exe on not mess with python referencing the exe file on windows, on mac you may need to use python. 

SyntaxError: Non-UTF-8 code starting with '\x90' in file means you probably don't need to use python /path/to/your/script.exe in the bat file.

Tested this work flow. EXE is now set to run everyday at a set time right before I get off work so I have time to chat in actual responses. 

---

## Future Additions

- Add list of auto responses that are clever.
- Fix the unlimited likes pop-up for free use applications.
- Add in Feature that texts me a response from a user with a link for me to pivot to the IOS/Android Tinder application. (Very Difficult and not worth it bc Id hope I find someone by the time I would need to implement this.)
- Add this functionality for multiple sites. Bumble, Hinge, Zoosk, etc...