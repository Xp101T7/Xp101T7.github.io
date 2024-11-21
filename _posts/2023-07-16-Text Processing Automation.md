---
title: Text Processing Automation using Python and Pandas Library
date: 2023-07-31 12:00:00 -500
categories: [Pandas,Python]
tags: [Pandas,TextProcessing,Automation,Python]


---

> Never interrupt your enemy when he is making a mistake.
> — <cite>Napoleon</cite>

## Text Processing Automation using Python and Pandas Library

![Pasted image 20230716201509.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716201509.png)

Pandas can handle millions and millions of lines, it wont crash on complex transforms, and its cross-platform.

![Pasted image 20230716201709.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716201709.png)

![Pasted image 20230716201758.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716201758.png)

![Pasted image 20230716201826.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716201826.png)

![Pasted image 20230716201913.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716201913.png)

![Pasted image 20230716201953.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716201953.png)

![Pasted image 20230716202045.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716202045.png)

---

## Text Processing with Python

![Pasted image 20230716202209.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230716202209.png)

Create files with random numbers, how to add characters to each line in our files, how to join the content into multiple files, remove duplicates and more we will be automating text processing with Python.

use numpy to create random numbers in this case int

```Python 
import pandas as pd
import numpy as np
```

## Create txt/csv File with Text Data

```python
import pandas as pd
import numpy as np

#Might need to create an array to handle the ValueError: high is out of bounds for int32 where we use int64
#arr = np.array([1000000000, 9999999999], dtype=np.int64)

# Create list of phone numbers (10 digits), size is 100. This is numpy array 
# Note: Phone numbers can start from 1000000000 to 9999999999
numbers = np.random.randint(low=1000000000, high=9999999999, size=(100,), dtype=np.int64)

# Save the numbers to a txt file using numpy's savetxt method
# The fmt='%i' argument specifies that the numbers should be saved as integers
# The newline='\n' argument specifies that each number should be on a new line
np.savetxt('C:\\Users\\user\\text_processing\\phone-number.txt', numbers, fmt='%i', newline='\n')

# Save the numbers to a csv file using numpy's savetxt method
np.savetxt('C:\\Users\\user\\text_processing\\phone-number.csv', numbers, fmt='%i', newline='\n')


# Create a DataFrame from numpy array and export it to a CSV file without index/header
df_phone_numbers = pd.DataFrame(numbers, columns=['phone-numbers'])
print(df_phone_numbers)
df_phone_numbers.to_csv('phone-number-2.csv', index=False, header=False)
```

```python
import pandas as pd
import numpy as np

# Create list of phone numbers in a TXT/CSV file (10 digits)
numbers = np.random.randint(1000000, 9999999, size=100)
numbers.tofile('./phone-number.txt', sep='\n')
numbers.tofile('./phone-number.csv', sep='\n')

# Create a DataFrame from numpy array and export it to a CSV file without index/header
df_phone_numbers = pd.DataFrame(numbers, columns=['phone-numbers'])
print(df_phone_numbers)
df_phone_numbers.to_csv('phone-number-2.csv', index=False, header=False)

```
---
## Add Characters to Each Line in CSV Files

```python
import pandas as pd

# Read CSV file with Pandas and name column as "phone-numbers"
df_phone_numbers = pd.read_csv('phone-number.csv', names=['phone-numbers'])
#print(df_phone_numbers)

df_phone_numbers['country-code'] = df_phone_numbers['phone-numbers'].apply(lambda x: f'+1{x}')
#print(df_phone_numbers)

# Read multiple CSV files
file_names = ['phone-number', 'phone-number-2']

for file in file_names:
    df = pd.read_csv(f'{file}.csv', names=['phone-numbers'])
    df['country-code'] = df['phone-numbers'].apply(lambda x: f'+1{x}')
    df.to_csv(f'code-{file}.csv', index=False)
```

---
## Remove Characters From Each Line in CSV Files

```python
import pandas as pd

# Read csv file 'code-phone-number.csv' and set the data type using dtype 'phone-number': as int, then set second column 'country-code': str Setting manually bc it will read as int and remove function only allows remove string.
df_phone_numbers = pd.read_csv('code-phone-number.csv', dtype={'phone-number': int, 'country-code': str})
print(df_phone_numbers.dtypes)

# Its important to extract string as we set above and use the .str below to Remove character '+' from column and save it in a new column "remove-plus"
# (r'') normally you would have to import re for regex but pandas extract allows regex \+(\d+) = Escape the meta character + and Capture only 1 or more digits then add .
df_phone_numbers['remove-plus'] = df_phone_numbers['country-code'].str.extract(r'\+(\d+)')
print(df_phone_numbers)

#export file and ignore the index field
df_phone_numbers.to.csv('remove-plust.csv', index=False)
```

---
## Concatenate Data in Multiple TXT/CSV Files

axis = 0 

![Pasted image 20230731195743.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230731195743.png)

Axis = 1

![Pasted image 20230731195854.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230731195854.png)

![Pasted image 20230731195857.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230731195857.png)

```python
import pandas as pd

# Concatenate multiple files you must have a common column
df_phone_numbers  = pd.read_csv('phone-number.csv', names=['phone-numbers'])
df_phone_numbers2 =  pd.read_csv('phone-number-2.csv', names=['phone-numbers'])

#Concat the files by creating a list of the files together.
df_concat = pd.concat([df_phone_numbers, df_phone_numbers2], axis=0, ignore_index=True)
#print(df_concat)
df_concat.to_csv('phone-concat.csv', index=False, header=False)
```

---
## Remove Dups within columns and Drop NaN Values

Drop NaN values or Null values false is non null values and true is null
	print(df_concat[df_concat['phone-number'].isnull()])

![Pasted image 20230731202538.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230731202538.png)

There are no null values as seen above  in order to drop the null values, but if they were you could check also by using the len function.

	print(len(df_concat[df_concat['phone-number'].isnull()]))


```python
import pandas as pd

# Remove Duplicates and NaN values Within a Column
df_concat = pd.read_csv('phone-concat.csv', names=['phone-number'])
#print(df_concat)

# Number of duplicated elements select true values inside the array usinng the first df_concat can select len as well to see how many dup elements
#print(len(df_concat[df_concat['phone-number'].duplicated()]))

# Drop duplicates and export it to csv
# Option 1 we only create a copy if we dont set the df_contact which overwrites the content to the dataframe
df_concat = df_concat.drop_duplicates(['phone-number'])
print(df_concat)
#out to csv and name it and have no index
#df_concat.to.csv('no-duplicates.csv', index=False)

# Option 2 if we use inplcae = true it wont be a copy and chages can be made you dont have to it as variable.
df_concat.drop_duplicates(['phone-number'], inplace=True)
#print(df_concat)
# Drop NaN values or Null values false is non null values and true is null can also check using len
#print(df_concat[df_concat['phone-number'].isnull()])

#print(df_concat[df_concat['phone-number'].isnull()])
#df_concat.dropna(subset=['phone-number'], inplace=True)
#print(df_concat)

# Fill NaN values you can use fillna to fill any value in null columns neet to overwrite content by setting to dataframe right change is seen on the left.
df_concat['phone-number'] = df_concat['phone-number'].fillna('-1')
#print(df_concat)
```
