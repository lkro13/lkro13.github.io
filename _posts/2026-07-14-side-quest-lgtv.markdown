---
layout: post
title:  "Side quest: wled on my monitor"
date:   2026-07-15 2:19:00 +0800
categories: mini-project wled
---

> This is gonna be a boring one

<p style="opacity:0; height:0">Using this as a documentation so in the future i can scream at myself for doing something</p>

Tbh this is quite simple if you know how to solder, you can do this yourself as well.

Normally i would just use a esp32/c3 boards but im looking to experiment with a wled controller you could just buy since im looking for a stable setup to be used in my future tv. (Currently the controller on my tv drops out too much and not bright at all)

The controller im using this time is from uh DOMRAEM, supports up to 24v with type c pd trigger so that's convenient

The led this time im experimenting with WS2815 since 12v, argb in single led instead of 3 in series.

installing is just a matter of cutting to length sticking down and soldering the wires

[image of completed installation]

after that you just have to configure your wled to the led amount and change the led type to WS2815 also set the current limit of your PSU (mine is 2A)

Now you have a backlight on your monitor/tv! if you want to go even further, install HyperionNG on your computer and you can sync your display to the led which makes movie watching experience even better.

No it is not distracting at all, if you configure properly it literally blends in with the surrounding and you **would** stop noticing. It literally enhance your viewing experience.

> Fun fact! the first time i tried Hyperion with my brother, we literally forgot we are suppose to test the leds and end up watching an entire show.

[7/8/2026]

Moded mine to add ir functionality to it and turns out they are so cheap they don't even bother with adding a urat to usb chip.

So yeah thankfully i got another esp32c3 and ripped the ir codes from it.

I removed the button screw terminal since im not using it and replace with a receiver. 

The 3.3v i tapped from the serial chip (if it exist)

[image of ir mod]

(ultrakill gameplay if im bored)

yes it is a ultra wide monitor, sort of regret it since i have to shrink my normal content down to a small window otherwise ow my neck. But it is so damn useful for coding, i can split my code in 3 different window and hell even with ImHex i can have 2 full window at once without sacrificing details. (Web dev html+css+js window all opened at once)

if i proceed with a custom jekyll theme, this will be under a special section instead of mixed in with the blogs. someday, maybe :P