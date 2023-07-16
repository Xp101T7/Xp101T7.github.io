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

[Regular Expressions Cheat Sheet DataCamp](https://www.datacamp.com/cheat-sheet/regular-expresso)

[Regex101](https://regex101.com/)

[regexr](https://regexr.com/)

🥇[Python Regular Expression Cheatsheet - Debuggex](https://www.debuggex.com/cheatsheet/regex/python)

🥇[Regex Pal](https://www.regexpal.com/)  Top Regular Expressions found on RegEx Pal

[Url Validation Regex | Regular Expression - Taha](https://www.regexpal.com/94502)  
[date format (yyyy-mm-dd)](https://www.regexpal.com/96683)  
[nginx test](https://www.regexpal.com/94055)  
[Extract String Between Two STRINGS](https://www.regexpal.com/96872)  
[match whole word](https://www.regexpal.com/95367)  
[special characters check](https://www.regexpal.com/99810)  
[Match anything enclosed by square brackets.](https://www.regexpal.com/97589)  
[Find Substring within a string that begins and ends with paranthesis](https://www.regexpal.com/94730)  
[Simple date dd/mm/yyyy](https://www.regexpal.com/99555)  
[Blocking site with unblocked games](https://www.regexpal.com/94148)  
[Match if doesn't start with string](https://www.regexpal.com/95226)  
[RegEx for Json](https://www.regexpal.com/95560)  
[Match anything after the specified](https://www.regexpal.com/95901)  
[all except word](https://www.regexpal.com/94017)  
[Java Variable](https://www.regexpal.com/97778)  
[10-digit phone number with hyphens](https://www.regexpal.com/94189)  
[Find any word in a list of words](https://www.regexpal.com/93690)  
[Regular Expression For Decimal Validation | Taha](https://www.regexpal.com/95625)

[powergrep](https://www.regular-expressions.info/powergrep.html)



![[https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Regular_Expressions_Cheat_Sheet.pdf]]

## Regex examples[#](https://www.keycdn.com/support/regex-cheat-sheet#regex-examples)

With the regex cheat sheet above, you can dissect and verify **what each token within a regex expression actually does**. However, you may still be a little confused as to how to put these tokens together to create an expression for a particular purpose. The following section contains a couple of examples that show how you can use regex to match a given string.

##  1. Matching an email address[#](https://www.keycdn.com/support/regex-cheat-sheet#1-matching-an-email-address)

To match a particular email address with regex we need to utilize various tokens. The following regex snippet will match a commonly formatted email address.

```none
/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,5})$/
```

The first part of the above regex expression uses an `^` to start the string. Then the expression is broken into three separate groups.

- **Group 1** `([a-z0-9_\.-]+)` - In this section of the expression, we match one or more lowercase letters between a-z, numbers between 0-9, underscores, periods, and hyphens. The expression is then followed by an `@` sign.
- **Group 2** `([\da-z\.-]+)` - Next, the domain name must be matched which can use one or more digits, letters between a-z, periods, and hyphens. The domain name is then followed by a period `\.`.
- **Group 3** `([a-z\.]{2,5})` - Lastly, the third group matches the top level domain. This section looks for any group of letters or dots that are 2-5 characters long. This can also account for region-specific top level domains.

Therefore, with the regex expression above you can match many of the commonly used emails such as `firstname.lastname@domain.com` for example.

### 2. Matching a phone number[#](https://www.keycdn.com/support/regex-cheat-sheet#2-matching-a-phone-number)

To use regex in order to search for a particular phone number we can use the following expression.

```none
/^\b\d{3}[-.]?\d{3}[-.]?\d{4}\b$/
```

This expression is somewhat similar to the email example above as it is broken into 3 separate sections. Once again, to start off the expression, we begin with `^.`

- **Section 1** `\b\d{3}` - This section begins with a word boundary to tell regex to match the alpha-numeric characters. It then matches 3 of any digit between 0-9 followed by either a hyphen, a period, or nothing `[-.]?`.
- **Section 2** `\d{3}` - The second section is quite similar to the first section, it matches 3 digits between 0-9 followed by another hyphen, period, or nothing `[-.]?`.
- **Section 3** `\d{4}\b` - Lastly, this section is slightly different in that it matches 4 digits instead of three. The word boundary assertion is also used at the end of the expression. Finally, the end of the string is defined by the `$`.

Therefore, with the above regex expression for finding phone numbers, it would identify a number in the format of 123-123-1234, 123.123.1234, or 1231231234.

Use Faker to create some dummy data to regex parse or use [Mockaroo](https://www.mockaroo.com/)


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


