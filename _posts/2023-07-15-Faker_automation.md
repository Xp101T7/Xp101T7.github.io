---
title: Create Fake Data with Python Faker Library
date: 2023-07-15 12:00:00 -500
categories: [Python,Faker]
tags: [Python,Data,Word,Faker]


---

> There is only one corner of the universe you can be certain of improving, and that's your own self.
> — <cite>Aldous Huxley</cite>

---

# Create Fake Data with Python Faker Library


pip install faker and pandas

	chat "how do i pip install faker and pandas at the same time?"

To install multiple Python packages, such as Faker and Pandas, at the same time using pip, you can list the package names separated by a space. Here's the command you can use:

```
pip install faker pandas
```

Simply open your terminal or command prompt, and execute the above command. This will install both the Faker and Pandas packages in your Python environment simultaneously.

Then test generate 1 or more fake profiles

```python 
import pandas as pd
from faker import Faker

fake = Faker()

for i in range(10):

    print(fake.profile())
```

use keys to get the keys

	print(fake.profile().keys())

pandas uses data frames so we need to understand that you can use any lists or datasets passed to pandas which uses data frames to process the list comprehension.

Create data frames one of two ways below one liners are better most times.
```python
import pandas as pd
from faker import Faker

fake = Faker()
profiles = [fake.profile() for i in range(10)]

#Same as above
profiles = []
for i in range(10):
	profiles.append(fake.profile())
```


Then we will create a data frame 

```python
df = pd.DataFrame(profiles)

print(df)
print(df.columns)
```

This can be opened up anywhere that accepts data sets.

Now add fake phone number by adding the column name from the fake profile method.

```python
import pandas as pd
from faker import Faker

fake = Faker()

profiles = [fake.profile() for i in range(10)]
df = pd.DataFrame(profiles)
#print(df.columns)
print(fake.phone_number())
```


```python
import pandas as pd
from faker import Faker

fake = Faker()
profiles = [fake.profile() for i in range(10)]
df = pd.DataFrame(profiles)

#print(df.columns)
#print(fake.phone_number())
# create more fake data: phone number
numbers = [fake.phone_number() for i in range(10)]

# new column in the dataframe pulled from the numbers list
df['phone_number'] = numbers
print(df.columns)
```

it outputs the following text

```txt
Index(['job', 'company', 'ssn', 'residence', 'current_location', 'blood_group',
       'website', 'username', 'name', 'sex', 'address', 'mail', 'birthdate',
       'phone_number'],
      dtype='object')
```

We can replace the columns and change the names

mail can be changed

```python
import pandas as pd
from faker import Faker

# Can set the language
# fake = Faker('se_ES')
fake = Faker()
profiles = [fake.profile() for i in range(10)]

# store fake data in Pandas
df = pd.DataFrame(profiles)
# Select only the columns that matter
df = df[['name', 'mail', 'address', 'job', 'company']]
#print(df.columns)
#print(fake.phone_number())

# create more fake data: phone number
numbers = [fake.phone_number() for i in range(10)]

# new column in the dataframe pulled from the numbers list
df['phone_number'] = numbers
    
#print(df.columns)

# change column names
df.rename(columns={'mail': 'email'}, inplace=True)

# change column names
df.to_csv('fake_data.csv', index=False)
```
