---
layout: post
title: "A Rails Testing Checklist"
date: 2012-08-16 21:29
comments: true
categories: Rails Testing
---

Are the fixtures valid?
-
A common pattern in my unit tests is to start with a valid instance of a model, then make a change to whichever attribute I'm testing, then assert that the model instance is no longer valid.

``` ruby
test "weight is required" do
  @prod_one = products(:one)
  @prod_one.weight = nil
  assert !@prod_one.valid?
end
```

Simple enough; I'm sure we've all done this a million times. This all breaks down, though, if the model instance wasn't valid to begin with. Since testing is about creating a solid foundation to build on, I like to begin each test class by testing that all my fixtures are valid.

``` ruby
setup do
  @prod_one = products(:one)
end

test "fixtures are valid" do
  assert @prod_one.valid?
end
```
I even made a helper method so I can test all of my fixtures in one line of code.

``` ruby
test_valid_fixture :prod_one, :prod_two
```

Which attributes are required?
-
I know, this is about the most basic thing you can test for, but this is a checklist; the point isn't to omit stuff that's obvious, it's to help us be methodical and machine-like. Anyway, I like to get all this done in one line of code:

``` ruby
test_required_attribute :prod_one, :part_number, :description, :price
```

Which attributes are not required?
-
By default, model attributes are not required. So what's the point in testing default behaviour? Well let me tell you. If you don't require a model attribute, it becomes more than just the default behaviour: _it becomes a feature of your application._

In other words, code you write later on will be based on the assumption that the attribute isn't required. If you have code which depends on something being true, then you should test that something keeps being true. The whole point of testing is to keep the ground from shifting beneath our feet.

You probably already guessed that I'm getting this all done in one line of code (yeah, I really took testing and Don't Repeat Yourself to heart).

``` ruby
test_optional_attribute :prod_one, :weight, :height, :width, :length
```

Which controller actions are publicly available?
-
Again, we're testing something that's the default. Well, so what? Could somebody mess this up by putting `before_filter :require_user` in the controller? Of course they could, so test this already.

Which controller actions are not publicly available?
-
I hope the _necessity_ of this one is self explanatory; it's the _means_ which I really want to think about for a bit. The standard way to control user access in a controller is with something along the lines of:

``` ruby
UsersController < ApplicationController
  before_filter :require_user, only: [:edit, :update, :destroy]
  # a bunch of methods
end
```

Of course, you could always put your access control code in the individual controller methods themselves:

``` ruby
UsersController < ApplicationController
  def create
    require_user || return
    # some business logic
  end
end
```

But you don't really see much of that in all the Rails demos around the web. It's a little messy to have access control code mixed in with business logic. Much better to put all the access control code right up at the top where it's hard to miss and easy to find.

So, why not take the same approach with testing? Make a nice little helper method and test all you access control in one line. By the way, noticing a trend here?

Which associations does the model have?
-
I know, I know, don't test functionality provided by Rails; it's already tested. The thing is, we're not testing whether our ActiveRecord associations work, we're testing whether or not we have implemented those associations. Two very different things.

The quick and dirty way to test this is to assert that the model responds to the name of the association. So if we declare an assocation on the model like this:

``` ruby
Order << ActiveRecord::Base
  has_many :line_items
end
```
Our test should look something like this:

``` ruby
OrderTest << ActiveRecord::TestCase
  test "has many line items" do
    assert @order.respond_to?(:line_items)
  end
end
```

Yeah, that's kind of ghetto and roundabout, but it's something. More ideal would be to actually test that it's an ActiveRecord assocation and not just some random method. I know Shoulda has some nice methods I'm working on it.

Should the user be able to set any IDs?
-
There are some cases where you definitely want the user to be able to set IDs for a model or it's associations. For example, your ecommerce app needs the user to tell it which products to put in the shoping cart. For this the user will need to be able to set the product ID.

There are also cases where the user should be able to set an ID, but some validation needs to happen to ensure they aren't doing anything malicious. To continue our example, your app needs the user to tell it which of their addresses to ship the order to. For this the user will need to be able to set the address ID, but your app needs to make sure the ID isn't for an address that belongs to another user.

Then there are situations where the user should never be able to set the ID. I hope you like ecommerce examples, 'cause we've got another one comin'. Your app needs to store the user ID for an order when it gets one, but you definitely don't want the user setting that puppy for you.

Which attributes should the user be able to set?
-

The previous item on the checklist is really just a special case of this one. To go back to our ecommerce example (old Lady Commerce, she just keeps on giving), your app needs the user to specify which products to add to the cart, and what quantity of each item to add, but you don't want your users choosing which prices they should pay. So make sure you've got this stuff handled, big boy.

What about 0, negative numbers, floats and integers?
-

A funny thing about real life is that we mostly deal with _positive integers_. Think about it. How many days are in this month? What time did you eat lunch today? How many times have you watched Trainspotting? (I've watched it lots, if you're wondering) The answer to each of those questions is a positive integer.

And yet, there's so much more to numbers. Remember those trusty number lines you learned about in school? Yeah, 0 is in the middle, which means that there are just as many negative as positive numbers. Also, between any two consecutive integers you can fit an infinite number of floats.

So there are infinity positive integers, infinity negative integers and infinity infinity floats. Plus zero. Anyway, the point I'm trying to make is that there are a whole lot of numbers out there and positive integers are a minority. Since most software models something in real life (the domain of positive integers), make sure you're getting the kind of numbers you want.
