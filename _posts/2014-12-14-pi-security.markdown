---
layout: post
title:  "Raspberry Pi meet Security system"
date:   2014-12-14
summary: More dream ideas
---

I have a new security system for my home. It consists of various sensors entry/exit and motion to detect a break-in. I want to gather information about the events for building custom alerts and logging for future data analysis. However there is no interface to the security system for obvious reasons.

Enter the new dream project... 

The security system says specific sentences on some events and has some beep sequences for other states. I need to get a device that can be listening all the time and then on certain sound events does things. This does not need to be super complicated and parse random sound files. If possible all that is needed is detection of known sound samples after removal of noise. Then a state machine needs to be programmed to model the mirror the security system with modes of off, home and away modes.

A friend has recently built a neat Raspberry Pi system to activate lights based on motion detection. So I can get some help understanding the basics. After that need to find the right sensor and program away. Seems like a fun project for some day.

*PS: Funny for once listening does not mean listening on a server socket but actually listening for sound.*

*PPS: Funnier that my wife would want my default understanding of listening to really mean listening and not socket communication.*
