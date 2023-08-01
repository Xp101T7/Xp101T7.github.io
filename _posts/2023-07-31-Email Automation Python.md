---
title: Email Automation with Python
date: 2023-07-31 12:00:00 -500
categories: [Automation,Python]
tags: [Code,Google,Automation,Python]

---

> Time stays long enough for anyone who will use it.
> — <cite>Leonardo da Vinci</cite>

---

## Email Automation with Python


Activate 2FA on your google account.

Then navigate to apppasswords
https://myaccount.google.com/u/5/apppasswords

set the env variable and import OS

```python
import os
email_password = os.environ.get("EMAIL_PASSWORD")
```

Setting Environment Variables

	$env:MY_VARIABLE = "my_value"

Check the variable 

	echo $env:MY_VARIABLE

Also test print

	print(email_password)

1337 C0d37

![Pasted image 20230731212549.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230731212549.png)

If you have a sslcertverificationerror you need to find Install Certificates.command and then double click and install certs. 

```python
import smtplib
import ssl
import os
#This library comes with python
from email.message import EmailMessage


# Define email sender and receiver
email_sender = 'cyb3rbu7n7@gmail.com'
email_password = os.environ.get("EMAIL_PASSWORD")
#email_password = 'write-password-here'
email_receiver = 'corybcrook@gmail.com'

# Set the subject and body of the email
subject = "Im a automater"
body = """
I'm now a hacker go check out the blog post!!! https://heckintechin.tech/posts/Email-Automation-Python/
"""

#Create email element then add all the variables as elements
em = EmailMessage()
em['From'] = email_sender
em['To'] = email_receiver
em['Subject'] = subject
em.set_content(body)

# Add SSL (layer of security)
context = ssl.create_default_context()

# Log in and send the email
with smtplib.SMTP_SSL('smtp.gmail.com', 465, context=context) as smtp:
    smtp.login(email_sender, email_password)
    smtp.send_message(em)
    print('Email sent successfully')

```

---
## Add Attachments

I added the attachment code for imghdr and then new it wouldn't work when I uploaded a PDF instead of an image.  So I asked chat GPT to read in a file and send it. 

```Python
import smtplib
import ssl
import os
#This library comes with python
from email.message import EmailMessage
import imghdr

# Define email sender and receiver
email_sender = 'cyb3rbu7n7@gmail.com'
email_password = os.environ.get("EMAIL_PASSWORD")
#email_password = 'write-password-here'
email_receiver = 'corybcrook@gmail.com'

# Set the subject and body of the email
subject = "Im a automater"
body = """
I'm now a hacker go check out the blog post!!! https://heckintechin.tech/posts/Email-Automation-Python/
"""

#Create email element then add all the variables as elements
em = EmailMessage()
em['From'] = email_sender
em['To'] = email_receiver
em['Subject'] = subject
em.set_content(body)

# Attach File rb stands for read bytes smf read in a image file
#with open('CyberSec_Resume_7-18-23.pdf') as file:
#    file_data = file.read()
#    file_type = imghdr.what(file.name)
#    file_name = file.name 
#em.add_attachment(file_data, filename=file_name, subtype=file_type, maintype='image')

with open('CyberSec_Resume_7-18-23.pdf', 'rb') as file:
    file_data = file.read()
    file_name = file.name
em.add_attachment(file_data, filename=file_name, maintype='application', subtype='octet-stream')

# Add SSL (layer of security)
context = ssl.create_default_context()

# Log in and send the email
with smtplib.SMTP_SSL('smtp.gmail.com', 465, context=context) as smtp:
    smtp.login(email_sender, email_password)
    smtp.send_message(em)
    print('Email sent successfully')
```

Chat GPT for the WIN

You're welcome! I'm glad to hear that it worked for you. If you have any more questions or need further assistance, feel free to ask. Happy coding! 🎉