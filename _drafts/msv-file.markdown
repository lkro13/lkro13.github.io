---
layout: post
title:  "Reading MSV database"

categories: radio
---

# date:   2026-04-15 11:19:00 +0800 < date started

# WIP 

if for whatever reason you are reading the repo instead

change the date later

Before we start, yes LLMs were used but they are being useless as usual. Although gemini did figured out the timestamp part but that's all.

> I don't have friends that do this type of shit ok?

Also this is all over the place as im still in the process of figuring out exactly how each files are created.

I know it's a mess i hope this toc can help a bit, i still trying to figure out jekyll as well as how their plugin works

* toc
{:toc}

# File structure ?

> if you know a jekyll plugin to display files structures lmk because that would be nice

this sturcture only contains files we are intersted 

```
MSV
|- HOLD\
|   ...
|   |-MUSIC\
|       |-APL01-10\ 
|       |   |- APL001-100.PL
|       |   |- APL101-200.PL
|       |   ....
|       |
|       |- COMMON\
|       |   |- GAPL.PLL
|       |   |- GGPL.PLL
|       |   |- GOPL.PLL
|       |   |- GUPL.PLL
|       |   |- ROAPL.DAT
|       |   |- ROOPL.DAT
|       |   |- ROUPL.DAT
|       |   ...
|       |
|       |- GPL01\
|       |   |- GPL001-015.PL
|       |
|       |- OPL01-10\
|       |   |- OPL001-100.PL (similar to APL)
|       |   ...
|       |
|       |- PLDAT01-10\
|       |   |- PL001-PL100.DAT (like APL and OPL)
|       |   ...
|       |
|       |- UPL01\
|       |   |- UPL001-005.PL (i believe it is user playlist files)
|
|- MUSIC\
|   |- OPL01-OPL10\
|       |- OPL001-100\ (like above)
|           |-TRACKXX.AT3
|           ...
...

```

#  MSV:/HOLD/MUSIC

the order of each will follow the above direcotry structer layout

we're skipping past the other 2 folder (AVBACKUP and CDDB) as they are irrelevent here

Also before we start the following files are my own cd rips into the hdd, i created my own compilation so i have more data to work with.

**ALL FILES ARE FORMATED IN SHIFTJIS**

## APL01-10/

They are split into 100 files per folder so `APL01` will have `APL001-100.PL` and `APL02` will have `APL101-200.PL` why ? idk this applies to others as well.

### APLXXX.PL file

[screenshot here]

this file contains information on the artist.

It starts with a version thingy and the 32 bytes for the artist name follow by 4 bytes of 00 and artist name again??? more padding and then the artist id this is on the file as well (the file is in hex while the file name is in decimal). **More** padding then 2 bytes for total tracks and 2 bytes for total tracks. again. _padding padding paddings_ and i have no idea what the "90 01" means. _More paddings_

and then we get to the tracks 2 bytes for album id and 2 bytes for track id (i think i did not create more then 255 tracks/album to find out) this repeats for all tracks that fall under the same artist

**IMPORTANT NOTE**

the hex pattern below contains `totalMinutes` and `totalSeconds`. this is **only** used on album files (OPLXXX.PL) and it is **not** used on other place (as far as i can tell)

## COMMON/

Here contains files that determine the album, artist, genre, user playlist order as well as their "voice search" (i will be calling it search from now on)

`G-PL.PLL` is the orders while `R--PL.DAT` is the search file

### G-PL.PLL

this includes

- GAPL.PLL (artist)
- GGPL.PLL (Genre)
- GOPL.PLL (Album)
- GUPL.PLL (User playlist)

the order of the `entry` determine how it is listed on the radio

starts off with the version again then follow by 1 byte that idk, padding, 2 bytes i think it's the max number of entries, follow by 2 bytes of total entry, 2 byte i think is total visible entries, padding and `entries`

the reason why i think the 2 byte is max entries as they are 999 in `OPL` and `APL` which if we see the folders in MUSIC/OPL10/ the last is OPL999 and in `GPL` the max is 15 which we only have 15 genre and in `UPL` we have max of 99 but no idea is that the actual max

#### Entry

