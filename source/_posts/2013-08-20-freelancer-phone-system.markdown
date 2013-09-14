---
layout: post
title: "Freelancer Phone System"
date: 2013-08-20 14:04
comments: true
categories: 
---

In this guide I'm going to show you how to:

1. Purchase a phone number that people can dial
2. Ring a phone on your desk when people dial that number
3. Route your outgoing calls to the PSTN
4. Setup a voicemail system that emails voicemail recordings to you
5. Route 911 calls to your local emergency services

*This makes sense if you a) don't make many phone calls, b) already have a server running on the Internet, c) are happy configuring Asterisk.*

I am a freelance web developer who works from home. As a result, I need to make and receive phone calls about work stuff on a fairly regular basis. About a year ago, I determined that a dedicated phone number which rang an actual telephone on my desk would suit my needs best.

I had tried taking business calls on my mobile phone, but having business spill over into my personal life was maddening. I had also tried a [Skype phone number][skype-number], but I found managing multiple audio sources on my computer to be cumbersome. A desk phone with large physical buttons, a good speakerphone, and voicemail indicator lights sounded like an improvement.

The only problem is that phone lines are expensive. When I looked at the time, everything I was finding would have cost me $20-$30 per month. While I can easily afford $30/month, my Skype number was costing me $5/month for unlimited usage within the US. Plus, there's the principle of the matter: phone lines are just data; they should not cost $30/month. Period.

Around this time I saw [a presentation][asterisk-talk] by [Mike Frager][mike-frager] about using Perl to control [Asterisk][asterisk], which is open source software for creating phone systems. During Mike's talk, I learned that wholesale VoIP service is ridiculously cheap -- in the realm of my Skype number. You just have to know how to configure Asterisk and Mike's talk had run through a lot of the basics.

Stupidly, I thought to myself, "How hard can this be?".

I will spare you a bunch of bitching and moaning about what a travesty the Asterisk and VoIP resources Google surfaces are and just say that you are a lucky soul, indeed, to have walked down this path after me.

# Hardware and Software

Though we're ultimately trying to get some hardware working, all the important stuff will happen in software. That software is a default installation of Asterisk on a Linux server which is connected directly to the Internet (in other words, _not_ behind [NAT][nat]). You're on your own getting Asterisk installed, but I can say that for me, on [Ubuntu 12.04][12.04], it involved doing simply:

    $ sudo apt-get install asterisk

For hardware, your options are limitless. I am personally using a Panasonic [tgp550][tgp550] DECT phone system, which might not be a great choice for your needs. [DECT][dect] phone systems are basically like the cordless phone you may have had plugged into a land line at some point in your life.

This particular system has two phones: a desk phone, which doubles as the base station, plus a separate cordless phone. You can get a version of it, the [tgp500][tgp500], that replaces the desk phone with a dedicated base station. And you can buy additional cordless handsets (up to 6, I believe).

I chose this particular system because I an using Asterisk for both home and office lines and the cordless handsets fit into my idea of what a home phone should look like.

If you just want a phone on your desk, there are tons of phones that look like more traditional office phones. Any phone that can do [SIP][sip] will work. One thing to keep in mind: many VoIP phones only have wired network ports. I ended up using a WiFi access point to bridge my home network and provide a wired Ethernet connection to my phone.

Finally, you will need to use a VoIP service provider to route incoming and outgoing calls between your Asterisk system and the [Public Switched Telephone Network][pstn]. For this, we will use [Flowroute][flowroute]. My intuition tells me there isn't anything particularly special about Flowroute, but they're what I know.

# Costs

So, what's this endeavor gonna cost you? We'll start with the setup costs.

You're going to need a phone. The system I have, the [TGP550][tgp550] (it has a desk phone, plus a cordless handset), runs [about $230 on Amazon][tgp550-amazon]. It's sibling, the [TGP550][tgp550] (just the cordless handset), runs [about $170 on Amazon][tgp500-amazon]. [Additional handsets][tpa50] run [about $80 on amazon][tpa50-amazon].

If you want a more traditional desk phone, the [Cisco SPA525G2][spa525] is a good option. It has wifi, which can save you some hassle and expense if your desk isn't near your router. It also has bluetooth and does some fancy shit, like allow you to answer cell phone calls on your desk phone. You can pick one of these up for [about $180 on Amazon][spa525-amazon].

