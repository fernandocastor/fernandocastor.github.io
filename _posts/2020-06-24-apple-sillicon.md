---
layout: post
comments: true
title:  "Quick Notes about How Apple Sillicion Impacts Swift Developers"
date:   2020-06-24 21:20:00
categories: swift
---

New Macs will use Apple-developed processors, not Intel. This new architecture has been named **Apple Sillicon**. Initial Developer Transition Kits (DTKs) are ready to be shipped to developers. They will pack the A12Z processor of the iPad Pro. Many apps are already being ported to leverage Apple Sillicon. Unity apparently has already been ported to run natively on Apple Sillicon and Microsoft's Office is on the way.

**Universal apps** One approach to support the transition from Intel to Apple Sillicon is the use of universal apps. These apps are packaged so as to include two partitions, one for each architecture, Intel and Apple Sillicon. This is similar to the old universal binaries from when Apple transitioned from PPC to Intel. Xcode will provide an *"Any Mac"* option that outputs universal apps. 

**Rosetta 2** is emulation technology that allows Intel apps to run on Apple Sillicon Macs. It even supports apps that require more direct access to hardware, for example, it translates calls to libraries such as Metal and system calls in general to native calls that leverage the Apple Sillicon hardware. Xcode still supports all the expected features for development even when using Rosetta, including lower level ones, such as debugging memory graphs. I think the most important feature of Rosetta 2 is how transparent it seems to be for developers, from both development effort and app performance. Rosetta also supports apps extensions, which run on processes different from the host process. 

**Virtualization** Apple also aims to provide *native* support for virtualization, without the need for Kernel extensions, for VMs running on Apple sillicon. The point here is not just to provide support for virtualization (there are already some VMs for Mac, such as VirtualBox), but to provide it natively, so that the performance of an application is not affected by the use of virtualization.

**iPhone, iPad apps** These apps can now run natively on Macs using Apple Sillicon, without any additional development effort. Some additional features will not be available, though, if the app is directly ported. It is possible to tailor an iOS or iPadOS app to run better on the Mac by using Mac Catalyst. Settings from the iOS/iPadOS app are automatically mapped to the application settings on the MacOS, multitasking iPad apps automatically support window resizing, and extensions to iOS apps automatically work on MacOS, just to give a few examples. There is a default mapping between touch commands and what a Mac supports.