starts with 1 byte that idk (most likely the file type ?) then 3 bytes padding and `01` and 3 bytes padding again. Next this byte determine is it visible or not (i think)

- `00` hidden
- `01` shown

another `00` then 2 bytes for the ID of the thing (artist, genre, album, userplist) then 20 bytes padding

repeat until the end

(GOPL.PLL shown)

[image]

### RO-PL.DAT

"voice search" files (when you press the voice assistant and speak a word, this is where it will search)

short usage guide translated from the user manual with some experimenting

In a playlist (artist, album, genre, userplist), speaking the name of the song you want to play

Switch album by saying "album" (ah-lu-bum-moo for jp) then the name of the album

switch artist by saying "artist" then the name of artist

switch genre by saying "genre" then the genre

this section includes the following files

- ROAPL.DAT
- ROOPL.DAT
- ROUPL.DAT

The `entry` order is **fixed** and correspond to the ID of the playlist

no matter the id in the file, the entry location is **fixed**. Artist id 40 **must** be the 40th entry, if a artist is "unavailable" the entry will be zero out.

starts off with the version (again) then 4 bytes that is fixed for each file(i think) then the entries

#### Entry

starts of with a byte of the type (i think) then 3 bytes padding follow by a byte that i think is used for visibility ? possibly idk it is like the G-PL file. 2 bytes for the id then 2 bytes of padding then 64 bytes for the search in hiragana (according to my brother). ends with 4 bytes of padding. rise and repeat

funny enough, both ROAPL and ROOPL have pre inserted 1000 entries, does that means the maxium you can have is 1000 albums and 1000 diffrent artist ? considering the MUSIC also have 1000 folders created? hmm

if my script is correct, i have about 545 unique artists and possibiliy 1051(!) albums according to lyrion (include duplicates as well as multi compilation albums) that might be a issue uh hmm


## GPL01/

this is where the genres is stored (i put mine all as others)

there are 15 genres total and they have their own ids

| id | genre |
| -- | ----- |
| 01 | Others |
| 02 | J-POP |
| 03 | Rock/Pop |
| 04 | Jazz | 
| 05 | Classic |
| 06 | Hip Hop/Rap |
| 07 | Black/soul (?) |
| 08 | Club/Dance |
| 09 | Blues |
| 10 | Reggae |
| 11 | Techno |
| 12 | Fusion |
| 13 | Soundtrack |
| 14 | world |
| 15 | Enka/Folk song (?) |

and corrisponse to their files as well

GPL0XX.PL

File strucrer is like APL but curios enoguh there isn a second "genre title" like in APL there is 2 artist huh

## OPLXX/

there is 10 OPL folders like OPL01-OPL10 and inside contains 100 files. This is roughly how it is

```
OPL01/
    |- OPL001.PL
    | ...
    |- OPL100.PL

OPL02/
    |- OPL101.PL
    | ...
    |- OPL200.PL

...
```

(jugding by how the music data is stored. i could be wrong)

### OPLXXX.PL

each OPLxxx.PL file is exactly like above BUT the first `listName` is the album title and `listName2` is the album artist. the `ID` is the artist ID. There is `totalMinutes` and `totalSeconds` although it doesn't does much as far as i can tell? but still a good idea to have it.

> sooo where's the album id?

as far i can tell it just based on the file name so OPL025.PL would be `19` (in hex)

it does seems like the max tracks in a album is 99, otherwise it will just wrap around back to track 1.

## PLDATXX/

this is where the track info is stored, follow with the album

so OPL001.PL will be PL001.DAT

OPL01 folder would be PLDAT01

(you get the idea right?)

### PLXXX.DAT

this is much more intersting and also the one i first looked into (also created my first hex pat with it i just updated with some new info i theorize and better naming)

starts off with the version as well but unlike others, it goes straight to the entries

#### Entry

Each entry is fixed at 256 bytes. 

The max amount of entry is 99. Techinaclly you can go beyond it but the radio will not read the rest. no idea why but mine either show random tracks or just none.

Starts with 32 bytes for the track title follow by 3 bytes padding and a `01` for some reason (possibily recording status ? `00` unfinished recording, `01` finished recording?)

and we got 64 bytes for the "search" in hiragana follow by 4 bytes padding. then 32 bytes for artist follow with 4 bytes of padding then 2 bytes for artist ID and 2 bytes of padding.

