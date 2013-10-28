---
layout: post
title: "Deskphone: Routing outbound calls"
date: 2013-08-27
comments: true
published: false
categories:
- Asterisk
---

Your outbound dialplan is actually pretty simple. We just take the number you dial from your phone and place a call to that number via Flowroute. Now, that might sound like no big deal, but when you place your first call over the PSTN, you'll feel like a total badass.

The only other thing we need to do is set your caller ID before we send the call to Flowroute. That's right, caller ID isn't set automatically. Actually, I'll let you in on a little secret: caller ID is totally a trust-based system. You can set your caller ID to whatever you want.

Alright, put this into `extensions.conf` and then we'll go through what it does:

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
exten => _1NXXNXXXXXX,1,NoOp()
  same => n,Set(CALLERID(num)=${CHANNEL:4:11})
  same => n,Dial(SIP/${EXTEN}@flowroute)
{% endcodeblock %}

The first thing you'll notice is that this context is called `[outbound]`. You may recall that your phone is configured to enter The Dialplan in this context. That's as it should be. Your phone will be making outbound calls and `[outbound]` will contain a dialplan for outbound calls.

The next thing you'll notice is that we're once again using `exten =>` to define a dialplan for a _pattern_, instead of for a specific extension. If you look closely, you'll notice that it's the exact same pattern we specified in our `[inbound]` context: a North American phone number.

This makes sense. You'll be dialing North American phone numbers, so this pattern will catch any number that you dial.

But it is at this point that the `[outbound]` dialplan starts to diverge from the `[inbound]` one. We have the same priority, 1, but then you see the `NoOp()` application, followed by a couple of indented lines that start with `same =>`.

What's going on here is that you've encountered your first multi-line dialplan.

`NoOp()` is an application that does nothing; it just passes control to the next priority in the dialplan for the extension (or pattern, in this case). Since the first line has priority 1, control passes to the line that specifies priority 2 for the `_1NXXNXXXXXX` pattern (think of the priority as the order).

But wait, we have no such line. All we have are those other two lines which start with `same => n`.

Here's what's going on. `same =>` is special syntax which specifies that a line in a diaplan applies to the same extension (or pattern) as the previous line. So, the second and third lines both apply to same extension as the first -- `_1NXXNXXXXXX`.

Since `same =>` has an implicit extension, it only takes two arguments instead of the three that `exten =>` requires. The first argument is the priority. For this, instead of numbers we use a lowercase n.

The lowercase n stands for next. As in, this priority is the next priority after the previous line. It's purpose is to save you from having to remember to adjust all of your priorities every time you insert a line in a dialplan. If you are shaking your head because this seems especially verbose and unnecessary, know that you are in good company.

Finally, the two `same =>` lines each specify an application to run. We'll come back to those in a bit. First, I want to explain why we use `NoOn()`, an application which does nothing, and the `same =>` syntax.

Essentially, the answer is that it makes multiline dialplans easier to read. We could rewrite this dialplan like this:

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
exten => _1NXXNXXXXXX,1,Set(CALLERID(num)=${CHANNEL:4:11})
exten => _1NXXNXXXXXX,2,Dial(SIP/${EXTEN}@flowroute)
{% endcodeblock %}

But here we've repeated the extension pattern twice and we've manually entered the priority. As you can imagine, this starts to become unwieldy once we have more than just a handful of steps in the dialplan.

Instead, we specify the extension only once, on the first line. Then we use the `NoOp()` application as a way to signify that this line is just a label for the extension and the following lines should be consulted for the actual contents of dialplan.

Then, the following lines -- the ones that use `same =>` -- dispense with specifying the extension and priority. Instead, they contain only the actual contents of the dialplan.

I agree with you, this is far from ideal. There is way too much repetition and adornment for my taste, but it's what we've got.

So, after that digression, what are we actually doing with this dialplan. Simple. The first instruction, `Set(CALLERID(num)=${CHANNEL:4:11})`, set's the caller ID. The second instruction, `Dial(SIP/${EXTEN}@flowroute)`, dials the phone number.

Let's start with the first line. `Set()` is an application that assigns values to variables. You can assign a value to a new variable, as we will do later on in this guide, or you can assign a value to an existing variable, which is what we are doing here.

`CALLERID(num)` is a special variable used to access the numeric portion of the caller ID. We won't bother setting the text part of the caller ID. The `=` symbol is part of the syntax used in the `Set()` application. Finally, `${CHANNEL:4:11}` is the value we are assigning to `CALLERID(num)`.

As you may recall, `${}` instructs Asterisk to interpolate the value of the variable inside the curly brackets into the surrounding statement. So, we are taking the value of `CHANNEL:4:11` and assigning it to the numeric part of the caller ID.

`CHANNEL` is a special variable that contains the name of the channel that the call is originating from. Since this is a call originating from your phone, the channel name will be something like `SIP/13235551212-SOMEOTHERNUMBERS`, but we don't want to use that whole thing for the caller ID.

That's where the `:4:11` part comes in. Any time you're interpolating a variable in Asterisk, you can use a single colon to specify how many characters you want to drop from the interpolated variable and an optional second colon to specify how many characters you want to interpolate total.

In this case, we're dropping the first 4 charactesr, the `SIP/` part, and keeping the following 11 characters, which works out to your phone number, since North American numbers all have 11 digits(1 + 3 digit area code + 7 digit local number).

So, on to the second line. This should look fairly familiar at this point. Here it is again, for reference:

{% codeblock lang:ini %}
  same => n,Dial(SIP/${EXTEN}@flowroute)
{% endcodeblock %}

You've seen the `Dial()` application before. You know that `SIP/` tells Asterisk that we're placing a call to a SIP channel. You know how variable interpolation works. You even know that the `EXTEN` variable contains the dialed extension -- in this case, the number you dialed from you phone.

The only new thing is the @ symbol, but that's pretty straightforward, we're just dialing an extension on the `[flowroute]` channel. And that's essentially how to we send calls to the PSTN via Flowroute. Boom!

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
; Add 1 before area code
exten => _NXXNXXXXXX,1,Goto(1${EXTEN},1)
{% endcodeblock %}

{% codeblock /etc/asterisk/extensions.conf lang:ini %}
[outbound]
; Strip plus sign
exten => _+X!,1,Goto(${EXTEN:1},1)
{% endcodeblock %}
