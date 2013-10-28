---
layout: post
title: "Deskphone: Hardware and software requirements"
date: 2013-08-21
comments: true
published: false
categories:
- Asterisk
---

Though we're ultimately trying to get some hardware working, all the important stuff will happen in software. That software is a default installation of Asterisk on a Linux server which is connected directly to the Internet (in other words, _not_ behind [NAT][nat]). You're on your own getting Asterisk installed, but I can say that for me, on [Ubuntu 12.04][12.04], it involved doing simply:

    $ sudo apt-get install asterisk

For hardware, your options are limitless. I am personally using a Panasonic [tgp550][tgp550] DECT phone system, which might not be a great choice for your needs. [DECT][dect] phone systems are basically like the cordless phone you may have had plugged into a land line at some point in your life.

This particular system has two phones: a desk phone, which doubles as the base station, plus a separate cordless phone. You can get a version of it, the [tgp500][tgp500], that replaces the desk phone with a dedicated base station. And you can buy additional cordless handsets (up to 6, I believe).

I chose this particular system because I an using Asterisk for both home and office lines and the cordless handsets fit into my idea of what a home phone should look like.

If you just want a phone on your desk, there are tons of phones that look like more traditional office phones. Any phone that can do [SIP][sip] will work. One thing to keep in mind: many VoIP phones only have wired network ports. I ended up using a WiFi access point to bridge my home network and provide a wired Ethernet connection to my phone.

Finally, you will need to use a VoIP service provider to route incoming and outgoing calls between your Asterisk system and the [Public Switched Telephone Network][pstn]. For this, we will use [Flowroute][flowroute]. My intuition tells me there isn't anything particularly special about Flowroute, but they're what I know.

# Other Hardware Options

- Cisco SPA 525G2
- POTS adapter
