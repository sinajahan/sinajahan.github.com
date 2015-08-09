---
layout: post
title: Couple of points on Feature Toggling
code: feature-toggling
subtitle: Keep going without branching
---

{{ page.title }}
================

By Sina Jahan {{ page.date | date_to_string }}

One of the ideas of agile development is "Trunk Development" and with
that comes the idea of feature toggling. If you are not familiar with the idea Fowler has demonstrated it [here](http://martinfowler.com/bliki/FeatureToggle.html).

As we are implementing new features at ClearFit we are using them
heavily and thought I would share a couple of ideas that we ran into.


Lets go with a simple example. We are going to use
[rollout gem from James Golick](https://github.com/bitlove/rollout). Ryan Bates has a good [episode](http://railscasts.com/episodes/315-rollout-and-degrade) on this.

Imagine we are developing a new flow for users and we need a feature
toggle for it. Inside one of our controller we will have something like
this:

    def create
      if $rollout.active? :new_flow, current_user
        # new flow
      else
        # old flow
      end
    end


If the feature is active for this user send them to the new flow.
Otherwise send the to the old flow.

Not only user based grouping
----------------------------------------------------

If you are using rollout you get the feeling that everything is tied to
a user. It doesn't have to be. Here I am defining a group based on a
random model object: Product.

    $rollout.define_group(:new_flow_productions) do |product|
      product.code.match(/X.*$/)
    end

And here is how you check if a feature is active for a set of products:

    $rollout.active? :new_flow, Product.find(id)

As you can see rollout doesn't care about the criteria of your group
definition. In fact here is the source code definition of how it works
from [here](https://github.com/bitlove/rollout/blob/master/lib/rollout.rb):

    def active?(rollout, user)
      if user.nil?
        @percentage == 100
      else
        user_in_percentage?(user) ||
          user_in_active_users?(user) ||
            user_in_active_group?(user, rollout)
      end
    end

And here is the definition of `user_in_active_group?`:

    def active_in_group?(group, user)
      f = @groups[group.to_sym]
      f && f.call(user)
    end

As you can see it is only deligating to your group definition.



Mock out `active?` method in your tests
---------------------------------------------------

As soon as you have a feature toggle in your source code you have to
have test for both situation of feature toggle being on and off. Let's
take a look at an example.

Just mock the `active?` method of rollout. Here is how your rspec will look like:

    context 'new flow' do
      before do
        $rollout.should_receive(:active?).and_return true
      end

      it 'new flows'
        #...
      end
    end

    context 'old flow' do
      before do
        $rollout.should_receive(:active?).and_return false
      end

      it 'old flows'
        #...
      end
    end

You can remove the whole old context as soon as you are sure the feature
is staying and the old path is no longer supported.


Using hooks for Cucumber
------------------------

If you are using Cucumber you can tag the features/scenarios that use
the feature toggle and use a hook file to set the toggle to on and off.

Your feature file will look like this:

    @new_flow
    Scenario: customer goes through the new flow
      Given ...

And then you can have something like this in your `features/support`
folder:

    Before('@new_flow') do
      $rollout.activate_globally :new_flow
    end

    After('@new_flow') do
      $rollout.deactivate_globally :new_flow
    end

As you can see here we are turning it before test run and disabling it
right after tests run finishes.


Clean up after feature toggle release
-------------------------------------

Usually you keep the both flows until you migrate 100% of your
users to the new flow. Having all these if/elses in the code will become
annoying for maintenance and is a broken window. Have a process to add a cleanup task to the
backlog as soon as you introduce a new feature toggle to the code base.
As you continue with next sprints you will probably forget that you have
that nasty if/else in the code. That cleanup task will just remind you
to do some cleanup.

