<details>
<summary> OSINT - Have You Bean Here Before?</summary>
<img src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/OSINT_Bean.png" alt="Bean Challenge" width="200"/>
# Solution

The first step in solving this OSINT challenge is to identify the location of the photo. The first thing I did was look for identifying information in the photo. Looking at the coffee cup, we can see a logo "PAUL" on the side facing us. 

<img src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Paul.png" alt="Paul Cup" width="200"/>

Using Google, I simply searched for "Paul Restaurant", which the first result was the website for [Paul French Bakery & Cafe](https://www.pauldmv.com/). Navigating to the Locations Tab, we can see the following locations:

<img src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Locations.png" alt="Paul Locations" width="200"/>

I then did research on each location, looking around on Google Street View until I found [this view of the restaurant]([**https://www.google.com/maps/place/PAUL/@38.9026998,-77.0296509,3a,75y,244.78h,79.07t/data=!3m6!1e1!3m4!1st0gdbpUCz3dH3h2pBbWo3w!2e0!7i16384!8i8192!4m15!1m8!3m7!1s0x89b7b7945ffccc1d:0xa31a1c637f46a13f!2s1275+K+St+NW,+1275+K+St+NW,+Washington,+DC+20005!3b1!8m2!3d38.9028463!4d-77.0292336!16s%2Fg%2F12hlgng30!3m5!1s0x89b7b7945ff74921:0x2b9bb7d6fb61081a!8m2!3d38.9027468!4d-77.0293581!16s%2Fg%2F11bwt9_wgs?entry=ttu**](https://www.google.com/maps/place/PAUL/@38.9026998,-77.0296509,3a,75y,244.78h,79.07t/data=!3m6!1e1!3m4!1st0gdbpUCz3dH3h2pBbWo3w!2e0!7i16384!8i8192!4m15!1m8!3m7!1s0x89b7b7945ffccc1d:0xa31a1c637f46a13f!2s1275+K+St+NW,+1275+K+St+NW,+Washington,+DC+20005!3b1!8m2!3d38.9028463!4d-77.0292336!16s%2Fg%2F12hlgng30!3m5!1s0x89b7b7945ff74921:0x2b9bb7d6fb61081a!8m2!3d38.9027468!4d-77.0293581!16s%2Fg%2F11bwt9_wgs?entry=ttu)). This narrowed it down to the location at 1275 K Street in Washington DC. The view looked identical to the one in the image, as you could see the building in the background and it matched the skyline from the original image. 

Now that we had the street location, we needed to figure out how to track down what the BSSID of the AP to solve the challenge. I had no idea how to do this, so I just googled "WiFi Map Online" and [a site called Wiggle]([https://wigle.net/](https://wigle.net/)) showed up. This was arguably the coolest part of the challenge for me, as I learned about this site's feature to collect and display beacon frames from the general public. Using this site, I navigated to the address of the location I chose and I saw quite a few SSIDs available to look at:

<img src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Wiggle.png" alt="Wiggle Results" width="200"/>

I noticed that one of the options available was "Paul Guest" which I assumed the target would be using as he was a guest at this restaurant. Clicking on that option, I saw the following information:

<img src="{{site.url}}/assets/images/CTF/Sentinel2024/bean/Paul_Guest.png" alt="Paul Guest WiFi" width="200"/>

This BSSID was the correct one, giving me the flag of

C1{6C:CD:D6:BD:5B:51}

<details>