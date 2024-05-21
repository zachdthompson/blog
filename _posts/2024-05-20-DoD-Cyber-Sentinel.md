---
layout: post
title: 2024 DoD Cyber Sentinel CTF
---

# Challenge Overview

Last Saturday, I had the opportunity to compete in the 2024 Cyber Sentinel challenge, hosted by the DoD. There were over $15k in cash prizes, plus the ability to be considered for a full time job. From number on slack, there were nearly 3000 competitors. I ranked 119th with 1125 points, which puts me in roughly the top 3%, which I am really proud of considering it was my first solo CTF.

Below are the challenges I solved, plus how I solved them. I learned a lot in these challenges, and I am excited to share my progress!

<details>
<summary>OSINT - Have You Bean Here Before?</summary>

# Summary

This challenge gave you a photo, as seen below, and asked to find the MAC address of the WiFi the user was connected to.

<br>

<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/OSINT_bean.png" alt="Bean Challenge"/>

<br>

# Solution

The first step in solving this OSINT challenge is to identify the location of the photo. The first thing I did was look for identifying information in the photo. Looking at the coffee cup, we can see a logo "PAUL" on the side facing us. 

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Paul.png" alt="Paul Cup"/>
<br>

Using Google, I simply searched for "Paul Restaurant", which the first result was the website for [Paul French Bakery & Cafe](https://www.pauldmv.com/). Navigating to the Locations Tab, we can see the following locations:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Locations.png" alt="Paul Locations"/>
<br>

I then did research on each location, looking around on Google Street View until I found [this view of the restaurant](https://www.google.com/maps/place/PAUL/@38.9026998,-77.0296509,3a,75y,244.78h,79.07t/data=!3m6!1e1!3m4!1st0gdbpUCz3dH3h2pBbWo3w!2e0!7i16384!8i8192!4m15!1m8!3m7!1s0x89b7b7945ffccc1d:0xa31a1c637f46a13f!2s1275+K+St+NW,+1275+K+St+NW,+Washington,+DC+20005!3b1!8m2!3d38.9028463!4d-77.0292336!16s%2Fg%2F12hlgng30!3m5!1s0x89b7b7945ff74921:0x2b9bb7d6fb61081a!8m2!3d38.9027468!4d-77.0293581!16s%2Fg%2F11bwt9_wgs?entry=ttu). This narrowed it down to the location at 1275 K Street in Washington DC. The view looked identical to the one in the image, as you could see the building in the background and it matched the skyline from the original image. 

Now that we had the street location, we needed to figure out how to track down what the BSSID of the AP to solve the challenge. I had no idea how to do this, so I just googled "WiFi Map Online" and [a site called Wiggle](https://wigle.net/) showed up. This was arguably the coolest part of the challenge for me, as I learned about this site's feature to collect and display beacon frames from the general public. Using this site, I navigated to the address of the location I chose and I saw quite a few SSIDs available to look at:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Wiggle.png" alt="Wiggle Results"/>
<br>

I noticed that one of the options available was "Paul Guest" which I assumed the target would be using as he was a guest at this restaurant. Clicking on that option, I saw the following information:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Paul_Guest.png" alt="Paul Guest WiFi"/>
<br>

This BSSID was the correct one, giving me the flag of

**C1{6C:CD:D6:BD:5B:51}**

</details>

<details>
<summary>Forensics - Exfil</summary>

# Summary

This challenge gave you a wireshark packet capture, and you had to deduce what the flag was from there. Below is a link to the file:

![]({{site.url}}/assets/files/CTF/Sentinel2024/exfil/exfiltrated.pcap)

Opening the packet capture, I could immediately see it was purely DNS traffic. It looked like the address came from an internal 192.168.0.0/16 private address range, pointing to google's DNS server (8.8.8.8). Below is a picture of what that looked like:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/exfil/Unfiltered.png" alt="Unfiltered Packet"/>
<br>

Looking at the requests, I was able to see that it was a seemingly random 31 character subdomain added to data.exfiltrated.com:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/exfil/Request_Details.png" alt="Request Details"/>
<br>

I knew there was a data exfiltration method that involved sending data over arbitrary DNS requests, which would bypass many firewall rules. Most firewalls allow for the client to make DNS requests, otherwise their network would not function properly. What you can do is hide data in the requests, and program a server to listen for these requests and parse them back into the file. 

I ran with this hunch, and I filtered the Wireshark capture with the following filter:

```dns.qry.name contains "data.exfiltrated.com" && ip.dst == 8.8.8.8```

This resulted in just the outbound requests, in sequential order, made to Google's DNS:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/exfil/Filter_Applied.png" alt="Filter Applied"/>
<br>

I exported these results out into their own packet capture file. I did this so that I could work just with the data I wanted and I didnt have to add filters into my Python code.

After that, I ran the following Python script on the file:

```python
import pyshark
import os
import base64

# Create file paths for the input and output
cwd = os.getcwd()
file = os.path.join(cwd, 'filtered.pcap')
output_file = os.path.join(cwd, 'output.txt')

# Load the packet capture
cap = pyshark.FileCapture(file)
data = str()

# Loop through each packet, grab the query itself
for pkt in cap:
    query = pkt.layers[3].qry_name

    # remove the .data.exfiltrated.com from the packet
    query = query.replace('.data.exfiltrated.com', '')
    data += query

# Write the output to a file
with open (output_file, 'w+') as output:
    output.write(data)
```

This resulted in several thousand characters of what was very clearly Base64 encoded data. I loaded this data into [Cyber Chef](https://cyberchef.org/). Cyber Chef has the wonderful magic wand, which was able to automatically detect that it was an image that had been encoded in Base64:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/exfil/Cyber_Chef.png" alt="Cyber Chef"/>
<br>

This resulted in the decoded image being displayed:

<br>
<img class="center" src="{{site.url}}/assets/images/CTF/Sentinel2024/exfil/Flag.png" alt="Flag"/>
<br>

This image, as you can see, contains the flag:

**C1{dns_3xfil7r4t3d!}**

</details>