---
layout: post
title: "How to configure Asterisk for your home office"
date: 2013-10-28 11:33
comments: true
categories:
- Asterisk
---

I work from home. Along with work comes frequent work-related phone calls. For a while I was taking work calls on my mobile phone, but having work spill over into my personal life was driving me nuts. I also tried a [Skype phone number][skype-number], but managing calls from my computer also drove me nuts.

About a year ago, I determined that the right thing for me was a dedicated phone number that rang an actual telephone on my desk. It would have a decent speakerphone, a dedicated voicemail light, and actual hardware buttons for dialing -- all of those glorious things.

And then I did a dumb thing.

<!-- more -->

Instead of just paying for a normal phone line, which would run me about $20/month, I decided to buy phone service from a wholesale VoIP provider and roll my own [Asterisk][asterisk] installation. Thus began an odyssey in which I learned more about Asterisk and the telecom industry than any decent person should really know.

All in the name of saving $17/month.

But, as they say, every cloud has it's silver lining. And in this case it's that _you_ get to skip all the [terrible documentation](http://www.voip-info.org/wiki/view/Asterisk) and [obtuse telecom jargon](http://en.wikipedia.org/wiki/Direct_inward_dialing) and just _get this fucking thing done_.

In a series of posts I'm going to show you how to:

1. Purchase a phone number that people can dial.
2. Ring a phone on your desk when people dial that number.
3. Route your outgoing calls to the public telephone network.
4. Setup a voicemail system that emails voicemail recordings to you.
5. Route 911 calls to your local emergency services.

These posts will assume that you know absolutely nothing about Asterisk, VoIP, or the telecom industry.

_A note: I owe a debt of gratitude to [Mike Frager][mike-frager], who did [a presentation][asterisk-talk] at LA Perl Mongers about using Perl to control Asterisk, and Leif Madsen, Jim Van Meggelen, and Russell Bryant, whose book, [Asterisk: The Definitive Guide](http://ofps.oreilly.com/titles/9781449332426/), can be accessed for free on the web (as of the time of this writing)._

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
