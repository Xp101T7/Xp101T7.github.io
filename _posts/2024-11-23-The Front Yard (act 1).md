---
title: The Front Yard (Act 1)
date: 2024-11-23 12:00:00 -500
categories: [HHC24,CTF]
tags: [CTF,SANS ,HHC24,HHC21]

---

> [!quote] An optimist is a person who sees a green light everywhere, while the pessimist sees only the red spotlight... The truly wise person is colour-blind.
> — Albert Schweitzer

---

## The Front Yard (Act 1)

![[Pasted image 20241123183522.png]]

![[Pasted image 20241124180013.png]]

## North Pole Monitoring Station 

![[Pasted image 20241124180122.png]]




## cURLing

![[Pasted image 20241123180221.png]]![[Pasted image 20241123180257.png]]

![[Pasted image 20241123180424.png]]

The official [cURL man page](https://curl.se/docs/manpage.html) has tons of useful information on how to use cURL.

![[Pasted image 20241123180437.png]]
![[Pasted image 20241123180528.png]]

```json
curl http://curlingfun:8080/
```


![[Pasted image 20241123180708.png]]

```json
curl -k https://curlingfun:9090/
```

You have successfully bypassed the self-signed certificate warning!
Subsequent requests will continue to require "--insecure", or "-k" for short.

If you need help, please remember to run "hint" for a hint!


![[Pasted image 20241123180933.png]]

```json
curl -k -X POST https://curlingfun:9090/ -d "skip=alabaster"
```

![[Pasted image 20241123181046.png]]

Working with APIs and embedded devices often requires maintaining session state by passing a cookie.  Use curl to send a request to https://curlingfun:9090/ with a cookie called "end" with the value "3", indicating we're on the third end of the curling match.

5) Working with APIs and embedded devices sometimes requires working with raw HTTP headers.  Use curl to view the HTTP headers returned by a request to https://curlingfun:9090/

Viewing HTTP Headers with curl

To view the HTTP headers returned by a request using `curl`, use the `-I` option to fetch only the headers or the `-v` option for detailed output.

Example Command:

```bash

curl -k -I https://curlingfun:9090/

```

Explanation:

- `-k`: Ignores certificate validation errors (for self-signed certificates).
- `-I`: Fetches only the HTTP headers, without the response body.

If you want both headers and the response body, use the `-v` (verbose) option:

```bash
curl -k -v https://curlingfun:9090/
```
### Additional Notes:

- The `-v` option provides more detailed information, including both the request headers sent and the response headers received.

6) Working with APIs and embedded devices sometimes requires working with custom HTTP headers.  Use curl to send a request to https://curlingfun:9090/ with an HTTP header called "Stone" and the value "Granite".

```json
curl -k -H "Stone: Granite" https://curlingfun:9090/`
```
### Explanation:

- `-k`: Ignores certificate validation errors for self-signed certificates.
- `-H "Stone: Granite"`: Adds a custom HTTP header named `Stone` with the value `Granite` to the request.

7) curl will modify your URL unless you tell it not to.  For example, use curl to retrieve the following URL containing special characters: https://curlingfun:9090/../../etc/hacks


To prevent `curl` from modifying your URL, you can use the `--path-as-is` option, which tells `curl` to keep the URL path exactly as you provided it, including special characters.

### Command:

```shell
curl -k --path-as-is https://curlingfun:9090/../../etc/hacks`
```
### Explanation:

- `-k`: Ignores certificate validation errors (for self-signed certificates).
- `--path-as-is`: Ensures `curl` does not normalize or modify the URL path, allowing it to include special characters like `..`.

Great work! 
Once HHC grants your achievement, you may close this terminal.


## Tinsel Upatree

![[Pasted image 20241123182900.png]]


## Wombley Cube

Just told me to go play with cURL with bow while he makes preparations for deliveries from the naughty and nice list.

## Morcel Nougat


![[Pasted image 20241123183309.png]]

![[Pasted image 20241123183327.png]]
![[Pasted image 20241123183350.png]]
![[Pasted image 20241123183403.png]]

![[Pasted image 20241123183436.png]]
![[Pasted image 20241123183444.png]]
![[Pasted image 20241123183454.png]]


![[Pasted image 20241123183548.png]]

Found Frosties book now i need to find the uv light

![[Pasted image 20241123183657.png]]

![[Pasted image 20241123183805.png]]

![[Pasted image 20241123183813.png]]

https://www.boxentriq.com/code-breaking/book-cipher

![[Pasted image 20241123185940.png]]

![[Pasted image 20241123185945.png]]

![[Pasted image 20241123190006.png]]

![[Pasted image 20241123190258.png]]

Download burpsuite

Use the built in browser in burpsuite

Go to Proxy history find the request to submit payloads to the server. Send that request to the intruder. 

Configure the intruder as follows

![[Pasted image 20241124163152.png]]

![[Pasted image 20241124163224.png]]


![[Pasted image 20241124173402.png]]

The first pass we cracked was picked up. 

![[Pasted image 20241124175312.png]]

Send to organizer

![[Pasted image 20241124175405.png]]

![[Pasted image 20241124175517.png]]





---


## Decoding the Shredded Paper

```python
import os
import numpy as np
from PIL import Image

def load_images(folder):
    images = []
    filenames = sorted(os.listdir(folder))
    for filename in filenames:
        if filename.endswith('.png') or filename.endswith('.jpg'):
            img = Image.open(os.path.join(folder, filename)).convert('RGB')
            images.append(np.array(img))
    return images

