---
layout: post
title: "Returning to Computation"
date: 2012-07-03 19:58
comments: true
categories:
published: false
---

Way back in the day, when I was in college, I took a couple of computer science classes as part of my business degree; one on basic programming with C++ and another on SQL and database design.

I only did _okay_ in those classes, but the concepts really stuck with me. There was something about the way real-world objects and their relationships are modelled in a relational database that struck me as fundamentally _true_. So, too, for decomposing work into variables, conditionals and loops.

I had certainly attained no mastery in either topic, but I had glimpsed what was possible, and there was no undoing that knowledge. It had shifted the way I perceived the world in a subtle, yet powerful way.

It was one of those things, a fundamental truth about the Universe, like that [genes, not organisms, are the basic units of reproduction][selfish_gene]. Or that [evolution, not the id, ego or superego, is what explains human behaviour][evolutionary_psychology]. One of those "Aha!" moments when the haze of confusion is swept away and suddenly everything is _clear_.

I had a new prism through which to view the world and it probably should have been obvious that a subject which affected my thinking so deeply was probably worth exporing further, but I had other priorities at the time, like getting college done as quickly and easily as possible, for which purposes, by the way, a business degree is well suited, having little writing and little math.

Anyway, there were other impediments; I still hadn't developed the ability to make a plan and then execute it. My entire childhood and most of college had been spent half-assing things and getting by (barely) on my innate ability. So the 8 years after college were spent bootstrapping myself out of the morass of not having the skillset to set goals and then achieve them.

Why'd that take me 8 years? Well, I was also learning some other important stuff, like how to carry on relationships, but it's a classic pulling yourself up by the bootstraps scenario. In order to learn the skillset of setting goals and achieving them, I had to set a goal (learning this skillset) and then achieve it. All very meta, I know, but it's hard too; so please, make sure your children know how to do this.

Anyway, I'm getting a bit off track here, the gist is that the core, basic concepts of computing stuck with me throughout this 8 year odyssey of finding myself. They were lying dormant inside my brain, waiting for the right moment to come awake. And sure enough it happened.

It started off so innocuously. We need a better way to manage ordering from our overseas factories. We have an Excel spreadsheet listing all of our current products. What if we add our inventory and sales data and then write a simple formula to calculate how much of each product we need to buy? Easy enough.

Okay, but it's confusing seeing zeros for all these products we don't need to order, is there a way to only show a quantity for the ones we _do_ need to order and leave the others blank? Yeah, I'm sure Excel can do that. Google around a bit; okay, so `IF(QuantityToBuy=0, "", QuantityToBuy)`.

Woah! A conditional. Kind of weird though; `if` [looks like a call to a function][excel_if], [instead of how I remember it][c++_if]. It turns out that in Excel, [everything is a function][fp], [which isn't so strange to me now][sicp], but was very strange at the time.

Actually, programming in Excel is a little stranger than that: functions are the only tool explicitly available to you. It's up to you to figure out how to create variables, loops, and procedures (If you're curious, individual cells are variables, rows are items in a collection, and columns are individual steps in a procedure).

So marked the beginning of my return to Computation and, strangely enough, it was with functional programming, though I was oblivious to it at the time. The next big step came in the form of converting our `<table>` & `<img>` based website to an HTML & CSS based Wordpress template.

  It should have been blindingly obvious at the time that I was meant to play with computation, but, being young, I lacked both insight about myself and the wherewithal to make a plan and stick with it. So in the intervening 9 years, while I worked on some other stuff (link to finding myself), computation receded into the recesses of my mind.

[selfish_gene]: http://en.wikipedia.org/wiki/The_Selfish_Gene "The Selfish Gene"
[evolutionary_psychology]: http://en.wikipedia.org/wiki/Evolutionary_psychology "Evolutionary Psychology"
[excel_if]: http://office.microsoft.com/en-us/excel-help/if-HP005209118.aspx "MS Excel IF Function"
[c++_if]: http://www-numi.fnal.gov/offline_software/srt_public_context/WebDocs/Companion/cxx_crib/if_arglist.html "C++ if syntax"
[sicp]: http://mitpress.mit.edu/sicp/full-text/book/book.html "Structure and Interpretation of Computer Programs"
[fp]: http://en.wikipedia.org/wiki/Functional_programming "Functional Programming"
