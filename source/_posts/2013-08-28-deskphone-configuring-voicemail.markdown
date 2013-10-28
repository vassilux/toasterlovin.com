---
layout: post
title: "Deskphone: Configuring voicemail"
date: 2013-08-28
comments: true
published: false
categories:
- Asterisk
---

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
; Send to voicemail
exten => 1,1,Goto(${CHANNEL:4:11},1)

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

## Configuring outbound email