def calculate_difference(slice1, slice2):
    # Calculate the sum of squared differences between the right edge of slice1 and the left edge of slice2
    return np.sum((slice1[:, -1] - slice2[:, 0]) ** 2)

def find_best_match(slices):
    n = len(slices)
    matched_slices = [slices[0]]
    slices.pop(0)

    while slices:
        last_slice = matched_slices[-1]
        differences = [calculate_difference(last_slice, s) for s in slices]
        best_match_index = np.argmin(differences)
        matched_slices.append(slices.pop(best_match_index))

    return matched_slices

def save_image(images, output_path):
    heights, widths, _ = zip(*(i.shape for i in images))

    total_width = sum(widths)
    max_height = max(heights)

    new_image = Image.new('RGB', (total_width, max_height))

    x_offset = 0
    for img in images:
        pil_img = Image.fromarray(img)
        new_image.paste(pil_img, (x_offset, 0))
        x_offset += pil_img.width

    new_image.save(output_path)

def main():
    input_folder = r'C:\Users\user\Downloads\shreds\slices'  # Use raw string for Windows path
    output_path = r'C:\Users\user\Downloads\shreds\assembled_image.png'  # Output file path

    slices = load_images(input_folder)
    if not slices:
        print(f"No valid image slices found in {input_folder}")
        return

    matched_slices = find_best_match(slices)
    save_image(matched_slices, output_path)
    print(f"Assembled image saved at {output_path}")

if __name__ == '__main__':
    main()
```


![[Pasted image 20241123203123.png]]

![[Pasted image 20241123203627.png]]
Baud: 112500
Parity: Even
Data: 7 bits
Stop Bits: 1 bit
Flow Control: RTS


#HHC24TODO


## Jewel Loggins

![[Pasted image 20241123190445.png]]

We have to connect the uart bridge to the terminal 

![[Pasted image 20241123190535.png]]


Power on the device

![[Pasted image 20241123190756.png]]

Plug the device into the uart bridge

Plug the cable pins from one side to the other

![[Pasted image 20241123191450.png]]

It starts smoking when you send the signal

![[Pasted image 20241123192152.png]]

The voltage had to be turned down then check....

To establish a UART connection between your USB-to-UART bridge and your device, please follow these steps:

1. **Identify Wire Functions**:
    
    - **Yellow Wire**: Transmit Data (TX)
    - **Green Wire**: Receive Data (RX)
    - **Black Wire**: Ground (GND)
    - **Red Wire**: Power (VCC)
2. **Connect to Your Device**:
    
    - **Ground (GND)**: Connect the black wire to your device's ground pin (G).
    - **Transmit (TX)**: Connect the yellow wire to your device's receive pin (R).
    - **Receive (RX)**: Connect the green wire to your device's transmit pin (T).
    - **Power (VCC)**: Connect the red wire to your device's power input pin (V), ensuring voltage compatibility to prevent damage.

This configuration aligns the transmit and receive lines correctly, facilitating proper communication between the devices. Ensure all connections are secure and that the voltage levels are compatible to prevent damage to your devices.

Nothing works for baud connection i need to figure out what these settings are.

![[Pasted image 20241123200621.png]]

Solved the paper shredder challenge

Baud: 112500
Parity: Even
Data: 7 bits
Stop Bits: 1 bit
Flow Control: RTS


![[Pasted image 20241123204129.png]]

![[Pasted image 20241123204205.png]]

![[Pasted image 20241123204222.png]]
![[Pasted image 20241123204227.png]]

![[Pasted image 20241123204237.png]]

![[Pasted image 20241123204245.png]]

![[Pasted image 20241123204331.png]]![[Pasted image 20241123204350.png]]

https://gchq.github.io/CyberChef/#recipe=HMAC(%7B'option':'UTF8','string':''%7D,'SHA256')


Got the password using history and line 11 there can just use !11 in the cmdline to run

![[Pasted image 20241123204544.png]]

Saving the options and help info for later use if necessary

![[Pasted image 20241123204940.png]]
![[Pasted image 20241123204923.png]]

```shell
!11
```

OR 

```shell
slh --passcode CandyCaneCrunch77 --set-access 1 --id 143
```

![[Pasted image 20241123204702.png]]

```shell
slh --set-access 1 --id 42 --passcode CandyCaneCrunch77
```

![[Pasted image 20241123205204.png]]

![[Pasted image 20241123205239.png]]

![[Pasted image 20241123205744.png]]

(42, 'c06018b6-5e80-4395-ab71-ae5124560189', 1, 'de206c18b0939f577bb26062292d62ad44e5d306f3e516d408520f1be2eb33ed')


```shell
ls /bin /sbin /usr/bin /usr/sbin
```

![[Pasted image 20241123211843.png]]


```shell
slh@slhconsole\> sqlite3 access_cards 
SQLite version 3.40.1 2022-12-28 14:03:47
Enter ".help" for usage hints.
sqlite> .tables
access_cards  config      
sqlite> SELECT * FROM config
   ...> ;
1|hmac_secret|9ed1515819dec61fd361d5fdabb57f41ecce1a5fe1fe263b98c0d6943b9b232e
2|hmac_message_format|{access}{uuid}
3|admin_password|3a40ae3f3fd57b2a4513cca783609589dbe51ce5e69739a33141c5717c20c9c1
4|app_version|1.0
```

![[Pasted image 20241123213234.png]]

I found the secret hours ago and then went on a wild goose chase the config was right here the whole time. 

![[Pasted image 20241123225121.png]]


