---
layout: post
title: "Freelancer Phone System"
date: 2013-08-20 14:04
comments: true
categories: 
---

I am a freelance web developer who works from home. As a result, I need to make and receive phone calls about work stuff on a fairly regular basis. About a year ago, I determined that a dedicated phone number which rang an actual telephone on my desk would suit my needs best.

I had tried taking business calls on my mobile phone, but having business spill over into my personal life was maddening. I had also tried a [Skype phone number][skype-number], but I found managing multiple audio sources on my computer to be cumbersome. A desk phone with large physical buttons, a good speakerphone, and voicemail indicator lights sounded like an improvement.

The only problem is that phone lines are expensive. When I looked at the time, everything I was finding would have cost me $20-$30 per month. While I can easily afford $30/month, my Skype number was costing me $5/month for unlimited usage within the US. Plus, there's the principle of the matter: phone lines are just data; they should not cost $30/month. Period.

Around this time I saw [a presentation][asterisk-talk] by [Mike Frager][mike-frager] about using Perl to control [Asterisk][asterisk], which is open source software for creating phone systems. During Mike's talk, I learned that wholesale VoIP service is ridiculously cheap -- in the realm of my Skype number. You just have to know how to configure Asterisk and Mike's talk had run through a lot of the basics.

Stupidly, I thought to myself, "How hard can this be?".

I will spare you a bunch of bitching and moaning and just say that you are a lucky soul, indeed, to have gone down this path after me.

In this guide I'm going to show you how to:

1. Purchase a phone number that people can dial
2. Ring a phone on your desk when people dial that number
3. Route your outgoing calls to the PSTN
4. Setup a voicemail system that emails voicemail recordings to you
5. Route 911 calls to your local emergency services

# Hardware and Software

Though we're ultimately trying to get some hardware working, all the important stuff will happen in software. That software is a default installation of Asterisk on a Linux server which is connected directly to the Internet (in other words, _not_ behind [NAT][nat]). You're on your own getting Asterisk installed, but I can say that for me, on Ubuntu 12.04, it involved doing simply:

    $ sudo apt-get install asterisk

For hardware, your options are limitless. I am personally using a Panasonic [TGP-550][tgp-550] DECT phone system, which might not be a great choice for your needs. [DECT][dect] phone systems are basically like the cordless phone you may have once had plugged into a land line at some point in your life.

This particular system has two phones: a desk phone, which doubles as the base station, plus a separate cordless phone. You can get a version of it, the [TGP-500][tgp-500], that replaces the desk phone with a dedicated base station and additional cordless handset. And you can buy additional cordless handsets (up to 6, I believe).

I chose this particular system because I an using Asterisk for both home and office lines and the cordless handsets fit into my idea of what a home phone should look like.

If you just want a phone on your desk, there are tons of phones that look like more traditional office phones. Any phone that can do [SIP][sip] will work. Keep in mind that whatever phone you use will need an Internet connection. Many VoIP phones only have wired network connections. I ended up using a WiFi access point to create a network bridge to my phone system.

Finally, you will need to use a VoIP service provider to route incoming calls to your Asterisk system and outgoing calls to the [Public Switched Telephone Network][pstn]. For this, we will use [Flowroute][flowroute]. My intuition is that there isn't anything particularly special about Flowroute, but they're what I know.

# Costs

Setup costs:

- Phone: $186.99 for cordless handset only or $219.99 for desk phone + cordless phone
- Phone number setup: $1.00 whether it's a new number or a ported number
- Phone number port: $7.50

Office line only without port: $187.99
Office line only with port: $196.49
Office + Home lines without number ports: $221.99
Office + Home lines with number ports: $236.99

|Configuration      |New Numbers ($1 setup fee each)|Ported Numbers ($1 setup fee + $7.50 each)|
|Office Line        |$187.99                        |$196.49                                   |
|Office + Home Lines|$221.99                        |$236.99                                   |

Recurring costs:

- Phone number: $1.25 per month per phone number
- E911 service: $1.39 per month per phone number
- Incoming calls: 1.2 cents per minute or $6.25 per line unlimited
- Outgoing calls: 0.98 cents perminute to US48 + Canada, see [Flowroute's outbound rates][outgoing-rates] for other destinations

# Configuration

Steps:

1. Forward calls from Flowroute to Asterisk
2. Connect Asterisk to Flowroute
3. Connect phone to Asterisk server
4. Route inbound calls
5. Route outbound calls

{% codeblock sip.conf lang:ini %}
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

{% codeblock extensions.conf lang:ini %}
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

# Glossary of Terms

- DID
- Channel
- Context

# Other Hardware Options

- Cisco SPA 525G2
- POTS adapter

[asterisk]: http://www.asterisk.org/
[ubuntu]: http://www.ubuntu.com/
[tgp-500]: http://www.panasonic.com/business/psna/products-home-business/sip-communications/Hosted-Open-Source-Market/KX-TGP500.aspx
[tgp-550]: http://www.panasonic.com/business/psna/products-home-business/sip-communications/Hosted-Open-Source-Market/KX-TGP550.aspx
[flowroute]: http://flowroute.com/
[outgoing-rates]: http://flowroute.com/services/rates/
[skype-number]: http://www.skype.com/en/features/online-number/
[la-perl]: http://losangeles.pm.org/
[asterisk-talk]: http://losangeles.pm.org/presentations/voip/VOIP_with_Perl.pdf
[mike-frager]: https://twitter.com/mfrager
[nat]: http://en.wikipedia.org/wiki/Network_address_translation
[dect]: http://en.wikipedia.org/wiki/Digital_Enhanced_Cordless_Telecommunications
[sip]: http://en.wikipedia.org/wiki/Session_Initiation_Protocol
