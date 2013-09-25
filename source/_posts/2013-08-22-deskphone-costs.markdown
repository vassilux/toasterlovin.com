---
layout: post
title: "Deskphone: Costs"
date: 2013-08-22
comments: true
categories:
- Asterisk
---

So, what's this endeavor gonna cost you? We'll start with the setup costs.

You're going to need a phone. The system I have, the [TGP550][tgp550] (it has a desk phone, plus a cordless handset), runs [about $230 on Amazon][tgp550-amazon]. It's sibling, the [TGP550][tgp550] (just the cordless handset), runs [about $170 on Amazon][tgp500-amazon]. [Additional handsets][tpa50] run [about $80 on amazon][tpa50-amazon].

If you want a more traditional desk phone, the [Cisco SPA525G2][spa525] is a good option. It has wifi, which can save you some hassle and expense if your desk isn't near your router. It also has bluetooth and does some fancy shit, like allow you to answer cell phone calls on your desk phone. You can pick one of these up for [about $180 on Amazon][spa525-amazon].

I have to give you a heads up about the Cisco phone though: I experienced some pretty serious call quality issues when I had one. I am 99% convinced that I had a configuration problem, since I don't really know what the fuck I'm doing, but I never got around to actually figuring out what the problem was. Instead, I had a baby and then switched to the Panasonic system, since it's a better fit for my setup (office _and_ home phone lines).

There are also some nominal setup costs that you'll incur through Flowroute. They'll charge you $1.00 per phone number to get service started with them, plus an additional $7.50 per phone number that you want to have ported over (porting a phone number is when you transfer an existing phone number from one provider to another).

Now let's take a look at your recurring costs, 'cause really, that's what matters. The $200 you spend on a phone should be amortized over a long time and work out to a miniscule monthly cost. If you keep your phone for 5 years, that works out to $3.33 per month. And that's before you sell the fuckin' thing on eBay.

Each phone number is going to cost you $1.25 per month, plus an additional $1.39 per month if you want to be able to dial 911 from that number. Incoming calls cost 1.2 cents per minute; unlimited incoming is $6.25 per phone number per month. Outgoing rates are based on destination. It's 0.98 cents per minute to the [contiguous United States][US48] & Canada. For the rest of the world, see [Flowroute's outbound rates][outgoing-rates].

So those are all the costs, with one big caveat: you need a server which is permanently connected to the Internet. This whole deal really only makes sense if you already have one. If you add the monthly cost of a [VPS][vps], it ends up making more sense to just pay for a phone line through your local telco.
