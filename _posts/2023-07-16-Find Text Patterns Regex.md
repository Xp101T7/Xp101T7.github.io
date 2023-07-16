---
title: Finding Text Patterns via Regex Automation
date: 2023-07-16 12:00:00 -500
categories: [Automation,Regex]
tags: [regex101,Python,Automation,Regex]


---

> Music is a higher revelation than all wisdom and philosophy.
> — <cite>Ludwig van Beethoven</cite>

---

## Finding Text Patterns via Regex Automation

[AndrewRathbun/DFIRRegex: A repo to centralize some of the regular expressions I've found useful over the course of my DFIR career.](https://github.com/AndrewRathbun/DFIRRegex)

[pemistahl/grex: A command-line tool and Rust library for generating regular expressions from user-provided test cases](https://github.com/pemistahl/grex)



[Regex101](https://regex101.com/)

[regexr](https://regexr.com/)

Use Faker to create some dummy data to regex parse. 

```python

import pandas as pd
from faker import Faker

# Can set the language
# fake = Faker('se_ES')
fake = Faker()
profiles = [fake.profile() for i in range(4)]

# store fake data in Pandas
df = pd.DataFrame(profiles)
# Select only the columns that matter
df = df[['name', 'mail', 'address', 'job', 'company']]
#print(df.columns)
#print(fake.phone_number())

# create more fake data: phone number
numbers = [fake.phone_number() for i in range(4)]

# new column in the dataframe pulled from the numbers list
df['phone_number'] = numbers
    
#print(df.columns)

# change column names
df.rename(columns={'mail': 'email'}, inplace=True)

# change column names
df.to_csv('fake_data.csv', index=False)
```

Open the csv in vscode

Copy the raw data and drop into Regex101 excluding the header




![[Pasted image 20230716172953.png]]