2 bytes for the genre id follow by 8 bytes fixed(?). 2 byte for minutes and 2 bytes for seconds then we got `84 00 00 00 00 01` im not sure what it does but it is fixed accorss all files. You may notice i group it with `ignoring` as they are both i cannot figure out what they are. some have, some does not, sometimes it have 1 byte padding between each byte and sometimes it just a cluster of data. Cannot figure out what it means at all, it might be the grace note thingy but a album i got the info using grace note does not have it so ¯⁠\⁠_ ⁠(⁠ツ⁠)_⁠/⁠¯

_next 3 bytes of padding_ 

> probably my own stupidty before. it probably is just 2 bytes and the `ignoring` expands by 1 but idc

next 1 byte or `trackIndicator` byte. again not sure but is is constant accorss all files except the last track where is just `00` instead of `64` 

> Gemini said it is volume ? i don't trust it

15 bytes of constant data again. then timestamp ts is so damn stupid actually it literally is just yyyymmddhhmmss in hex

> why i did not notice it until gemini points it out? 

Because i have the gps attena plugged in and it **has** the correct time but for some reason the system is stuck at 2006-09-13 22:07:21 while i ripped the disc in idk _fucking 2026????_

then we have `02` for idk seems constant as well then 49 bytes of padding. repeat ts till end

[image of pldat]

## UPL/

this folder contains 5 `.PL` files which i bealive it is the "myMix" or i called it user playlist 

i did not look into it at all but the syntax does looks like others

# MSV:/MUSIC

this is where the actual music data is stored

also there is a image folder here ? no idea what it's for

contains 10 folder as well from OPL01-OPL10

## OPLXX

each OPLXX folder contains additional 100 folders like this

```
OPL01/
    |- OPL001/
    | ...
    |- OPL100/

OPL02/
    |- OPL101/
    | ...
    |- OPL200/

...
```

notice how it's like OPL in HOLD folder ? (i just copied and paste it)

### OPLXXX

this is where the actual music data is stored

the folder is the album id so OPL01/OPL025/ would be `19` (in hex)

there will be files named like `TRACKXX.AT3`, this is where the audio is ripped, compressed and stored.

track number follow the entry on PLDATXX/PLXXX.DAT

like TRACK01 will be the first entry and TRACK02 is the second entry and so on

so a album with the id of `19` would be 25 in decimal which would be OPL01/OPL25 and PLDAT01/PLDAT025.DAT