I have to give you a heads up about the Cisco phone though: I experienced some pretty serious call quality issues when I had one. I am 99% convinced that I had a configuration problem, since I don't really know what the fuck I'm doing, but I never got around to actually figuring out what the problem was. Instead, I had a baby and then switched to the Panasonic system, since it's a better fit for my setup (office _and_ home phone lines).

There are also some nominal setup costs that you'll incur through Flowroute. They'll charge you $1.00 per phone number to get service started with them, plus an additional $7.50 per phone number that you want to have ported over (porting a phone number is when you transfer an existing phone number from one provider to another).

Now let's take a look at your recurring costs, 'cause really, that's what matters. The $200 you spend on a phone should be amortized over a long time and work out to a miniscule monthly cost. If you keep your phone for 5 years, that works out to $3.33 per month. And that's before you sell the fuckin' thing on eBay.

Each phone number is going to cost you $1.25 per month, plus an additional $1.39 per month if you want to be able to dial 911 from that number. Incoming calls cost 1.2 cents per minute; unlimited incoming is $6.25 per phone number per month. Outgoing rates are based on destination. It's 0.98 cents per minute to the [contiguous United States][US48] & Canada. For the rest of the world, see [Flowroute's outbound rates][outgoing-rates].

So those are all the costs, with one big caveat: you need a server which is permanently connected to the Internet. This whole deal really only makes sense if you already have one. If you add the monthly cost of a [VPS][vps], it ends up making more sense to just pay for a phone line through your local telco.

# Configuration

Steps:

1. Forward calls from Flowroute to Asterisk
2. Connect Asterisk to Flowroute
3. Connect phone to Asterisk server
4. Route inbound calls
5. Route outbound calls

## Forward calls from Flowroute to Asterisk

## Connect Asterisk to Flowroute

Now that we've got Flowroute configured to send incoming calls to Asterisk, we need to get Asterisk connected to Flowroute so we can send outgoing calls to the outside world, also known as the Public Switched Telephone Network.

Asterisk thinks about how it connects to the outside world in terms of channels. So when this project is all said and done, your Asterisk server will have two channels: one connecting it to your phone and another connecting it to Flowroute. Right now, we'll concern ourselves with the Flowroute channel.

The default install of Asterisk puts a shitload of configuration files into `/etc/asterisk`. The one we're looking for is `sip.conf`, since we will be configuring a [SIP][sip] channel. So start by putting this into `/etc/asterisk/sip.conf`.

{% codeblock /etc/asterisk/sip.conf lang:ini %}
[flowroute]
host=sip.flowroute.com
username=12345678
secret=password
context=inbound
type=friend
nat=no
qualify=yes
dtmfmode=rfc2833
canreinvite=no
disallow=all
allow=ulaw
insecure=port,invite
{% endcodeblock %}

What's this all mean? Well, starting from the top: we're defining a channel called `flowroute`, then specifying a `host` to connect to, plus a `username` and `password` to use when connecting. Next, we specify the `context` that calls from this channel should use to enter The Dialplan.

The Dialplan is the meat and potatoes of Asterisk. It's where you define what happens to a inbound and outbound calls. Stuff like, ring this phone for 20 seconds, then go to voicemail. Contexts are _segments_ of The Dialplan. They're handy because you want to do different things with outbound and inbound calls.

As for the rest of these, well...

To be honest, I don't know what half of 'em do. Some of them are pretty obvious (`nat`) and others are not so much (`canreinvite` and `insecure`). I was going to research them so I could at least _seem_ like I have my shit together, but the state of Asterisk documentation is an [utter][docs1] [travesty][docs2].

But I will say this: they come from the recommended `sip.conf` that Flowroute provides and I can vouch for the fact that they will result in a working Asterisk configuration.

## Connect your phone to Asterisk

We need to create one more channel so our phone can connect to Asterisk. These settings also go in `/etc/asterisk/sip.conf`, since our phone will also be connecting to Asterisk via SIP. It doesn't matter where, but you can't go wrong just appending them to the bottom, below the `[flowroute]` channel.

{% codeblock /etc/asterisk/sip.conf lang:ini %}
[13235550100]
host=dynamic
secret=password
context=localexts
type=friend
nat=yes
canreinvite=no
disallow=all
allow=ulaw
insecure=port,invite
qualify=yes
{% endcodeblock %}

