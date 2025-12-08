---
layout: post
title: "Nuphy Air 75 V3 - Configuring on Linux"
date: 2025-12-08
---

This will be a very short post to put something on the internet that I couldn't easily find with Google.

I recently got the [Nuphy Air 75 V3](https://nuphy.com/collections/keyboards/products/nuphy-air75-v3-page) mechanical keyboard. It's great so far - from the 12 hours I've had it with me so far :)

The one problem I faced was trying to use the [Nuphy.io](https://nuphy.io) configurator to change the keybindings. It worked on my Mac, but on my Linux (Omarchy btw :) ) it just failed with a confusing error about permissions. It showed up in the list of devices on Chrome when I tried to connect to it, but it just wouldn't connect.

The solution is to create a `udev` rule that does *something* to allow the keyboard to be connected to from Chrome somehow. I have ZERO knowledge of the details, just that it works.

I created `/etc/udev/rules.d/50-nuphy.rules` and put the following content in it.

```
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="19f5", ATTR{idProduct}=="1028", MODE="0666"
KERNEL=="hidraw*", ATTRS{idVendor}=="19f5", ATTRS{idProduct}=="1028", MODE="0666"
```
```
```


For other Nuphy keyboards, the vendor id would stay `19f5`, but the product id will change. Use `lsusb` to find yours. On Omarchy I use the USBView application since `lsusb` isn't installed by default.

Hope this shows up in the Google search for the next person facing this issue and helps them.
