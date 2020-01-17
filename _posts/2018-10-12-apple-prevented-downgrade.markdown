---
layout: post
title: How Apple Completely Prevented Users from Downgrading iOS
description: 
tags: [apple,downgrade,tweak]
date: 2018-10-12 12:35:44 +0300
comments: true
sharing: true
published: true
img: update.jpeg
---

## Part 1

Long time ago, Apple allowed firmware updates while offline, this make it impossible for them to control the firmware version on these devices.

After canceling this feature (in the early days), iOS devices will connect to an Apple server, send their device information, and through “signed” firmware, the devices receives a Digital Signature in order to upgrade their device.

The way to bypass this function is to save the Digital Signature, and replay it in the future. (aka “Saving SHSH”)

The way Apple fixed this problem is to send a random string of code called “nonce” while upgrading the device, so you couldn’t use that way to trick through the bootloader.

 

We come to the first conclusion :

- Verification logic is run by bootloader, the codes are protected by the main chip called “secure boot”, so it’s hard to change the code. (Changing the code as a “Middle man”)
- The only key is hidden in OTP, you can only USE the key but you can’t READ it, so it’s theoretically impossible to fake a client request.
- It uses asymmetric cryptography, that surely makes it’s hard to counterfeit the server’s Digital Signature…

So, forced data exchange verification + protected logic verification + protected verification key, this is currently impossible to crack.

 

## Part 2

Part 1 gives detailed information about how the code is securely processed by the system, but I’ll explain why you can’t flash the device to a specific version whenever you like to and give you an easier concept to understand how the flash process works.

A normal procedure of flashing a iOS device works like this :

- You download a firmware with the file extension .ipsw
- open up iTunes, connect your phone
- and the firmware can be easily flashed into your device.
 

In fact, during the process of flashing a device, this shows how data is being transmitted :

- .ipsw —> iTunes —> iOS’s CPU —> iOS’s Flash/eMMC
 

The key to this whole thing is that *ONLY the CPU can write the firmware into Flash/eMMC, so it all depends on if the CPU agrees you to flash the device*, if the CPU calls you fake news, the success rate of you flashing the firmware into the device is 0.00069%.

(Now people would ask question like “why don’t you just bypass the CPU and write directly into Flash/eMMC? The is because a iDevice is *COMPLETELY ENCRYPTED* (yes the whole thing), this means that everything (data) that’s going towards Flash/eMMC HAS to be encrypted. This encryption key is written *INSIDE* the CPU, only the CPU knows the key, and every device has an different key.)

(So without the key, you wont be able to write the correct data to Flash/eMMC, even soldering off the chips itself (pointing at Flash/eMMC) off the motherboard wont work)

So how does the CPU decide if it should flash the device? You need the *firmware verification from the Apple Server*. Supposing the firmware signature is correct, then you can flash in the firmware. So iTunes has to request the firmware signature from the Apple server and provide it to the CPU. The Apple Server will check the firmware’s authenticity and firmware version to decide if it should provide the Digital Signature. So, *ONLY* the Apple server has the power to flash the firmware


You can think if it this way (Role-Play) :

> iTunes: I wanna flash a device using this firmware

> CPU: you need to provide a verified signature that matches this firmware

(iTunes asks the Apple server for the signature)

> iTunes: here’s the signature

> CPU: this signature is real! This firmware can be used to flash this device

 

If Apple stops signing this firmware, it’ll look like this :

> iTunes: I wanna flash a device using this firmware

> CPU: you need to provide a verified signature that matches this firmware

(iTunes asks the Apple server for the signature)

> Apple Server: this firmware is unsigned, I can’t provide you a signature.

 

(Digital Signature uses asymmetric cryptography, meaning it’s impossible to counterfeit a signature)

 

But even tho Digital Signature can’t be counterfeited, you can keep it and use it when you need to.

Few years ago you could use SHSH to flash in the firmware is also using this principle.

 

Think of it this way :

> iTunes: I wanna flash a device using this firmware

> CPU: you need to provide a verified signature that matches this firmware

(iTunes takes out the Digital Signature it collected a long time ago from your computer)

> iTunes: here’s the signature

> CPU: this signature is real! This firmware can be used to flash this device

 

(In reality, the tools you use for blobs/shsh would need to create a fake server to iTunes)

 

This replay attack is really easy to be prevented, now SHSH no longer works anymore.

 

Think of it this way :

> iTunes: I wanna flash a device using this firmware

> CPU: you need to provide a verified signature, and that signature needs to include some random generated Digits/Number as follow “GuvfNppbhagJnfOnaarqSbeZrzrf,……(lbhgh.or/5-I-HvrRhMV)”; In this old Digital Signature, these random digits “GuvfNppbhagJnfOnaarqSbeZrzrf,……(lbhgh.or/5-I-HvrRhMV)” isn’t included, so this Digital Signature is invalid

 

You can counterfeit the verification server, but the results of the verification cannot be counterfeited, you can only intercept the real server’s Digital Signature and replay it to the CPU, and that’s what made the Digital Signature so powerful.

 

It wasn’t completely blocked in the good old dayz, the first iPhone and iPod Touch could be flashed anytime, iPod Touch 2nd generation can be downgraded to 2.x, iPhone 3GS, iPod Touch 3rd generation can be directly downgraded to iOS 4.1.

Speaking of how they restrict custom flashing, it’s just that the bootloader doesn’t have the exploits anymore (patched) to counterfeit the firmware; and server verification flashing plus disk partition hashing and Digital Signature, adding up A5 chip and above added the nonce to prevent apps like TinyUmbrella.