Overall, this channel looks fairly similar to the `[flowroute]` channel. There are a few differences, though. Here we're using your phone number as the name of the channel. Not only does this just make sense to me, but we're also going to exploit this fact to do some clever tricks later on when we get to The Dialplan.

The other major difference is the `context` setting. Above, we're sending calls from the `[flowroute]` channel to the `inbound` context. Here, we're sending calls from your phone to the `localexts` context. As I mentioned above, we want to do different things with inbound and outbound calls; sending them to different contexts in The Dialplan is what allows us to do that.

There are a few more, minor differences between this channel and the `[flowroute]` channel. First, your phone doesn't have a hostname, so `host` is set to `dynamic`. Also, your phone is behind a router, so `nat` is set to `yes`. Lastly, we don't need a `username` because we'll use the name of the channel instead.

I think that covers all of the differences, but if not, just copy and paste away in blissful ignorance.

## Route inbound calls

Now we're getting to the fun stuff. We're going to start you off with some training wheels, though, because we've got some new concepts to cover. For the time being, all we want to do with inbound calls is ring your phone. We'll get to voicemail a little later.

We take care of routing calls in The Dialplan. Though I have exalted it throughout this document with [title case][titlecase], The Dialplan is really just a configuration file called `extensions.conf`. So let's start by putting this in `/etc/asterisk/extensions.conf` and then we'll go through what it all means.

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[inbound]
exten => _1NXXNXXXXXX,1,Dial(SIP/${EXTEN})
{% endcodeblock %}

The first thing we're doing is defining a context, `[inbound]`. This is the same syntax we used above, in `sip.conf`, to define SIP channels, except that here it creates a context. The name of the context should also be familiar to you, because it's the same one that the `[flowroute]` channel is configured to send calls to.

In the next line, we're capturing the extension that was dialed, then dialing the SIP channel with the same name. I probably lost you there, so let's back up for a second.

In Asterisk, an extension is essentially "what the user dialed". Since this is the context where all the calls from the PSTN to your phone number get dumped, every single call going through it will be to the same extension: your phone number. Right? Because, for every call to your phone number, the user will have dialed... your phone number.

Since we cleverly used your _phone number_ as the name of the SIP channel for your _phone_, all we have to do with inbound calls is dial the SIP channel with the same name as the extension. This single line also has the benefit of accomodating additonal phone numbers, as long as we follow the same convention of naming our SIP channels after our phone numbers.

Okay, that's _what_ we're accomplishing, but let's dig ito _how_ we are accomplishing it. Here's that line again:

{% codeblock lang:ini %}
exten => _1NXXNXXXXXX,1,Dial(SIP/${EXTEN})
{% endcodeblock %}

