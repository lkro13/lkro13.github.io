---
layout: post
title:  "Building armbian for fly mini pad"
date:   2026-10-1 9:40:00 +0800
categories: armbian mellow
---

<style>
    img{
        width:50%;
    }
</style>

**note: i have absolutly zero experiance building a os, this is just me trying to get it running**

**[Jump to OS download](#fly-armbian-download)**

> Disclaimer: LLM were used in researching and ensuring it is feasible. How it is used will be mention

I waked up in the afternoon realizing I missed my alarms and slept pass all of my classes (oopsie), I sat on my bed thinking what I should do with the extra time I have now.

Instead of cleaning my room, I stare at the fly mini pad and think _how hard is it actually to get armbian on it?_

If you have absoulty no idea what im talking, bascially fly mini pad is a linux SBC with a 3.5" touch screen on it and was design to run 3d printer firmwares like klipper and have other things like crownest, klipper screen, RRF, fluidd and moonraker. There is another 2 software on the list but i have no idea what it does, but yeah the list of preinstall software is small. But OS uses a squashFS so you cannot update, install or modify the software. You can only use the preloaded software on it.

I discovered it when i tried to install tailscale on it which now looking back, I suck at reading lmao. (It was written on the download page as a "advantage")

(I talked about it in my [mellow hate post](/2026/03/30/mellow.html#present-time))

Y'know, it would be nice if they included happy hare but nope SIU.

For the last few days, it has been extremly unstable for some odd reason might be my wifi issue since i get disconnected from my laptop.

I was thinking i probably should update it since it been months only to remember 

> right flyos-fast i can't do shit with it

alright enough back story

Before i even start i always ask myself is it worth the time and effort since there are multiple project i start only to find out it is way out of my skill set (_cough kiwi map_)

Soo i asked gemini how hard is it to do and it gave me the answer of easy to hard

Easy if it's a clone or based on a existing board. Hard if it is a custom board.

<details markdown="block">

<summary>in gemini words</summary>

---

Making an Armbian build for an Allwinner H3-based board ranges from very easy to moderately challenging, depending on whether your board is a clone/variant of an existing supported design or an entirely custom piece of hardware.

Here is what you are looking at breakdown-style:
1. If it's a clone or heavily similar to an existing board (Easy)

If your board shares its layout, PMIC (Power Management IC), RAM configuration, and GPIO layout with a well-known H3 board (like the Orange Pi PC, Orange Pi One, or NanoPi M1):

    The Process: You can often take an existing Armbian image built for a similar board, swap out or tweak the Device Tree Blob (.dtb file), and it will boot right up.

    Effort Level: Low. Mostly configuration file tweaking.

2. If it's a completely custom board (Moderate to Hard)

If you designed your own PCB or have an obscure device with unique peripheral wiring, things get trickier:

    U-Boot & Device Tree: You will need to write or adapt a custom Device Tree file (.dts) defining your specific GPIO pins, clock configurations, UART ports, and Ethernet PHY. U-Boot must also be configured to match your specific RAM timings and voltage regulators.

    The Armbian Build Framework: Armbian provides a brilliant, standardized build system (armbian/build on GitHub). Running it is automated via Docker or scripts, so compiling the actual Debian/Ubuntu userland and mainline Linux kernel is straightforward—the framework handles the heavy lifting.

    Troubleshooting: Without a working serial console (UART) wired up to debug early boot failures, you will be flying blind if it doesn't post.

Summary

If you are just adapting an existing device tree, you can do it in an afternoon. If you are bringing up a brand-new custom hardware layout from scratch, expect to spend time digging through the linux-sunxi documentation, tweaking U-Boot, and compiling device trees.

Are you working with a specific off-the-shelf device (like a TV box or clone) or designing a custom board?

---

</details>


a lot of back and forward later, digging out the schemetics (why the fuck is it black text on dark gray background and why the hell is it in a google drive???)

I went to claude and ask based on the schemetics and spec, which already existing armbian build is the closest to it

and it basically listed out all H3 based sbc :/ but **but** it does says to try with orange pi one as it the closest match (apart from wifi)

so i went to armbian flashing and flash one, pop the sdcard in, connect to a serial monitor and to my surprise, it boot up! anddd kernal panaic without any reason :/

It just says "cut here"

(according to claude that is the line begin to print dump or something)

some back and forward later with claude, it asked me to manually edit the dts file to remove the power led pin as "it is one the the gpio for dram power supply"

i did that and it does goes furthur

by furthur i meant it actually print out the kernal panic part and the end

since now i know where the device tree file lives, i got the idea to replace the file with the one from flyos fast 

> queue gemini and claude telling me why it was a terrible idea blah blah blah

i grab a new copy of flyos fast, copied the `fly-h3.dtb` and replace `sun8i-h3-orangepi-one.dtb` and it boot!

like actually boot, i can login and everything 

but the wifi, idk why it just cannot download anything for some reason.

the display obviously does not work at all but it does light up.

some more back and forward later, i discovered display is stored in the diffrent file

copied over, added it into the boot config as a overlay andd still nothing

that's where claude asked me to compile a "display firmware" based on the instruction it gave.

did that, copied over and nothing again. Then it asked me to do a modprobe command and there! i got a blinking cursor!

did some more command and i was able to get it started with the display.

![FLY MINI PAD booting with orange pi one OS](/images/mellow/image1.webp)

At this point im thinking i should start figuring out how do i build a armbian build for it.

So i asked claude, and you would not believe this. It gave me a script that does exactly what i did just now.

sigh- whats the point of building a firmware only for it to be another patch on it.

at this point it's the second day, i went to sleep and head to class the morning.

That's went i started writing this blog, recalling my steps and LLM interactions.

Im putting this out there as a proof that yes it can be done with some modification, but it need someone with armbian knowledge to make it viable for daily use.

Im not that someone, so hopefully someday, someone did that.

As i was gathering resources, (you can see below) i accidentally stumble across klipper.cn. If you google search anything about mellow, it will bring you to mellow.klipper.cn or 3dmellow.com or their github.io

But somehow i just decide to go to klipper.cn i assumed the docs is the same as i was using 

oh boy was i wrong

THEY HAVE FUCKING ARMBIAN BUILD ALL ALONG BUT HIDDEN. (also in Chinese only)

I DID ALL THAT FOR NOTHING, I DRAIN AN ENTIRE POND FOR NOTHING 

FUCK

_anyways_ i'll be leaving a link here for anyone want their board to run armbian

## Fly armbian download

### [Armbian download](https://klipper.cn/docs/down)

This is a link to their "hidden" docs

use google translate or firefox translate idk im bilingual

Keep in mind that the build is from 2024 so it's not updated in some time. 

Use rufus or armbian imager. Raspberry Pi imager might work for you but it error out for me.

## Direct url

In case they removed it from their site

There are 2 type of os, FLYOS-armbian and Armbian

FLYOS contains preinstalled tools like klipper and other stuff

Armbian only the stock Armbian image

Default account for FlyOS

username: `fly`/`root`

password: `mellow`

### Fly Pi/Pi 2/C8/Gemini

Allwinner H5 based boards

- FLYOS-Armbian
    - [Google Drive](https://drive.google.com/drive/folders/1Ok4MMgE0A6enKdkzZc9K_RQgQ8exqnG1)
    - [Baidu Netdisk](https://pan.baidu.com/s/10of5_uNGKTV2bMDT54qPVQ?pwd=ksm7) (pw if required, `ksm7`)
- Armbian
    - [Google Drive](https://drive.google.com/file/d/19KI6GjYUfn5l-z9qZhfTXpW9O5vwFFDK/view)
    - [Baidu Netdisk](https://pan.baidu.com/s/1DmcGcORQpQaihhT6rV85qw?pwd=nsk8) (pw if required, `nsk8`)

### Fly mini pad/lite 2

Allwinner H3 based board

- FLYOS-Armbian
    - [Google Drive](https://drive.google.com/drive/folders/1NzHTAfhxqVukeKuTs5agEyMwU9NLHl21)
    - [Baidu Netdisk](https://pan.baidu.com/s/1qhsjSC4ubkDox7o3o3gPIA?pwd=bfsb) (pw if required, `bfsb`)
-   Armbian
    - [Google Drive](https://drive.google.com/file/d/1gN3PQU6k3GzW6Z92pjNaokEWuU3DFrV7/view)
    - [Baidu Netdisk](https://pan.baidu.com/s/1euNAzhjEgWaQzHZTneHuTQ?pwd=kcvh) (pw if required, `kcvh`)

If the url stopped working, open an issue or contact me on discord. I have a backup on my computer.

## Resources

[schemetics](https://drive.google.com/drive/folders/1KrLeEYHHdBNVTePS2LA3RmzrAT0kQRZw)

[flyos fast](https://flyos.klipper.cn)

[flyos introduction](https://mellow.klipper.cn/en/docs/FLYOS/flyos-fast/getting-started/introduction)

[armbian build docs](https://docs.armbian.com/build-framework/getting-started/)