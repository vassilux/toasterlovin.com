---
layout: post
title: "Asterisk + Flowroute + SIP Phone = Deskphone"
date: 2013-08-20 14:04
comments: true
categories:
- Asterisk
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
[opium-suppositories]: http://www.imdb.com/title/tt0117951/quotes?item=qt0335547