the files are in ATRAC3 with pioneer header, if you want to add your own music remove the ATRAC3 header and replace with pioneer ones. if you want to dump the tracks, replace pioneer header with a ATRAC3 header. more info in my [last blog](https://lkro13.github.io/2026/04/15/carrozzeria.html#msv-song-converter) 

tl:dr

`30 34 50 00 00 00 00 00` is the pioneer header

```
52 49 46 46 3C 8C 34 00 57 41 56 45 66 6D 74 20 20 00 00 00 70 02 02 00 44 AC 00 00 99 40 00 00 80 01 00 00 0E 00 01 00 10 00 00 00 00 00 00 00 01 00 00 00 64 61 74 61 00 8C 34 00
```
is a ATRAC3 header

replace pionner header with ATRAC3 header if you wanna read and vice verse if you want to write

# How cd is ripped to disc

it first starts by looking for empty OPL or any existing OPL that matches the disc. If there is any existing ones it just continues recording it.

If it's a new disk

it will create/modify the following files

```
* Modify
# Create

MSV/
|- HOLD/
|   |- MUSIC/
|       |- APLXX/
|       |   |- APLXXX.PL #
|       |       ...
|       |       
|       |- COMMON/
|       |   |- GAPL.PLL *
|       |   |- GOPL.PLL *
|       |   |- ROAPL.DAT *
|       |   |- ROOPL.DAT *
|       |
|       |- GPL01/
|       |   |- GPL0XX.PL *
|       |
|       |- OPLXX/
|       |   |- OPLXXX.PL #
|       |
|       |- PLDATXX/
|           |- PLXXX.DAT #
|
|- MUSIC/
    |- OPLXX/
        |- OPLXXX/
            TRACKXX.AT3 #
            ...
```

~~In my testing at least, it won't add same artist to file it will create a new one instead for some reason?~~

~~might be my disc label is wrong but im not sure~~

i fucking just realized i put the wrong id. imma test with a new one and report back

> fuck

To explain it better Im gonna create a hypotectical DB with songs already and assumed all disc are ripped completely

```
Number of albums: 4
Number of artist: 12
```

> Disc only contains 1 artist for simplicitly but it will create multiple artist files if there is multiple diffrent artist

so inserting a disc it will create
```
MSV/
    HOLD/
        MUSIC/
            |- APL01/
            |   |- APL013.PL
            |
            |- OPL01/
            |   |- OPL005.PL
            |
            |- PLDAT01/
                |- PL005.DAT
    MUSIC/
        |- OPL01/
            |- OPL005/
                TRACKXX.AT3
                ...

```
And modify
```
MSV/
    HOLD/
        MUSIC/
            |- COMMON/
                |- GAPL.PLL 
                |- GOPL.PLL
                |- ROAPL.DAT
                |- ROOPL.DAT
            |- GPL01/
                |- GPL0XX.PL (based on genre id)
```

Import the hex pats bellow to see the data structure, refer to above for details (need to rewrite tbh)

but basically it will take data from cd text (if any) and insert it into the db

| file | details |
| ---- | ------- |
| `OPL005.PL` | Contains album info like album name, artist and tracking order |
| `APL013.PL` | Contains artist info like artist name and track order |
| `PL005.DAT` | Contains track info for each track, it contains track title, artist, hiragana and genre|

> Note that both `.PL` files contains the track orders described above

the file it modified basically tells the radio 
> hey! I exist!

| file | details |
| ---- | ------- |
| `GAPL.PLL` | contains the order of the artist listing in 32 bytes entries |
| `GOPL.PLL` | Same 32 byte entry but for album |
| `ROAPL.DAT` | Contains hiragana of the artist in 80 bytes chunk |
| `ROOPL.DAT` | Contains hiragana of the album in 80 bytes chunk |
| `GPL0xx.PL` | Contains list of songs in the genre |

if the disc did not specify the genre, it will be places as others or `01`

both `.DAT` files are fixed as describe above and below, and it will only automatically do translation if it's in japanese
else it will be just `00` 

it is used for their voice search system (which btw works surprisly well, better than a 2023 toyota corolla cross somehow)

> it's a mess i know but im trying my best to describe it

## Hex pats 
<p style="opacity:0; height: 0px">whos a good hex~ yes you are~ yes you are~<br><i>what im i doing</i></p>

i found out most are repeating so im just gonna make a generic ones that applies to multiple files

### G-PL.PLL

or groups list or idk

applies to the following files

- GAPL.PLL (artist)
- GGPL.PLL (Genre)
- GOPL.PLL (Album)
- GUPL.PLL (User playlist)

totalEntry **must** match the number of the entry in the file. Failure to do so will result the file getting wiped and only the top 4 remains. (ask me how i know)

```
#include "std/mem.pat"

struct Entry{
    u8 unknown;
    u8 padding1[3];
    u8 const1[4];
    u8 visibility; // oo: hidden 01: shown
    u8 padding2;
    u16 ID; //ID for thingy like artistID or alubmID
    u8 padding3[20];
};

struct Gidk{
    char version_num[12];
    u8 unknown;
    u8 const1[7];
    u16 maxEntry;
    u16 totalEntry;
    u16 visibleEntry;
    u16 padding1;
    
    
    Entry entry[while(!std::mem::eof())] [[comment("Tracks")]];
};

Gidk gidk @ 0x00;
```

### RO-PL.DAT

voice search files

applies to the following files

- ROAPL.DAT
- ROOPL.DAT
- ROUPL.DAT

the entry **must** follow the id like entry 1 if the id is 1, entry 40 if the id is 40. This **cannot** be changed and is **fixed**. Even if you skip a id like 1,2,4,5 the entry still follows like 1,2,[],3,5

```
#include "std/mem.pat"

struct Entry{
    u32 fixed;
    u8 visibility; //(i think?)
    u24 padding1;
    u16 id;
    u16 padding2;
    char search[64];
    u32 padding3;
};

struct ROsomething{
    char version[12];
    u8 fixed[4];
    Entry entry[while(!std::mem::eof())] [[comment("Tracks")]];

};

ROsomething rosmth @ 0x00;
```

### playlist or something (i srs do not know)

applies to the following files

- GPLXXX.PL
- APLXXX.PL
- OPLXXX.PL
- UPLXXX.PL

**IMPORTANT NOTE**

the hex pattern below contains `totalMinutes` and `totalSeconds`. this is **only** used on album files (OPLXXX.PL) and it is **not** used on other place (as far as i can tell).

in OPLXXX.PL the first `listName` is the album name while the second `listName2` is the album artist and the `ID` is the artist ID

GPLXXX.PL and UPLXXX.PL have the `listName2` zero out while APLXXX.PL have the artist name again for some reason?

> can this get more confusing?


```
#include "std/mem.pat"

struct timestamp{
    u16 year;
    u8 month;
    u8 date;
    u8 hour;
    u8 minute;
    u8 seconds;
    u8 dayOfTheWeek;
};

struct tracks{
    u16 albumID;
    u16 trackID; // in the album
};

struct trackListing{
    char version_num[12];
    char listName[32];
    u8 const1[4];
    char listName2[32];
    u8 const2[4];
    u16 ID;
    u8 const3[10];
    u16 totalTracks;
    u16 totalTracks2;
    u16 totalMinutes;
    u16 totalSeconds;
    timestamp timestamp;
    u8 const5[30];
    u16 noidea;
    u8 const6[32];
    tracks tracks[while(!std::mem::eof())] [[comment("Tracks")]];
};

trackListing list @ 0x00;
```

### PLXXX.DAT

The `recordingStatus` is something i theorized, i may be wrong. `00` unfinshed recording `01` finished recording. It could be just a visibility indicator like the above

I not sure what does the `trackIndicator` does ? but it is `64` on all track except the last one which is just `00`

for genre id refer to [above GPL01 section](#gpl01) (in hex of course, convert the decimal id to hex)

```
#include "std/mem.pat"

struct timestamp{
    u16 year;
    u8 month;
    u8 date;
    u8 hour;
    u8 minute;
    u8 seconds;
    u8 dayOfTheWeek;
};

struct msv{
    char title[0x20];
    u24 const1;
    u8 recordingStatus;
    char search[0x40];
    u32 padding1;
    char artist[0x20];
    u32 padding2;
    u16 artistID;
    u16 paddingss;
    u16 genre;
    u8 const3[0x08];
    u16 minutes;
    u16 seconds;
    u8 ignoring[0x1F];
    u24 padding3;
    u8 trackIndicator; //unsure
    u8 const5[0x0F];
    timestamp timestamp;
    u8 const6;
    u8 paddingssss[0x27];
};

struct dat{  
    u8 versionNum[0x0c];
    msv tracks[while(!std::mem::eof())] [[comment("Tracks")]];
};

dat data @ 0x00;
```

> it's already 463 lines ? damn i haven't even add docs for how it records the songs

> andddd now we're at 656 lines!

# Tool used

atracdenc

ImHex <3

Wiztree

google translate

deepl



<details markdown="block">

<summary>old/in progress docs (it is confusing i do not recommend refering this)</summary>
<br>

OPLXX > Album song list

GPLXX > Genre song list

APLXX > Artist song list

PLDATXX > Song information (sorted by album)

UPLXX > likey to be Mymix 

when recording a album, a new opl file is created 

HOLD > MUSIC > OPLXX > OPLXXX.PL

as well as track data

HOLD > MUSIC > PLDATXX > PLXXX.DAT

depends on the track genre, it will be added to GPL files

HOLD > MUSIC > GPL01 > GPL0XX.PL 

based on the genre id (i think)

If there is new artist being added a new artist file is created containing the tracks id

HOLD > MUSIC > APLXX > APLXXX.PL 

Based on the artist id

the rest need to figure it out rn


GAPL.PLL < Artist list ?

GOPL.PLL < album list

ROAPL.DAT < voice search for artist

ROOPL.DAT < voice search for album

RRPL.PL i uh really cannot

</details>