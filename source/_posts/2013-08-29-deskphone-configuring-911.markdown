---
layout: post
title: "Deskphone: Configuring 911"
date: 2013-08-29
comments: true
categories:
- Asterisk
---

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
; Emergency services, remember to test this
exten => 911,1,NoOp()
  same => n,Set(CALLERID(num)=${CHANNEL:4:11})
  same => n,Dial(SIP/${EXTEN}@flowroute)
{% endcodeblock %}

A word about enabling 911 on your phone: if you're the type that always has your cell phone in your pocket and you're only going to have an office phone (that a significant other or child wont reach for in an emergency), then this may not make sense for you. My wife and I share a cell phone, and it stays in the car. Other than that, the VoIP phones are the only phones in the house, so you better believe I enabled 911.
