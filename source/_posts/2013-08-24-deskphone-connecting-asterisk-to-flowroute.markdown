---
layout: post
title: "Deskphone: Connecting Asterisk to Flowroute"
date: 2013-08-24
comments: true
published: false
categories:
- Asterisk
---

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
