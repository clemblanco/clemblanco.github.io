---
layout:     post
title:      "New Titanium Alloy Widget: Selectable <ListItem> with 'Other' support"
date:       2017-09-26 15:07:32
categories: [open source]
comments:   false
---

A `Ti.UI.ListView` wrapper with mighty powers for forms in your [Appcelerator Axway](https://www.appcelerator.com/) [Titanium](http://docs.appcelerator.com/platform/latest/#!/guide/Titanium_SDK) [Alloy](http://docs.appcelerator.com/platform/latest/#!/guide/Alloy_Framework) applications.

This is a Widget wrapping some standard components in order to create a re-usable Widget for a single choice selection within a `<ListView>` element with a support for an "Other" entry.

<!--more-->

Here we are using one [`Ti.UI.ListView`](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.ListView), one [`Ti.UI.ListSection`](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.ListSection) and multiple [`Ti.UI.ListItems`](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.ListItems).

We've tried to make the API as simple and intuitive as possible but we're opened for pull requests too.

At the moment we only support a single selection mode but we could work on a multiple selection mode in the future.

Check it out on [Github](https://github.com/Cyber-Duck/alloy-select-listview) and [gitTio](http://gitt.io/component/uk.co.cyber-duck.select).
