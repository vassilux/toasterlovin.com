---
layout: post
title: "Deskphone: The final dialplan"
date: 2013-08-30
comments: true
published: false
categories:
- Asterisk
---

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
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
