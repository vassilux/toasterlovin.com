---
layout: post
title: "Returning to Computation"
date: 2012-07-11 09:07
categories:
- Programming
---

Way back in the day, when I was in college, I took a class on basic programming with C++. I only did _okay_ in that class, but the concepts really stuck. There was something about decomposing work into it's fundamental units -- variables, conditionals and loops -- that struck me as _true_.

It was one of those things, a Fundamental Truth About The Universe, like that [genes, not organisms, are the basic units of reproduction][selfish_gene]; or that [evolution, not the id, is what explains human behaviour][evolutionary_psychology]. One of those "Aha!" moments when the haze of confusion is swept away and suddenly _everything makes sense_.

[selfish_gene]: http://en.wikipedia.org/wiki/The_Selfish_Gene "The Selfish Gene"
[evolutionary_psychology]: http://en.wikipedia.org/wiki/Evolutionary_psychology "Evolutionary Psychology"

And so my eyes were opened a little wider.

<!-- more -->

Wandering off into the wilderness
-
Anybody else, coming across a subject which they found so compelling, and which also offered lucrative career opportunities, would have made an immediate course change. But I was missing a couple of crucial skills:

1. Setting goals for myself
2. Making plans to achieve those goals
3. Executing said plans

So this beautiful, golden opportunity floated right past me while I was busy taking the path of least resistance.

Soon, though, it became apparent to me that I needed to learn how to accomplish things, and so began the multiyear process of pulling myself up by the bootstraps: learning how to set goals, make plans and execute those plans by setting a goal, making a plan and executing that plan.

Finding my way back
-
And once I was started in a different direction, the momentum of life kept me going in that direction for a good long while. All told, it took 8 years for programming to find it's way back into my life.

It started off so innocuously. We needed a better way to manage ordering from our overseas factories. We had an Excel spreadsheet listing all of our current products.

<hr class="dialogue" />

"Yo Bossman, check this out," says Rico. "I added our current inventory and monthly sales numbers to the product spreadsheet, then wrote a simple formula to calculate how much of each product we need to buy."

"Sweet, that should save us a bunch of time," replies The Bossman, "but it's kinda confusing. Is there a way to only show a quantity for the producst we _do_ need to order and leave the others blank?"

"Yeah, I'm sure Excel can do that," says Rico as he Googles around a bit. "Hmmm, this should work: `IF(QuantityToBuy=0, "", QuantityToBuy)`."

<hr class="dialogue" />

Woah! A conditional. It's kind of a weird one -- `if` [looks like a call to a function][excel_if], instead of [how I remember it][c++_if] -- but it's a conditional nonetheless.

[excel_if]: http://office.microsoft.com/en-us/excel-help/if-HP005209118.aspx "MS Excel IF Function"
[c++_if]: http://www-numi.fnal.gov/offline_software/srt_public_context/WebDocs/Companion/cxx_crib/if_arglist.html "C++ if syntax"

Building a home for myself
-
Making software with Excel turned out to be alternately deeply strange and downright blissful, but it marked the beginning of my return to Computation. The next big step was converting my company's `<table>` & `<img>` based website to an HTML & CSS based design.

This is the point where things really start to go fractal. It starts with selector specificity. Then it's the CSS box model. Then semantic markup. Soon I realize that I need something to handle templating for the website. Now it's Wordpress and writing little snippets of PHP. Oh, and JQuery happened somewhere in there too.

The thing is, it just doesn't stop. Soon I'm making command line utilites and Coda plugins in Perl. Somewhere along the way I start using MacVim. And Git. Then command line Vim + tmux. And regular expressions, object oriented programming, recursion and Big O notation.

Then I get started with Ruby on Rails, so it's models, views, controllers, migrations, ActiveRecord, partials, REST, routes, HAML, SASS, YAML, unit testing, integration testing, Rake, Capistrano.

It's insane how much stuff you need to cram into your brain to be a decent web developer, and yet web development is just one tiny facet of the whole of Computation; a facet which _isn't even that interesting_.

Where things get really interesting is when you begin to realize that [computation is every where][digital_physics], that there are other, [very different forms of computation][collective_systems], and that some of those are [way better at certain tasks than what we have now][robust_systems].

[digital_physics]: http://en.wikipedia.org/wiki/Digital_physics "Digital Physics - Wikipedia.org"
[collective_systems]: http://edge.org/conversation/ants-have-algorithms "Ants Have Algorithms - Iain Couzin at Edge.org (video & transcript)"
[robust_systems]: http://groups.csail.mit.edu/mac/users/gjs/6.945/readings/robust-systems.pdf "Building Robust Systems - Gerald Jay Sussman"

Thinking about all these things to learn, I can't help but remark to myself, "There's a whole lifetime of work in there for me."

What a wonderful thought!
