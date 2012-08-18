---
layout: post
title: "Testing Rails Fixtures"
date: 2012-08-18 16:29
comments: true
categories: Rails Testing
---

A common pattern in my Rails unit tests is to start with a valid instance of a model, make a change to the attribute I'm testing, then assert that the model instance is no longer valid.

``` ruby
test "weight is required" do
  @prod_one = products(:one)
  @prod_one.weight = nil
  assert !@prod_one.valid?
end
```

Simple enough; I don't think we're treading new ground here. The problem is that this test assumes the fixture is valid. **If the fixture isn't valid, the test will still pass, but it won't actually be testing anything**. Yikes! Better to assert that the fixture is valid.

<!-- more -->

``` ruby
test "weight is required" do
  @prod_one = products(:one)
  assert @prod_one.valid?
  @prod_one.weight = nil
  assert !@prod_one.valid?
end
```

That's definitely better, but we've got a bunch of tests to write; it's going to be tiresome (and error-prone) to have to assert the validity of our fixtures in each test. Plus, we want to keep our tests laser focused on what they're actually testing. Why not just get it all out of the way at once?

``` ruby
setup do
  @prod_one = products(:one)
end

test "fixtures are valid" do
  assert @prod_one.valid?
end

test "weight is required" do
  @prod_one.weight = nil
  assert !@prod_one.valid?
end
```

Look at how tight that test is. There's nothing in there to distract you from immediately comprehending what's being tested. Truly a thing of beauty. There _is_ still one thing that's bothering me though...

We're going to be testing our fixtures this way a lot -- at least once for each unit and functional test class. It's tiresome to actually have to write an entire test each time we want to do this. Why not just write a helper method so we can turn this into a one liner?

``` ruby test/test_helper.rb
class ActiveSupport::TestCase
  # some other stuff

  def self.test_valid_fixture(*fixts)
    fixts.each do |fixture|
      test "@#{fixture} fixture is valid" do
        fixture = instance_variable_get("@#{fixture}")
        assert fixture.valid?, "Fixture is invalid: #{fixture.errors.full_messages.join(', ')}"
      end
    end
  end

end
```

Now we can be concise and expressive in our test class, which allows us to conserve our brainpower for where it's really needed.

``` ruby test/unit/product_test.rb
class ProductTest < ActiveRecord::TestCase
  setup do
    @prod_one = products(:one)
    @prod_two = products(:two)
  end

  test_valid_fixture :prod_one, :prod_two

  test "weight is required" do
    @prod_one.weight = nil
    assert !@prod_one.valid?
  end
end
```
