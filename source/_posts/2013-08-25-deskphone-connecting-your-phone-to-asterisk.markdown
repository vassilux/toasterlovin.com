---
layout: post
title: "Deskphone: Connecting your phone to Asterisk"
date: 2013-08-25
comments: true
published: false
categories:
- Asterisk
---

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
