---
title: Word Doc Automation via Docxtpl
date: 2023-07-15 12:00:00 -500
categories: [Python,Automation]
tags: [Automation,Faker,Python,Word]


---

> Experience keeps a dear school, but fools will learn in no other.
> — <cite>Benjamin Franklin</cite>

---

## Word Doc Automation

[Word Doc Automation](https://docxtpl.readthedocs.io/en/latest/)

We can generate data into pandas dataframes by using faker data to generate the dataset.

We can also use the docxtpl library to handle the data from our dataframe and autofill any word doc in bulk replacing the elements with the key value you set from faker to docxtpl word documents.

Elements look like 

```
{{Email}}
{{User_Name}}
```

Once you set the elements you can check them with print functions using iterrows 

```python 
import pandas as pd
from datetime import datetime
from docxtpl import DocxTemplate

doc = DocxTemplate("template-manager-info.docx")
my_name = "Cory Crook"
my_phone = "+1 361-696-0782"
my_email = "heckya@heckintechin.tech"
my_address = "Corpus Christy, TX"
today_date = datetime.today().strftime("%d %b, %Y")

context = {'my_name': my_name, 'my_phone': my_phone, 'my_email': my_email, 'my_address': my_address,
           'today_date': today_date}

df = pd.read_csv('fake_data.csv')

for index, row in df.iterrows():
    print(index)
    print(row)
```

now we need to create a dictionary for the elements and then our personal data and combine them together.

Now we create the docs

```python
import pandas as pd
from datetime import datetime
from docxtpl import DocxTemplate

doc = DocxTemplate("template-manager-info.docx")
my_name = "Cory Crook"
my_phone = "+1 361-696-0782"
my_email = "heckya@heckintechin.tech"
my_address = "Corpus Christy, TX"
today_date = datetime.today().strftime("%d %b, %Y")

my_context = {'my_name': my_name, 'my_phone': my_phone, 'my_email': my_email, 'my_address': my_address,
           'today_date': today_date}

df = pd.read_csv('fake_data.csv')

for index, row in df.iterrows():

    context = {'hiring_manager_name': row['name'],
               'address': row['address'],
               'phone_number': row['phone_number'],
               'email': row['email'],
               'job_position': row['job'],
               'company_name': row['company']}
    
    context.update(my_context)
    
    doc.render(context)
    doc.save(f"generated_doc_{index}.docx")
```

![Pasted image 20230715195739.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715195739.png)

![Pasted image 20230715200452.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715200452.png)


