---
layout: post
title: "A Rails Testing Checklist"
date: 2012-08-16 21:29
comments: true
categories: Rails Testing
---

Are the fixtures valid?
-

Which attributes are required?
-
I know, this is about the most basic thing you can test for, but this is a checklist; the point isn't to omit stuff that's obvious; it's to make a list of things to check everytime we start writing tests.

Which attributes are not required?
-
This seems counterintuitive at first thought. By default, model attributes are not required, so what's the point in testing default behaviour?

Well let me tell you. First, from a philosophical perspective, that certain attributes are not required is a feature of your application. If you have a model attribute that isn't required, the subsequent code you write will be based on that assumption.

In other words, there is a decent chance that you will write code which depends on that attribute not being required. If you have code which depends on something, then it's a good idea to test that something.

Still skeptical, consider this example (which happened to me):

Which controller actions are publicly available?
-
Again, we're testing something that is the default. Well, so what? Could somebody mess this up by putting `before_filter :require_user` in the controller? Of course they could, so test this already.
Which controller actions are not publicly available?
-
Which associations does the model have?
-
I know, I know, don't test Rails functionality; it's already tested. The thing is, we're not testing whether ActiveRecord associations work, we're testing whether or not we have implemented those associations. Two very different things.

Should the user be able to set any IDs?
-
There are some cases where you definitely want a user to be able to set IDs for a model's associations (think choosing which products are on an order).

There are also cases where the user should be able to set the ID, but some validation occurs (think choosing an address for an order).

Then there are situations where the user should never be able to set the ID (think selecting the user on an order).

Which attributes should the user be able to set?
-
What about 0, negative numbers, decimals and integers?
-
