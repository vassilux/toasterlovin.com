---
layout: post
title: "Ruby on Rails: Testing for Insecure Admin Routes"
date: 2013-11-02 19:29
comments: true
categories:
- Ruby on Rails
---

I have a Ruby on Rails app with an admin section, as is common for a lot of apps. The admin section is namespaced, so all of the controllers are named something like `Admin::WidgetsController` and the routes all start with `/admin`.

Now, there is no reason, ever, for a non-admin user to do anything in the admin section, so I created an `Admin::ApplicationController` and put a `before_filter` in it to redirect requests from non-admin users to the admin login page. Then, I had all of my `Admin::` controllers inherit from it.

So I'm all set, right?

<!-- more -->

Of course, the weak link in this chain is that I have to remember to make my `Admin::` controllers inherit from `Admin::ApplicationController`. But I'm just a human -- and I frequently use generators to create my controllers -- so I'm always forgetting to do this.

But this is a huge sercurity problem. And, now that I know how to [test for orphaned routes](/ruby-on-rails-testing-for-orphaned-routes/), testing that all of my `/admin` routes are secure is a piece of cake.

In fact, this test caught me forgetting to inherit from `Admin::ApplicationController` not more than 3 hours ago. Enjoy!

``` ruby test/integration/admin/routes_test.rb

require 'test_helper'

class Admin::RoutesTest < ActionDispatch::IntegrationTest
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

  test "admin routes should redirect to admin login page" do
    admin_routes = ROUTES.select { |route| route[:path].starts_with? "/admin" }
    insecure_routes = []

    admin_routes.each do |route|
      begin
        reset!
        # Use the route's verb to access the route's path
        request_via_redirect(route[:verb], route[:path])
        # If we aren't redirected to the admin login, the route is insecure
        insecure_routes << "#{route[:verb]} #{route[:path]}" unless path == admin_login_path
      rescue ActiveRecord::RecordNotFound
        # Since we are blindly submitting "1" for all route params, this error can pop up.
        # If it does, it means that the request got past the `before_filter` and to the
        # code in the controller where it attempts to locate the record in the database.
        # That means this is an insecure route.
        insecure_routes << "#{route[:verb]} #{route[:path]}" unless path == admin_login_path
      rescue AbstractController::ActionNotFound
        # This error means the route doesn't connect to a controller action. This is a
        # problem if it happens, but this is a separate concern and should be tested
        # elsewhere. See my previous post about testing for orphaned routes.
      end
    end

    # Fail if we have insecure routes.
    assert insecure_routes.empty?, "The following routes are not secure: \n\t#{insecure_routes.uniq.join("\n\t")}"
  end
```
