---
layout: post
title:  "Replacing RK71 battery"
date:   2026-05-17 2:13:00 +0800
categories: keyboard
---

#### [Jump to battery mod](#battery)

![image of rk71 with a external battery pack](/images/rk71/kbwithextbattery.webp)

This is my RK71, technically my first mechanical keyboard that I bought. I was using a RK61 that my brother gave me (he sort of fucked up the board when desoldering switches but i ran a couple of wires and make it work again) it was fine ig ? they are cheap and works well. The reason why i consider getting RK71 over other models at that time as they are much cheaper than others, has hotswappable keys, 2.4g, bluetooth and build in battery.

At that time i was playing with raspberry pi so i really looking to get a keyboard with either usb or a usb dongle which this has both. I really enjoyed using it since it has a full arrow key instead of the weird ass arrow key on 60% keyboard and a additional function row (delete insert etc etc) which although i don't use it often but it's really nice to have when you need it (inkscape moving layers up/down and vsc going from front of the line to end).

as you can see above the image, it has a external battery attached to it because the battery is dead. Like the battery swollen to the point where it is no longer safe to use. 

![image of keyboard bulging](/images/rk71/battery%20pusing%20against%20kb.webp)

> as you can see there is a section is bulging out

![image of battery swollen](/images/rk71/puffed%20up%20battery.webp)

this was like 3 years ago, at that point i had removed the battery and use it in wire mode

after a week the battery has swollen to the point where it practically becomes a cylinder, yikes!

(i've dispose the battery at that point)

soo tried my best to get the battery specification (my attempts here)

![image of battery dimension?](/images/rk71/battery%20dimensions.webp)

still can't find a replacement online :/

i already given up by then and build a external battery pack using whatever parts i have.

fast forward to last year -ish saw a 60% keyboard with identical feature set and with a full arrow key instead of others fuckass layout! it was on sale and i bought it and hey the battery is 2000mah which is a upgrade as well. In the few weeks i was using i liked it but it started to give me problem and i returned it and got a replacement and the same shit happened.

(not gonna go in depth here)

> sigh- looks like i can't find a good replacement for it after all

so i went back searching for a battery replacement for it and that's when i saw this comment on reddit

![image eof reddit comment](/images/rk71/reddit%20comment.webp)

They even attached a image of how they did as well!

![image of battery mod](/images/rk71/attatched%20image.webp)

> u/_Sigrunn

iPhone 6 battery isn't expensive and i can get it locally as well.

# Teardown

There are 6 screw hiding under the space bar, between the windows key and alt key, the tab key, q and y key, right shift key and between the delete and \ key.

once you remove you can pull the top out.

then there is just 5 more screw and you can pull the pcb out if you want to.

![image of pcb](/images/rk71/pcb.webp)

by some dumb luck, i somehow found a connector that fits in in my junk box.

![image of connector](/images/rk71/battery%20connector.webp)

the main ic is BYK916 which from this [blog post](https://carlossless.io/nuphy-air60/) is a relabeled Sinowealth SH68F90. This basically controls the keys, rgb and usb.

The second ic on there is BK3632, based on it's product page it manages the bluetooth side and 2.4g as well.

![image of main ics](/images/rk71/main%20ic.webp)

The last ic is the charging ic it's just a TP4056

![image of charging ic](/images/rk71/charging%20ic.webp)

onto the battery

## Battery

i just got a generic af iphone 6 battery

![image of battery](/images/rk71/battery.webp)

peeling back the black tape reveal 3 pins which i guessed that one is gnd, b+ and temperature probe thingy

![image of battery pinout](/images/rk71/battery%20pinout.webp)

#### ⚠ double check with your multimeter, your pinout may differ from mine

> Be careful when handling lithium batteries, they are hard to put out if it started thermal runaway.

after **carefully** desoldering the cable with my still broken fnirsi (T~T) we can solder wires directly to the pads

![image of cable removed](/images/rk71/cable%20remove.webp)

![image of wire soldered](/images/rk71/wire%20solder.webp)

> i know it's ethernet wires, it's just what i have on hand that isn't ridiculously thin or thick.

it is a good idea to verify the connection and test it

![image of kb working with battery](/images/rk71/itworks.webp)

it works! yay!

now test the charging as well

![image of kb charging](/images/rk71/idonttrustit.webp)

(this is how little i trust it)

<p style="opacity:0;height:0; margin:0">Yes, it is in my toilet. No, im not planning to use water to put it out if anything happens. It just a place in my house that is away from any flammable objects</p>

Good news is after 10 minutes of charging, nothing happens. Yay! (～￣▽￣)～

now to get it fit into the case

## Case trimming

![image of case](/images/rk71/before%20tirm.webp)

this is where the original battery sits, we need to trim some plastic to get it fit in (highlighted in red below)

![image of area to trim](/images/rk71/plastic%20removed.webp)

but i think my battery is much fatter ? i ended up removing more

![image of after trim](/images/rk71/after%20trim.webp)

now the battery fits in nicely

![image of battery in place](/images/rk71/battery%20just%20fit.webp)

and it's a matter of reassembling it

![image of complete kb](/images/rk71/mod%20complete.webp)

and it's done and back to working! although i do have some concerns as the battery might come into contact with the pcb butttt so far it seems like it does not.

That's it, it's quite simple 

Next up! Hand wiring this piece of shit.

[img of ts]

(jk not now but someday)


# references/ links/useful stuff

[u/_Sigrunn comment](https://www.reddit.com/r/RoyalKludge/s/5AH7ciVOYs)

[BK3632 product page](https://www.bekencorp.com/en/goods/detail/cid/18.html)

[carlossless - Hacking NuPhy air60](https://carlossless.io/nuphy-air60/) - sinoweath ic as well as some reverse engineering

[carlossless - SMK](https://github.com/carlossless/smk) - BYK916/sinowealth alternative firmware

<div style="height:60vh"></div>

## Hellow! 

you are still here, if you wondering what happen to the msv that i said i was going to write last, i kind of hit a roadblock. I still testing my generated files and still coming up with new theories and stuff. Although i think it is mostly correct as my files does technically works except the ones with >99 entries. which it does not like at all, i also still in the process of writing the tools and i will probably release it with the write up as well.

replacing the battery is simple idk why the fuck it took me months to get to it. i literally complete it in like an hour or 2. Kinda sad my soldering iron is broken and i still haven't order parts yet :/. If you are wondering about the keyboard that i fucking hate it, the first one just one day unpair itself from the 2.4g dongle and will no longer connect to it. that i send back and got a replacement but it still have the same issue as the same one but not 2.4g unparing. imma just list out the problems below

- bluetooth randomly disconnect for some fucking reason
- when it goes to sleep and you wake it up, it will connect and start sending keystroke then crash and hangs for a while.
- bluetooth/usb/2.4g takes a awfully long time to connect. yes even in wired mode (usb) it takes time to "connect" (what the actual fuck)
- the timeout to sleep is so damn long (i configured it to the minium on the software already and it's 10 minutes)
- oh yeah it will also randomly crash as well
- the fucking 2.4g dongle is a chip design for a mouse and someone at my dorm somehow got a mouse that can connect to my dongle so my cursor start to move by itself
- oh you can't alt+f4 because someone decide fn+alt should put the keyboard into "test mode"

(the concept of a fucking keyboard crashing)

> stupid fucking keyboard can't even do the job it was made for

oh since i also found someone making a qmk like firmware for the chip, i probably can contribute to it as well since RK software aren't good at all especially my model is extra terrible for some reason (?) maybe someday i still need a working keyboard after all

<p style="opacity:0">don't stalk my reddit please</p>