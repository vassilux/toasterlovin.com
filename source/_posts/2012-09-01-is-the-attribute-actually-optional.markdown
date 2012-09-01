---
layout: post
title: "Is the attribute actually optional?"
date: 2012-09-01 11:01
comments: true
categories: Rails Testing
---

Ruby on Rails model attributes are optional by default. That is, unless you specify otherwise, an attribute is _not_ required for a model instance to be valid. I like to test that this is, in fact, the case.

``` ruby
test "weight is not required" do
  assert @product.valid?, "product should be valid to begin with"
  @product.weight = nil
  assert @product.valid?, "product should still be valid"
end
```

This might seem too trivial to test, or like we're testing core Rails functionality, but **an optional attribute is a feature of your application**. In other words, it's easy for parts of your app to depend on an attribute being _optional_, which means it's easy to break something by making that attribute _required_ down the road.

<!-- more -->

``` ruby db/migrate/create_products.rb
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.text :name
      t.decimal :weight
    end
  end
end
```

``` ruby test/unit/product_test.rb
class ProductTest < ActiveRecord::TestCase
  test "name is required" do
    @product = products(:one)
    @product.name = nil
    assert !@product.valid?
  end
end
```

``` ruby app/model/product.rb
class Product < ActiveRecord::Base
  validates :name, presence: true
end
```

Here we have a model with two attributes: `name`, which is required, and `weight`, which is optional. We also have a test to make sure that `name` is required. Now we decide that the `weight` attribute shouldn't accept negative numbers, so we add a test and modify the model.

``` ruby
class ProductTest < ActiveRecord::Base
  # more tests
  test "weight should not be negative" do
    @product.weight = -5.4
    assert !@product.valid?
  end
end
```
``` ruby
class Product < ActiveRecord::Base
  validates :part_number, :part_name, presence: true
  validates :weight, numericality: { greater_than_or_equal_to: 0 }
end
```

Easy enough, right? Nope! We just make a small but insidious mistake.

First, because we didn't add `allow_nil: true` to the `numericality` validation, `weight` is now required, but that's not what we wanted to do. We just need to make sure `weight` isn't a negative number; it should still be optional.

Okay, that's not too bad; we might have caught it if it broke some of our other tests. Something else is much more diconcerting; if we're using a fixture without the `weight` attribute set, the `"name is required"` test **isn't actually testing anything**.

``` ruby test/fixtures/products.yml
one:
  name: Cat 5e
```
``` ruby
class Product < ActiveRecord::Base
  # validates :name, presence: true
  validates :weight, numericality: { greater_than_or_equal_to: 0 }
end
```

Boom! Both of our tests still pass, but we're clearly not validating that `name` is required. This should strike fear into your heart; we have a reasonable looking situation that completely fails to do what we expect. In other words, we have built our app on top of quicksand. Maybe it's time to tweak our testing methodology.

There are two ways we could have caught this bug. First, we could have [validated our fixtures][validate-fixtures], which is good hygiene but kind of a separate issue. Second, we could have tested that our optional attribute was actually optional. We'll focus on this second one.

[validate-fixtures]: http://jane.ai/blog/testing-rails-fixtures/

``` ruby
test "weight is not required" do
  assert @product.valid?
  @product.weight = nil
  assert @product.valid?
end
```

Look familiar? This test is how we got this whole blog post started. More importantly, it will start a chain of failing tests that, as we cause them to pass, lead us back to code which does what we expect it to do.

It's kinda wordy for my taste though, and will result in egregious violation of the Don't Repeat Yourself (DRY) principle, especially if we have _a bunch_ of optional attributes. Why not create a helper method so we can test _all_ of our optional attributes in one fell swoop?

``` ruby test/test_helper.rb
class ActiveSupport::TestCase
  # some other stuff
  def self.test_optional_attribute(fixt, *attrs)
    attrs.each do |attr|
      test "#{attr} is optional" do
        fixt = instance_variable_get("@#{fixt}")
        assert fixt.valid?, "#{fixt} isn't a valid fixture"
        fixt.send "#{attr}=", nil
        assert fixt.valid?, "#{attr} should be optional"
      end
    end
  end
end
```

Now we have a nice, succinct way to test all of our optional attributes in a single line of code.

``` ruby
class ProductTest < ActiveSupport::TestCase
  # other tests
  test_optional_attribute :product, :weight # plus as many other attributes as we want to test
end
```

Maybe this all sounds like paranoia to you, but mistakes like this one are shockingly easy to make. Indeed, this is the reason we do any testing at all; we humans, with our inconsistency and tendency toward errors, and computers, with their unforgiving precision, are like oil and water; we don't mix.

And yet, here we are anyway, trying to master computation. Rather than pretend that humans and computers aren't fundamentally incompatible, embrace it. Go all the way. **Test every single assumption.** Just be smart about it; make it easy and succint. Make it joyful.
