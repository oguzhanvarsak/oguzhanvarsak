---
layout: post
title: "Monitoring iOS HTTPS Network Traffic"
date: 2018-10-22 16:17:00 +0300
description: "In an attempt to explore Apple’s Find My Friends API, I was led to monitor the network requests coming from my iPhone 6s and actually finding some rather interesting responses."
tags: [iOS,network,monitor]
comments: true
sharing: true
published: true
img: observable_001.jpg
---

In an attempt to explore Apple’s Find My Friends API, I was led to monitor the network requests coming from my iPhone 6s and actually finding some rather interesting responses.

For this guide I have used the [Charles](https://www.charlesproxy.com/) app for Mac OSX and my iPhone 6s, although this guide and the Charles app could be applied to any other OS and hardware.





## Instructions

#### Step 1: Download

Download a copy at [here](https://www.charlesproxy.com/download/) and install on your machine. Note, this app is $50 USD (includes 4 licences) but is free on a trail for 30 days.

Upon first run, you will start to see SSL requests coming in their encrypted form, we will need to perform an SSL “[man in the middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)” on ourselves to view the data unencrypted

<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/hLmicXp.png"></div>
<p style="text-align: center;">Encrypted Requests</p>

 

#### Step 2: Install Root Certificate Authority (CA) on your Mac

Go to this path:

>  Help > SSL Proxying > Save Charles Root Certificate


Double click to install the certificate, this should open “Keychain Access” then search “Charles Proxy CA”, right click on the certificate > Get Info, open the “Trust” menu and select from the dropdown “When using this certificate” to “Always Trust”.

Go back to the Charles App and go to menu

>  Proxy > SSL Proxy Settings... 


add host “*” and port “*” to the Locations list. Make sure to restart your browser so it now uses the new Root Certificate.


<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/OgIUZmG.png"></div>
<p style="text-align: center;">Tricked!</p>


This will allow you to monitor SSL requests by Charles on your machine!

<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/Aplk5uj.png"></div>


#### Step 3: Setup your Phone

You will need to send the certificate you had saved above via email to an active email account on your iOS device and be using the Apple Mail App (unfortunately, going to the recommended http://chls.pro/ssl in Safari does not install the certificate). Click on the attached certificate in the email message and this view below should appear, install the profile:


<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/pgtg3iE.png"></div>


Next, find out the IP of your machine (Will be in network settings) and make sure that both your iOS and Desktop OS Machine devices are connected to the same network then navigate to


> Settings App > Wi-Fi > WifiName Settings > HTTP Proxy > Manual


then enter Server: x.x.x.x (the local address of your machine found above) and Port: 8888


<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/4rP1QCU.png"></div>
<p style="text-align: center;">Wi-Fi Settings Page</p>


Then go back to Charles App and click “Allow” on the popup asking for permission


<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/hCsrAvC.png"></div>


You should now see requests coming in from your device!


<div style="width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://i.imgur.com/uaELYCM.png"></div>