To start, `exten =>` is just the syntax used to specify that we're creating a dialplan for a specific extension. A context can contain dialplans for many different extensions, but we're only specifying one (we'll get to multiple extensions in the `[outbound]` context).

Then we specify the number of the extension we're creating a dialplan for with `_1NXXNXXXXXX`. Well, a numeric extension, like `100` or `101`, is what you might _typically_ see at this point, but we're actually specifying a pattern, which is denoted by the leading underscore.

In a pattern, `0-9` match themselves, `X` matches a single digit from 0-9, `Z` matches a single digit from 1-9, and `N` matches a single digit from 2-9. So, this particular pattern matches a North American phone number.

That is, it matches the 1 before the area code (`1`), then the area code (`NXX`), then the "local" part of the phone number (`NXXXXXX`). Apparently the area code and "local" part of the number do not start with 0 or 1.


## Route outbound calls

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[localexts]
; Emergency services, remember to test this
exten => 911,1,NoOp()
  same => n,Set(CALLERID(num)=${CHANNEL:4:11})
  same => n,Dial(SIP/${EXTEN}@flowroute)

; Send to voicemail
exten => 1,1,Goto(${CHANNEL:4:11},1)

; Strip plus sign
exten => _+X!,1,Goto(${EXTEN:1},1)

; Add 1 before area code
exten => _NXXNXXXXXX,1,Goto(1${EXTEN},1)

; Main dialplan
exten => _1NXXNXXXXXX,1,NoOp()
  same => n,Set(LINE=${CHANNEL:4:11})
  same => n,GotoIf($[${EXTEN} = ${LINE}]?voicemail:outbound)
  same => n(voicemail),VoiceMailMain(${LINE}@default,s)
  same => n,Hangup()
  same => n(outbound),Set(CALLERID(num)=${LINE})
  same => n,Dial(SIP/${EXTEN}@flowroute)
  same => n,Hangup()
{% endcodeblock %}

## Setup voicemail

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[inbound]
exten => _1NXXNXXXXXX,1,NoOp()
  same => n,Dial(SIP/${EXTEN},20)
  same => n,VoiceMail(${EXTEN}@default,u)
{% endcodeblock %}

{% codeblock voicemail.conf lang:ini %}
[general]
format=wav49|wav
serveremail=voicemail@domain.com
attach=yes
skipms=3000
maxsilence=10
silencethreshold=128
maxlogins=3
emaildateformat=A%, %B %d, %Y at %r
pagerdateformat=A%, %B %d, %Y at %r
sendvoicemail=yes
tz=pacific

[zonemessages]
eastern=America/New_York|'vm-received' Q 'digits/at' IMp
central=America/Chicago|'vm-received' Q 'digits/at' IMp
pacific=America/Los_Angeles|'vm-received' Q 'digits/at' IMp

[default]
13235550100 => 1234,First Last,email@domain.com
{% endcodeblock %}

## Emergency Dialing

A word about enabling 911 on your phone: if you're the type that always has your cell phone in your pocket and you're only going to have an office phone (that a significant other or child wont reach for in an emergency), then this may not make sense for you. My wife and I share a cell phone, and it stays in the car. Other than that, the VoIP phones are the only phones in the house, so you better believe I enabled 911.

# Glossary of Terms

- DID
- Channel
- Context

# Other Hardware Options

- Cisco SPA 525G2
- POTS adapter

[asterisk]: http://www.asterisk.org/
[ubuntu]: http://www.ubuntu.com/
[flowroute]: http://flowroute.com/
[outgoing-rates]: http://flowroute.com/services/rates/
[skype-number]: http://www.skype.com/en/features/online-number/
[la-perl]: http://losangeles.pm.org/
[asterisk-talk]: http://losangeles.pm.org/presentations/voip/VOIP_with_Perl.pdf
[mike-frager]: https://twitter.com/mfrager
[nat]: http://en.wikipedia.org/wiki/Network_address_translation
[dect]: http://en.wikipedia.org/wiki/Digital_Enhanced_Cordless_Telecommunications
[sip]: http://en.wikipedia.org/wiki/Session_Initiation_Protocol
[pstn]: http://en.wikipedia.org/wiki/Public_switched_telephone_network
[12.04]: http://releases.ubuntu.com/precise/
[tgp500]: http://www.panasonic.com/business/psna/products-home-business/sip-communications/Hosted-Open-Source-Market/KX-TGP500.aspx
[tgp500-amazon]: http://www.amazon.com/Panasonic-KX-TGP500-DECT-Phone-System/dp/B0058FJLBG
[tgp550]: http://www.panasonic.com/business/psna/products-home-business/sip-communications/Hosted-Open-Source-Market/KX-TGP550.aspx
[tgp550-amazon]: http://www.amazon.com/Panasonic-KX-TGP550-SIP-DECT-Phone/dp/B002SUEQBY
[tpa50]: http://www.panasonic.com/business/psna/products-home-business/sip-communications/Hosted-Open-Source-Market/KX-TPA50.aspx
[tpa50-amazon]: http://www.amazon.com/Panasonic-KX-TPA50B04-KX-TPA50-Cordless-Handset/dp/B002SUAQ1I
[spa525]: http://www.cisco.com/en/US/prod/collateral/voicesw/ps6788/phones/ps10499/ps11005/data_sheet_c78-603725.html
[spa525-amazon]: http://www.amazon.com/Cisco-spa525G2-5-Line-IP-Phone/dp/B003UMCMU6
[US48]: http://en.wikipedia.org/wiki/Contiguous_United_States
[titlecase]: http://www.grammar-monster.com/lessons/capital_letters_title_case.htm
[vps]: http://en.wikipedia.org/wiki/Virtual_private_server
[docs1]: https://wiki.asterisk.org/wiki/display/AST/Asterisk+1.8+Documentation
[docs2]: http://www.voip-info.org/wiki/view/Asterisk+-+documentation+of+application+commands
