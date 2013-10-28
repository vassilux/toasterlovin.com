---
layout: post
title: "Deskphone: Routing inbound calls"
date: 2013-08-26
comments: true
published: false
categories:
- Asterisk
---

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

To start, `exten =>` is the syntax we use to specify that we're creating a dialplan for a specific extension. We follow it up with three arguments, separated by commas: the Extension, the Priority, and the Application.

An Extension might typically be a number, like `100` or `101`, but we're using a pattern, `_1NXXNXXXXXX`, which you can always identify by the leading underscore. In a pattern, the digits `0-9` match themselves, `X` matches a single digit from 0-9, `Z` matches a single digit from 1-9, and `N` matches a single digit from 2-9.

This particular pattern matches a North American phone number. That is, it matches the 1 before the area code (`1`), then the area code (`NXX`), then the "local" part of the phone number (`NXXXXXX`). Apparently, the area code and "local" part of the number never start with 0 or 1.

The Priority is used to tell Asterisk what order to execute applications in. We'll gloss over the piority for now, since we're only using one application here, which is...

The `Dial()` Application. Applications in Asterisk look and behave a lot like function calls in C-like programming languages. In fact, I think the word _command_ would have been a better choice. Here we're using `Dial` with a single argument, `SIP/${EXTEN}`, which is a string with a variable interpolated into it.

All SIP channel names start with `SIP/`, so you know this will be dialing a SIP channel. The next part, `${EXTEN}`, interpolates the `EXTEN` variable into the string. `EXTEN` is a special variable that always contains the current extension. So, if the caller dialed 1 (323) 555-1212, the entire string would work out to `SIP/13235551212`.

Since this is the `[inbound]` context, the `EXTEN` variable will always be your _phone number_, which is perfect, since the SIP channel we want to dial -- the one that will ring your phone -- bears your _phone number_ as it's name.

