---
layout: post
title: "Ruby on Rails: Testing for Orphaned Routes"
date: 2013-10-30 20:50
comments: true
categories:
- Ruby on Rails
---

Over time, one of my large Ruby on Rails apps has accumulated a lot of cruft in it's routes file. In addition to the normal entropy that accumulates in any large app, I didn't really have a solid grasp of how routes worked when I started on this app, so it was looking pretty bad.

Well, I recently went through `routes.rb` and did some major housekeeping. But this app has a bunch of models, with controllers for most of them in both the default namespace and an admin namespace.

<!-- more -->

So, even after I had spent the better part of a day cleaning house, my spidey-sense was still tingling. I just didn't feel like I could be _sure_ that everything was copacetic. And I've learned the hard way that this means it's time for some tests...

Since routes are the first interface to a web app, I figured a good place to start would be to make sure there were no orphaned routes -- routes that do not lead to a controller action. Also, I wanted a _single_ test that would test _all_ of my routes, so there would be no forgetting to add tests down the road.

Here's that test.

``` ruby test/integration/routes_test.rb

require 'test_helper'

class RoutesTest < ActionDispatch::IntegrationTest
  ROUTES = Rails.application.routes.routes.map do |route|
    # Turn the route path spec into a string:
    # - Remove the "(.:format)" bit at the end
    # - Use "1" for all params
    path = route.path.spec.to_s.gsub(/\(\.:format\)/, "").gsub(/:[a-zA-Z_]+/, "1")
    # Route verbs are stored as regular expressions; convert them to symbols
    verb = %W{ GET POST PUT PATCH DELETE }.grep(route.verb).first.downcase.to_sym
    # Return a hash with two keys: the route path and it's verb
    { path: path, verb: verb }
  end

  test "no orphaned routes exist" do
    orphaned_routes = []

    ROUTES.each do |route|
      begin
        reset!
        # Use the route's verb to access the route's path
        request_via_redirect(route[:verb], route[:path])
      rescue AbstractController::ActionNotFound
        # If we get this error, this is an orphaned route
        orphaned_routes << "#{route[:verb]} #{route[:path]}"
      rescue ActionController::RoutingError, ActiveRecord::RecordNotFound
        # We want to rescue these errors:
        # - ActionController::RoutingError happens because of the /assets route
        # - ActiveRecord::RecordNotFound happens because we are using 1 for all the route params
      end
    end

    # Fail if we have any orphaned routes
    assert orphaned_routes.empty?, "The following routes lead to nowhere: \n\t#{orphaned_routes.uniq.join("\n\t")}"
  end
end
```
