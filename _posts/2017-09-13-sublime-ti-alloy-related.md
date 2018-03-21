---
layout:   post
title:    "Sublime Text 3 Package 'sublime-ti-alloy-related' officially released!"
date:     2017-07-13 10:26:22
comments: false
---

I've been working on a Sublime Text 3 plugin [sublime-ti-alloy-related](https://github.com/Cyber-Duck/sublime-ti-alloy-related)
in order to easily navigate around your [Appcelerator](https://www.appcelerator.com/) Titanium Alloy application source code between the controller,
style and view files and it got officially accepted to be part of the non-official (but kind of official)
Package Manager for Sublime Text, the great wbond [packagecontrol.io](https://packagecontrol.io), which
probably every single developer using Sublime Text as their IDE uses.

<!--more-->

If you don't know what [Titanium SDK](http://docs.appcelerator.com/platform/latest/#!/guide/Titanium_SDK) or [Alloy framework](http://docs.appcelerator.com/platform/latest/#!/guide/Alloy_Framework) are, it's basically one of the first technology which appeared around
2012 in order to develop truly native mobile application using JavaScript. Similar to React Native, Xamarin, NativeScript, FUSE and many more...

You can read more about this in [this old Quora post](https://www.quora.com/How-do-mobile-developers-feel-about-Titanium-Appcelerator)
where I go through a bit of explanation around that kind of technology.

To give you a little more background, I wanted to replicate the functionality of
[a plugin I was using on Atom](https://github.com/chrisgedrim/ti-alloy-related-plus) which gives the user the ability to directly open 3 types of files within
an Appcelerator Titanium project using the Alloy MVC framework using key bindings (or application menu).

The folder structure of an Alloy project is always the same regarding those files and using regular
expression you can pretty easily work out where to look for. Considering this, it was just a matter of
getting familiar with Python, because that's what Sublime is built on, and work out how to use Sublime's API
in order to do what I wanted.

Even though this is pretty simple, it's my first Sublime package developed and officially made
available [to the rest of the world](https://packagecontrol.io/packages/Titanium%20Alloy%20Related).

Pretty proud of that achievement considering I didn't know much about Python and about 50 people already use it.

Woop woop! 🤙
