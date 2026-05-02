---
layout: post
title:  "Dumping and converting carrozzeria hdd"
date:   2026-04-15 11:19:00 +0800
categories: radio
---

[Jump to guides and instructions](#documentationguides)

[Jump to tools and resources](#references)




A few days ago (5 days ago to be exact), I received a carrozzeria AVIC-HRZ99 I bought it directly from Japan and got it for a really good price. Add on proxy fees, tax, shipping fees and "Custom import clearance fees" and still somehow way cheaper than local prices.

> fuck you fmx btw. it's not necessary at all but you declare as a business import when it's not so I had to pay your "agent"

I even got a iPod adapter and all the wiring harness (yay!) (nvm the ipod connector is not making good contact at all, good news is I already have another one)

Put it on the bench and everything works as it should except for the tilt which some super glue (temporary) fixed. I even got a minidisc and a burned DVD that left inside the radio lol.

> Super glue only lasted 1 week, I model a replacement which uses 2 m2 screws so hopefully it will last. might upload to printables later

## The hard drive

For those who don't know, here's a quick introduction to HDD navi radio which mostly found in Japan (I think). They are a double din touch screen radio with a HDD which stores their map data and some models (like I have) can rip CDs onto the HDD. And this model release back in 2006 (same age as I am).

Now for obvious reasons, the HDD is locked with a password if you tried to plug it in a computer. If you want to update the map data, you can either buy a upgrade DVD or send it back to Pioneer to have it update with a price of uhm 21,000 yen ????? that's expensive af. Of course the service is no longer provided so it's not like you can update it if you want to.

It's a 30GB ide toshiba hard drive

![hdd](/images/unlocking_hrz-99/hdd.webp)

### Unlocking the HDD

This isn't the first HDD radio I have, I already known it is locked with a password so tried searching online but with no luck at all. Best options I found are 

- Wire in a extension cable and keep it powered while the head unit unlock the drive and hot swap to pc
- Sniff the signal to retrieve the password
- Use a Fujitsu HDD and use a software to exploit something to get the password

So about to give up and tried searching much general

> pioneer HDD unlocking

and that's where I found avic forums
 
someone

somewhere

dumped Pioneer service CD and uploaded as a torrent and it has the ability to remove the password temporary.

Yay! but the link is dead since you need a account. Alright let's sign up for an account, and of course it need verification not a issue

![0 forwarded email](/images/unlocking_hrz-99/0forwarded.webp)

yeah i waited for an entire day and still not receive the email

(it's been almost 3 week and i still have not received it)

__fuck__

Oh someone mention they found on a specific russian tracker forum ok great, signup required -sigh again. But good news is the account system still functioning. ok found the torrent and retrieved the metadata successfully anddddd stalled.

After quite a while it did start downloading, at 4-10kb/s. Another day when by and the torrent finished downloading. Burned the cd and tried a few times to get into service mode. No idea why but it took a few tries, but it successfully entered

[28/4/2026] im suspecting the cd-rw i got is terrible quality, tbf i did bought it for ~1.2$.

![test screen](/images/unlocking_hrz-99/navi-testdisk.webp)

New issue, the buttons and touch does not work in test mode and i have to use the remote. Well mine does include the remote but the battery corroded the traces and only 4 button works which are 

- left
- right
- map zoom in/out

and i need

- left
- right
- up
- down
- enter
- current location

so i had to buy a new remote which i kinda don't want to :/ I had the idea of using a ir receiver to capture the code and see if i can figured out anything. Since im lazy to hook up a receiver and program a ir example sketch, I went to my living room and grab my esphome ir blaster which I modded from a tuya ir blaster/receiver. I press the few working buttons and to my surprise esphome supports pioneer specific protocol, which this means i don't have to figure out the ir code at all! (yay)

quickly throw up a esphome config that allows me to send any code without recompiling and went to my car and start brute forcing.

> I should clarify it is 2 days between finding the test disk and brute forcing the ir, I already install it in my car by then.

Im gonna link the code i discovered at the end

But success! i was able to unlock the drive and imaged onto my computer.

## Audio files extractions

Since it has the ability to copy the songs from a cd on to the internal drives, I would like to have a copy of the songs the last owner listen to. It is compress in ATRAC3 format which is a Sony proprietary format. 

Lucky for me, the music is store in a separate partition called MSV (or Music Server) and I did found the tracks all label TRACKXX.AT3 so i don't need to dig **too** far.

VLC and ffmpeg have ATRAC support and it did not played at all. _what do i expect_

googled did not bring much 

Gemini kept insist it's either pioneer special format or Sony drm encrypted format and hallucinated a key file. Another said it's a "zip" file ????

ChatGPT uhm yeah idk how would i describe it other than useless

Ollama forgot what i was talking about

AVIC forums so far non figured it out (at least what i could find)

Although another gemini instance did mention it's possible it's just missing a header since there is technically(?) ATRAC data and it suggest use VGMStream to play it 

> In gemini words "Good for playing unknown and broken ATRAC files" it did not work

![pioneer header](/images/unlocking_hrz-99/batch_at3data.webp)

some AVIC forums members did tried to hex edit the files but no success 

So I took the "missing" header approach and tried making my own ATRAC3 files. Found on github ATRAC reloaded and it claims it can do ATRAC3/PLUS and ATRAC9.

Throw some random audio file and open it in hex editor to grab the header part and put it in the pioneer ATRAC

ffprobe show promising result

![ffprobe_invalid](/images/unlocking_hrz-99/pprobe_invalid.webp)

but

![ffplay error](/images/unlocking_hrz-99/pplay_error.webp)

yeah it sounds like a heavily scratched disc

To make sure that it isn't the decoder issue, i checked the file that atrac reloaded gave me and... wait 

![fraud atrac3 file](/images/unlocking_hrz-99/atrac3plus.webp)

ATRAC3Plus? Apparently i can't read and made a ATRAC3+ file and idk how to make a ATRAC3 file using that software specifically.

Back to previous gemini instant for a bit, when im tying to get the file to play i asked gemini to give a list of tools and it mention use a ATRAC decoder like atracdenc and well it did not work (obviously) but it has a encode option.

So reading the man page again and found the command to encode a atrac3

```atracdenc -e atrac3 -i in.wav -o out.at3```

~~(the out .at3 must be in lower case or else it will write it in oma file for some reason ?)~~

(nvm it doesn't matter the head unit decoded it successfully anyways)

and verified it is actually atrac3 non plus, I copied over the header and 

![ffplay successfully playing file](/images/unlocking_hrz-99/ffplay_working.webp)

it uh just played ? it plays just fine? I was so for sure it has encryption using some openMG or other formats

Oh well now i have to convert all the others one

~~probably write a script to do so~~

I did write a script, it can be found [here](https://github.com/lkro13/Pioneer-AT3)

> I should clarify that i only use LLMs as a more advance search engine as google keep giving me shitty result like "pioneer unlock code (far too modern head unit like the ones with BT)", "unlock service (again wrong generation)" or "android auto plugin".

### Adding own songs

My brother asked is it possible since I figured out how to extract it, theoretically as long as it's the same bitrate and replace the header with pioneer ones it should play.

if i successfully figured out i probably add to here

[19/4/2026]

ok im still trying to figure out how to but this is just funny

so if you replace one of the track in a disc that is not yet finished recording with your own AT3 (removed and replace with pioneer header) and you insert back the same disc, it will just start playing from the HDD instead of the disc

[22/4/2026]

ok i think i got it ? although to be sure im gonna wait for my ssd adapter to arrive and try to add a completely new album directly to hdd. i did successfully change the names, track numbers, add a song to a album and change the album order.

but if you have a small music library, it might be more worth it to get some cd-rw and burn a few songs at a time for it to store into the HDD. I found the process doesn't take too long anyways, you could burn some and pop it in while on the way to somewhere. You can use imgburn to add cd text to it and it will automatically save to hdd as well. This not only ensure it will 100% work, it doesn't required creating multiple files as well as compressing all the music from wav to at3.

The issue is because he has a library of uhm, **1540** song (as of today) that's roughly 57 cds and they all need to add metadata as well.

[28/4/2026]

nvm tried to add my own from scratch and the head unit got pissed off and deleted the DB files

## Map data

The map data i think it is store in the LDATA partition with a .kwi file extension. ~~Quick google search shows that it is using kiwiGPS which idk if they still exist or not. UnfortunatelyI couldn't find any existing converter available and idk how the mapping community make their own maps so this would need someone else look into.~~

[22/4/2026]

Ok i was wrong, it is **not** kiwi gps but rather a kiwi format file. It basically is like a zip file but uh more specialize ? idk it is a open standard and like fucking hell it's stupidly well documented since 2000! Im reading the specification file and everything matched how it would be in the docs, but the issue is idk how to read this kind of files i found the offset, data length and the description of what each "header" does matched the docs. Im gonna look into it more, if you are interested in researching as well send me a message maybe we could figured out something.

# Documentation/Guides

This section is for documenting stuff and providing guide to various stuff

## HDD unlocking

Download the test disk from a (ru)ssian tracker (go to fmhy.net > torrenting and find the site there).

Use imgburn and select the .mds file as a disk image (important! some people used windows and burn the 2 files instead of burning the image file)

The disc **should** contains the following files

```
AAAAAAAA.AAA
TESTDISC.KEY
ZZZZZZZZ.ZZZ
```

put your car into accessory mode

insert the disc and wait until the device show something about "CD-ROM" if it shows some error message, remove the disc and try again until it does.

![cd-rom is inserted](/images/unlocking_hrz-99/cd-rom.webp)

wait for it to reboot and it will show test disk

![checking program](/images/unlocking_hrz-99/Layer%204.webp)

![navi test disk](/images/unlocking_hrz-99/navi-testdisk.webp)


scroll the page with the remote to some menu option called set HDD shipping flag or some sort (HDD LFG Set for international version)

> BE CAREFUL. in my model at least factory reset is directly above the option

![test menu](/images/unlocking_hrz-99/test-menu.webp)

Press enter and the "現在地" button then flip the screen down and turn off your car power. Wait for it to power down before removing it (i waited around 35 seconds).

![flag set successfully](/images/unlocking_hrz-99/hdd-change-complete.webp)

I should mention that the hdd is unlocked temporary. It will lock it again once you put it back into the radio. On avic forums there is someone mentioning a method to disable the lock but i did not try it myself.

Also DO NOT lose the hdd cover, there is a switch telling the radio to not power on at all if the cover is missing.

## SSD mod

Now that you have the HDD unlocked, you can clone it to a SSD.

~~well at least in theory anyways, i bought a ide to m.2 adapter and still waiting for it to arrive.~~

[24/4/2026]

it arrived and i clone it to a 128gb m.2 ssd and extend the MSV partition and now i have 90GB for music server which is way __way__ more than i would ever need.

A song is about ~3-4MB in AT3 compression, and my entire library of about 1550 takes up 18GB in aac and alac compression which is about ~6-24MB per file. So yeah it is overkill.

It boot up much faster now tho.

Also the stupid adapter i have used a through hole pin header instead of surface mounted header so i ended up having to design a entire new tray to compensate for the pin poking out so you should keep that in mind if you decide to get a adapter instead of a direct ide ssd. You can carefully insert it into the ide connector and pull the entire board up and shove the tray back in. Obviously it won't be good for the connect long term, you could tape the switch down as well so you won't have to use the tray.

## MSV song converter

For my model at least the files are just missing header. Find the AT3 files under the MSV partition MUSIC>OPLXX>OPLXXX

copy to somewhere on your computer and open one of the files in a hex editor (im using imHex here)

if your file look something like this

```
30 34 50 00 00 00 00 00 A2 00 03 A2 49 24 92 52     04P�����¢��¢I$’R
```

![pioneer header](/images/unlocking_hrz-99/pioneer_header.webp)

well you can try and replace `30 34 50 00 00 00 00 00` (anything before the A2 00 03 A2) with the following

```
52 49 46 46 3C 8C 34 00 57 41 56 45 66 6D 74 20 20 00 00 00 70 02 02 00 44 AC 00 00 99 40 00 00 80 01 00 00 0E 00 01 00 10 00 00 00 00 00 00 00 01 00 00 00 64 61 74 61 00 8C 34 00
```

(paste in the hex area)

You should see on the right side it would say something like RIFF and WAVE

![atrac3 header](/images/unlocking_hrz-99/atrac3_header.webp)

save and open it in VLC or ffmpeg. If it plays then congrats your files are not encrypted! All you need is a converter to convert it from AT3 to format that you want. 

```ffmpeg -i "TRACK01.AT3" "TRACK01.wav"```

I wrote a script that automatically adds the header to all the AT3 files in the folder.

[Pioneer-AT3](https://github.com/lkro13/Pioneer-AT3)

## Map data

If you are looking to add your own, i didn't figured out how. ~~All i know it is in kiwiGPS format.~~ Most forums brings up nothing other than using pioneer own map DVD

(Correction, see above map data section)

## IR code

### WARNING THIS IS FOR ESPHOME I HAVE NOT TRIED ON ARDUINO IR LIBRARY

- 0xB536 Left
- 0xB537 right
- 0xB52F up
- 0xB535 down
- 0XB56F enter/ok

- 0xB51A av
- 0XB56B maps

- 0XB50B vol down
- 0XB50A vol up

- 0xB529 BACK

## References

[kenkuma2.cocolog-nifty.com/blog/2010/12/hdd1-1f72.html](http://kenkuma2.cocolog-nifty.com/blog/2010/12/hdd1-1f72.html) < FujiTool method

[kiwi-w.org/format_english/format_kihon.html](https://web.archive.org/web/20090425190804/http://kiwi-w.org/format_english/format_kihon.html) < Kiwi documents

[avic411.com/index.php?/topic/30763-hdd-unlocking-free/](https://avic411.com/index.php?/topic/30763-hdd-unlocking-free/)

[www.minidisc.wiki/software/start](https://www.minidisc.wiki/software/start) < Various ATRAC and minidisc tools

obviously a whole bunch of sites were used while researching but i only added the relevant ones mostly avic411 forums


## Tools used

[XyLe-GBP - ATRACTool reloaded](https://github.com/XyLe-GBP/ATRACTool-Reloaded)

[dcherednik - atracdenc](https://github.com/dcherednik/atracdenc)

[werwolv - ImHex](https://imhex.werwolv.net/) (better alternative i started using, support shiftJIS as well!)

[Open Home Foundation - ESPHome](https://esphome.io/)

[mh-nexus - HxD](https://mh-nexus.de/en/hxd/) (Good for simple hex edits)

python

ffmpeg

wizTree

vsc (for shiftjis)

# Tool created

[Pioneer-AT3](https://github.com/lkro13/Pioneer-AT3)

![lmao](/images/unlocking_hrz-99/batch_std.webp)

(actual reply on the forum)

#### t(-.-t)

oh hey you are still here, if you noticed the commit date is much later than the tagged date it's because i kinda got distracted in figuring out stuff. I started writing this when i got the first AT3 playing and is way too excited to document this thing since after all, the avic forums is sort of dead ? and me and my brother put quite a lot of effort into searching for the hdd unlock method.

I never knew one day i would take on such project, documenting and figuring out stuff by myself (my brother is not a programmer, he just help me find some stuff). Always wanted to reverse engineer something but 9/10 times failed so it kinda the reason why i got really excited when i got the at3 playing. Now that i sort of figured out how the Music server work, i have started to look into the kwi files. It's not gonna be easy since the tools are either outdated or cannot run at all (kiwiread on github, and another written in ruby in a Japanese blog, also the python one but it's for loading.kwi which is for firmware as far as i can tell), but at least i have the documents and it's gonna be a while.

I stared at the ATRAC3 file for so long, I can tell you exactly where is the AT3 frame start by looking at the hex. I am very happy for the minidisc community, they gather plenty information in one place. That's how i found the user manual as well as a article about the release of the radio which mentioned it used ATRAC3. Plus ATRAC3 are much commonly used in minidisc so their list of tools did help a bit. **DO NOT INSTALL SONIC STAGE ON WINDOWS 11** it will cause your pc to not boot. [Follow this guide to fix it](https://www.reddit.com/r/Windows11/comments/1hu8er2/psa_pxhlpa64sys_causes_boot_issues_in_newest/) or delete `PxHlpa64.sys` from `Windows\System32\drivers` if you are able to boot into another os

I hope that this post help with you or someone would like to tinker with their head unit, the numbers of forums i went through to get the HDD unlocked is messy already and (in my research at least) no one has figured out how to get the AT3 files working. Oh hey if you got more information that you would like to tack on, feel free to send me a message. I would love to learn more about how their system works.

I really hope that no one would use the tools to sell it as a "service". I really believe such tools should be free and accessible to anyone since after all, it is a discontinued and obscure product. Im doing this for fun and on my time as well, it won't be fair if someone took others people work for free and sell to someone. Unfortunately i know it's gonna happen someday especially if i got a map converter working since the "carrozzeria" brand makes people pay stupid amount of money to get one from here even is it's just a remote (~55$ for a passive crossover, ~20$ for a used remote, ~10$ for just a single rca video cable????) you can imagine if someone on idk facebook or some shit advertise a service to load the local maps on the radio for probably high price as well.

My goal in getting the AT3 working is preserving the data of the last owner as there is a possibility that the albums it contain may not exist at all. Like I have a bulk of used MD, and there is 1 album where i could not find at all other than a used cd on second-hand market.

Also how the fuck do yall get a fucking LLM to sequence your DNA while my LLM keep telling me to give up or basically being useless???? I tried to use Gemini multiple times and they still provide garbage result. The worst is the "Thinking" models, they hallucinate so damn much. Hell even on Gemini fast it keep giving me tools that doesn’t even exist _don’t you have access to everyone data as well as the document you scrape??_

Right i did mention i have another hdd car radio as well, it's a Honda gathers for those who are wondering. I accidentally broke the display ribbon while trying to clean off the corrosion :/. To be fair i did bought it knowingly broken since i got for for basically ~2$ from a flooded vehicle. I do also have another again "broken" carrozzeria but it's a DVD navi. Both turns on and the Honda gathers at least functioning but the carrozzeria have a broken screen and i don't have the dvd for it to turn on. I did found a dump on the tracker site tho, just need a dual layer dvd, a functional screen and a working dvd drive for it.

Got distracted and write more than i should :p

if im still alive after the kiwi as well as finals you probably be seeing me again 

in a few months maybe. (¬_¬ )

> At least 1 LLM were harm while researching.