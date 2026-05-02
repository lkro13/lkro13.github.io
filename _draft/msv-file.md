---
layout: post
title:  "Reading MSV database"
date:   2026-04-15 11:19:00 +0800
categories: radio
---

# WIP 

if for whatever reason you are reading the repo instead

change the date later

Before we start, yes LLMs were used but they are being useless as usual. Although gemini did figured out the timestamp part but that's all.

> I don't have friends that do this type of shit ok?

File structure ?

OPLXX > Album song list
GPLXX > Genre song list
APLXX > Artist song list
PLDATXX > Song information (sorted by album)
UPLXX > likey to be Mymix 

when recording a album, a new opl file is created 

HOLD > MUSIC > OPLXX > OPLXXX.PL

as well as track data

HOLD > MUSIC > PLDATXX > PLXXX.DAR

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

