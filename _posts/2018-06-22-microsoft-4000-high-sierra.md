---
layout: post
title:  Microsoft Keyboard on OSX High Sierra
tags:   productivity keyboard
---

<img src="https://compass-ssl.microsoft.com/assets/b1/12/b112ddfc-0541-4948-a804-a7268b0cc2a7.jpg?n=mk_nek4000v2_large.jpg">

[Microsoft Natural Ergonomic Keyboard 4000](https://www.microsoft.com/accessories/en-us/products/keyboards/natural-ergonomic-keyboard-4000/b2m-00012) was one of the best selling ergonomic keyboards for its generation. I wanted to start using my keyboard on a new MBP with High Sierra installed. It is a wired keyboard and getting the keyboard connected with a dongle is a pain. Even though the keyboard was detected and identified none of the special keys worked as you would expect. Some googling led me do the project [Karabiner](https://pqrs.org/osx/karabiner/) which works! But this was only after some additional steps which I'll document here.

## Karabiner Installation

Download Karabiner Elements from the [site](https://pqrs.org/osx/karabiner/). After setup I ran into a permissions failure.  In System Preferences > Security and Privacy, click "Allow" for the "System software from ...." as shown below-

<img src="{{ site.url }}{{ site.baseurl }}/imgs/karabiner-setup.png">

Two applications get installed -
* Karabiner Elements for setting up the keyboard configuration
* Karabiner Event Viewer for debugging key presses.

## Keyboard setup

The basic setup needed for using the natural keyboard was-
* Remap the left alt key as left Command
* Remap the windows key as left Option

The following settings in the Karabiner Elements preferences achieved those basics-

<img src="{{ site.url }}{{ site.baseurl }}/imgs/karabiner.png">

I just did the settings for the specific target device. Also the From keys are left_alt and left_gui which confused me initially.

## Debugging keystrokes

Karabiner Event Viewer is a utility app that is helpful for debugging keystrokes.

<img src="{{ site.url }}{{ site.baseurl }}/imgs/karabiner-element-viewer.png">

Just by pressing any key in the Main tab, it prints out the key name. One nifty integration is the "Add name to Karabiner Elements" button in the same UI for easier configuration. I would have taken a long time to map the keystroke to the name without this tool.
