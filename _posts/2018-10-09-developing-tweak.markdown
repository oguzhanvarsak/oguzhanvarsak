---
layout: post
title: Developing an iOS 11 Substrate Tweak
description: 
tags: [cydia,tweak,developer,swift,objc]
date: 2018-10-09 11:29:44 +0300
comments: true
sharing: true
published: true
img: cydia.jpeg
---

In this article, I will cover the steps needed in order to develop a Cydia Substrate jailbreak tweak.

# Background (How does it even work)

A tweak is basically a dynamic library that is being injected to specified applications (according to a predefined filter) that can actually hook some of the Obj-C \ Swift methods (or messages if you’re using Apple’s terms) after you do that, you can just choose your own implementation of the method.

 

## Tools
We will be using the following tools in order to accomplish our task.

### Frida

Before we even start hooking to methods, we need to know *what method* we want to hook to.

I like to use this neat tool called frida, it’s basically a dylib injection framework written which has a good python API

#### Installation

On your device, open up Cydia and add the following repo `https://build.frida.re`

On your Mac, install frida using python pip `pip install frida`

### Theos

Theos is a little tool which helps you with all the application creation and compilation.

You can find all you need to know about installing Theos here: https://github.com/theos/theos/wiki/Installation

After you download theos, download the appropriate private SDK into your $THEOS/sdks folder from here: https://github.com/theos/sdks



## Writing the tweak

In this tutorial, I will guide you through the creation of WhatsappReadReceiptsDisabler – a simple tweak I have written to disable read receipts in WhatsApp.

### Finding the right method

This is where the true power of Frida comes in hand.

First, we want to know the name of the process we are looking for, run

```
frida-ps -U | grep -i whatsapp
```

frida-ps is like `ps` (shows running processes), `-U` means it should this command on the connected USB device (which will be your iPhone).

Gives us this output:
```
PID Name
---- -------------------------------------------------
1667 WhatsApp
635 AppleIDAuthAgent
1210 AssetCacheLocatorService
```

We can see that `WhatsApp` is the name of our app, and 1667 is the current PID

Now we will try this line:
```
frida-trace -U -m "-[* *Receipt*]"
```

Frida trace is a tool for printing calls for methods, here we use the obj-c filter of methods that contain the string `Receipt` in them (using wildcards), this will give us the following output (after getting into one of the conversations):
```
54599 ms -[WAChatViewController enqueueMessagesForSendingReadReceipts:0x1d0a41080]
54662 ms -[XMPPConnection sendReadReceiptsForMessagesIfNeeded:0x1d085d2b0]
54662 ms | -[WAMessage needsSendReadReceipt]
54662 ms | -[WAMessage needsSendReadReceipt]
54662 ms | -[WAMessage needsSendReadReceipt]
54662 ms | -[WAMessage needsSendReadReceipt]
54662 ms | -[WAMessage needsSendReadReceipt]
54662 ms | -[WAMessage needsSendReadReceipt]
54662 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[XMPPMultiReceipt addReceiptId:0x1d1243300 edit:0x0]
54663 ms -[XMPPConnection sendReadReceiptsForMessagesIfNeeded:0x1d0a41080]
54663 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[WAMessage needsSendReadReceipt]
54663 ms | -[WAMessage needsSendReadReceipt]
```

My guess is that `[XMPPConnection sendReadReceiptsForMessagesIfNeeded]` is the method we are looking for.


### Hooking

Let’s create the tweak using theos
```
$THEOS/bin/nic.pl
```

Select the appropriate settings, in the filter field we will choose:
```
net.whatsapp.WhatsApp
```

as this is the identifier of the WhatsApp application (you can find this by ssh-ing to your device and running `ps -xe`)

A folder will be created, looking inside we will find a file named​ `Tweak.xm` open it and add the following lines:
```
%hook XMPPConnection

- (void) sendReadReceiptsForMessagesIfNeeded:(id)arg {
UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"ROFL"
message:@"Dee dee doo doo."
delegate:self
cancelButtonTitle:@"OK"
otherButtonTitles:nil];
[alert show];
%orig;
}

%end
```

This will cause the method to open an alert view each time the method is being called so we will know we got the right method.

Build the package
```
make package
```

And deploy to device:
```
THEOS_DEVICE_IP=[your device ip] make install
```

after doing so, we can just remove the code from inside the method, this will make the method do nothing (and not send the read receipts).

___

Download Tweak : https://github.com/oguzhanvarsak/whatsappTweak
