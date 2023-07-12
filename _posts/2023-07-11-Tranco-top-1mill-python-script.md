---
title: Tranco Top 1 Million Python Script for SIEM Enrichment
date: 2023-07-11 12:00:00 -500
categories: [SIEM,Enrichment]
tags: [SIEM,Logs,Enrichment,Tranco]


---

> Knowledge without justice ought to be called cunning rather than wisdom.
> — <cite>Plato</cite>

---

### Tranco Top 1 Million Python Script for SIEM Enrichment


Sign up for an account 
[Your account - Tranco](https://tranco-list.eu/account)   

Check out this article on why Tranco is boss. 
[Why Tranco's Boss](https://tranco-list.eu/assets/tranco-ndss19.pdf)

Take your API key and your email and plug it in to the code below.

Create a cron job or a scheduled task then send the data to your SIEM. 

```python 
# Import necessary modules
import requests
from requests.auth import HTTPBasicAuth
import time
from datetime import datetime, timedelta

# Function to create and download list from Tranco
def create_and_download_list(username,  password):
    # Calculate the start and end dates for the last 30 days
    end_date = datetime.now() - timedelta(days=1)  # Yesterday
    start_date = end_date - timedelta(days=30)  # 30 days ago
    # Configuration for the list to be created
    configuration = {
        "providers": ["alexa", "majestic", "umbrella"],
        "startDate": start_date.strftime("%Y-%m-%d"),
        "endDate": end_date.strftime("%Y-%m-%d"),
        "combinationMethod": "dowdall",
        "listPrefix": "full",
        "filterPLD": "on"
    }
    # URL for creating a new list
    url = "https://tranco-list.eu/api/lists/create"
    headers = {'Content-Type': 'application/json'}
    auth = HTTPBasicAuth(username,  password)  # Basic authentication with username a password API key
    # Send a PUT request to create a new list
    response = requests.put(url, headers=headers, auth=auth, json=configuration)
    # If the list is created successfully
    if response.status_code == 200:
        list_id = response.json()['list_id']
        print(f"List created. ID: {list_id}")
        # Continuously check if the list is available
        while True:
            url = f"https://tranco-list.eu/api/lists/id/{list_id}"
            response = requests.get(url, auth=auth)
            # If the list is available
            if response.status_code == 200 and response.json()['available']:
                download_url = response.json()['download']
                download_response = requests.get(download_url)
                # If the list is downloaded successfully
                if download_response.status_code == 200:
                    # Convert the list to CSV
                    lines = download_response.text.split('\n')
                    with open(f'{list_id}.csv', 'w', newline='') as f:
                        f.write('rank,domain\n')  # Write the headers
                        for line in lines[:1000000]:  # Only write the first 1 million lines
                            rank, domain = line.split(',', 1)
                            f.write(f'{rank},{domain}\n')
                    print(f"List downloaded. File: {list_id}.csv")
                    break
                else:
                    print(f"Failed to download list. Status code: {download_response.status_code}")
                    break
            else:
                print("List not available yet. Waiting for 5 seconds.")
                time.sleep(5)
    else:
        print(f"Failed to create list. Status code: {response.status_code}")
        
# Your username a password API key as username and password
username = 'supersecret@gmail.com'
password = 'Get_your_own_token'

# Call the function to create and download the list
create_and_download_list(username,  password)

```

Prepare your CSV file: Ensure your CSV file is properly formatted according to SIEM's requirements. Typically, the first row should contain the column names, and the following rows should contain the data. Make sure your data is clean and correctly formatted.

Configure your SIEM Relay: You need to configure your SIEM Relay to accept the data. This typically involves setting up the appropriate tags and rules on the Relay to correctly process the incoming data.

Send the data to the SIEM Relay: You can send the data to the SIEM Relay using a variety of methods, such as a syslog sender, a log agent, or a custom script. The exact method will depend on your specific requirements and environment.

Verify the data in SIEM: Once the data has been sent to the SIEM Relay, it should be forwarded to your SIEM account. You can verify this by checking the data in your SIEM account.

Create your lookup table in SIEM: Once the data is in SIEM, you can create a lookup table from it. Go to the "Data Search" section, select "Lookup Tables" from the drop-down menu, and follow the prompts to create a new lookup table using the data you sent to the SIEM Relay.


<span style="color:transparent; text-decoration:none">[](https://chat.openai.com/share/db63415c-48a1-421c-b88f-f0412064fe6c)</span>

