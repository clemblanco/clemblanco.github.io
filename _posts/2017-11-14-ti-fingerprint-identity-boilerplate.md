---
layout:     post
title:      "New Titanium Alloy Boilerplate App: ti-fingerprint-indentity-boilerplate"
date:       2017-11-14 15:07:32
categories: [open source]
comments:   false
---

More and more applications tend to use fingerprint technologies offered by the latest devices and
operating systems to perform authentication on mobile applications.

Appcelerator Axway Titanium module to support fingerprint technologies is quite often getting updated
and I was keeping a close eye on this while working on a specific project.

One of the main issue I face with Appcelerator Axway Titanium is the amount of scaffolding you sometimes
need to go through before producing something quite common in terms of what the market might need.
<!--more-->Here, I wanted to produce a boilerplate in order to be able to quickly get a fingerprint functionality
implemented on any project and also I wanted a common and simple API to use on top of whatever was the
underlying technology supporting the functionality. To be honest, re-usability and simplicity are
probably the most common motivations for people creating boilerplates.

Here what it looks like:

iOS Demo           |  Android Demo
:-----------------:|:-------------------------:
![](https://raw.githubusercontent.com/Cyber-Duck/ti-fingerprint-identity-boilerplate/master/docs/ios.gif)  |  ![](https://raw.githubusercontent.com/Cyber-Duck/ti-fingerprint-identity-boilerplate/master/docs/android.gif)

This is based on the official [Ti.Identity](https://github.com/appcelerator-modules/titanium-identity) module from Axway Appcelerator for the underlying interaction with the Operating System and the Titanium SDK.

Unfortunately, this module doesn't provide exactly the same functionality out of the box for Android and iOS.
iOS will provide alert dialogs for interacting with the end user during the workflows involved with Touch ID
while Android doesn't do this.
The Material Design library provides "guidelines" to implement those dialogs but
they don't come out of the box, you have to implement them yourself. Same thing for the security layer on top of this.
iOS uses the Keychain for additional security in order to store hashed keys rather than Android let you
pick the solution you want to secure the implementation.

In order to fill the gaps for Android, I've used [ti.androidfingerprintalertdialog](https://github.com/adamtarmstrong/ti.androidfingerprintalertdialog) for Android Fingerprint Dialogs which is a great little community driven open source widget.
I've also decided to use [Bencoding.Securely](https://github.com/benbahrenburg/Securely) to store login credentials securely on Android (replaces Apple KeyChain).

Check it out on [Github](https://github.com/Cyber-Duck/ti-fingerprint-identity-boilerplate)!
