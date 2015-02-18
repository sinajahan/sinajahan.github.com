---
layout: post
title: Some handy tools to refactor your view logic in Rails 3
---

# 5 GUI Patterns That help to have skinny rails classes

Big classes can create maintainability problems in application. They are hard to change and everyone on your team hates to touch them. You start with clean beautiful class and before you know it has become hundred lines of code. Big classes are usually a sign that your class is doing too many things. They violate the [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) and they should be refactored. Here I am going to review a couple of refactoring patters specific to user interface that you can have in your toolbox. Applying these kind of extracting logic from classes should be an ongoing process in any code base.

## View Helpers from ActionController

When I wrote my "hello world" application in Rails I fell in love with [view helpers](http://api.rubyonrails.org/classes/ActionController/Helpers.html). You are coding in your erb file and have some logic specific to view. Rails has already provided a place for that. Yay! Lets add a helper in `app/helpers`. For example a simple logic to handle caption of a label:

{% highlight ruby linenos %}
  module CandidateHelper
  # app/helpers/candidate_helper.rb
  ...
    def caption(candidate)
      candidate.distorted? ? "Application Distortion" : "Application Pass"
    end
  ...
  end

{% endhighlight %}

It was cool to put this in a helper and forget about it. Rails was "magically" loading my method in erb and I could simple call it:

{% highlight ruby linenos %}
  <% @candidates.each do |candidate| %>
    <%= caption candidate %><br>
  <% end %>
{% endhighlight %}

After all this was all "convention over configuration" of Rails. After Rails 3 you can easily write tests for your helpers too:

{% highlight ruby linenos %}
  require 'test_helper'

  class WelcomeHelperTest < ActionView::TestCase
    test 'returns the correct caption' do
      c = Candidate.new(distorted:true)
      assert_equal "Application Distortion", caption(c)
    end
  end
{% endhighlight %}

But lets take a closer look now with your object oriented expert hat on. This helper is a ruby module. That means no inheritance, hard reuse...and you don't get to work with your lovely objects.

## View Objects or Presentors

The idea is very simple: Your controller will delegate the logic of view
related functions to a separate poro object.

For example imagine the logic of showing the default candidate avatar when user
has not provided any image.

{% highlight ruby linenos %}
  class CandidateView
    def initialize(candidate)
      @candidat = candidate
    end

    def avatar_name
      if candidate.avatar_image_name.present?
        candidate.avatar_image_name
      else
        "default.png"
      end
    end

  end
{% endhighlight %}

For cleaner codebase I have created a folder `app/view_objects` in my
rails app and have asked rails to load classes in that folder in my
`application.rb` file:

{% highlight ruby linenos %}
  ...
  class Application < Rails::Application
    # Custom directories with classes and modules you want to be autoloadable.
    config.autoload_paths += %W(
      #{Rails.root}/app/view_objects
    )
{% endhighlight %}

And then I use it in my Controller:

{% highlight ruby linenos %}
  class WelcomeController < ApplicationController
    ...
    def index
      @view = CandidateView.new(Candidate.find(params[:id]))
    end
  end
{% endhighlight %}

And finally my view will just call the required method on the `@view`
object:

{% highlight ruby linenos %}
  <%= @view.avatar_name =%>
{% endhighlight %}

As you saw in above example we are extracting view logic to a separate
class without any need from Rails magic. Sience we are using plain old
ruby object there is no magic required in testing these objects either.


## Decorators

Decorator pattern was originally introduced by [Gang of Four's book](http://www.amazon.ca/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612).
It is a design pattern that allows behaviour to be added to an
individual object.

In our view object example we are initializing a new class and having
methods implement view logic. In decorator pattern for view templates we
are wrapping the active record model class and decorating it with new
behaviour.

There are multiple ways to implement a decorator pattern in Rails. There
is an awesome gem called [draper](https://github.com/drapergem/draper)
only for this purpose. Rails cast has [an episode](http://railscasts.com/episodes/286-draper) on it too which he goes
through the process of refactoring a heavy view template and helper to
decorator design pattern.

The idea of using a decorator design pattern for extracting view logic
is simple. You have your rails model class that has all the data you
want. Now lets add some methods as behavior to it for view and have
them separated in their own class.

## Exhibit pattern

The idea came from Avdi Grim in his book [Objects on Rails](http://objectsonrails.com/). The main power of exhibit pattern is when you want to render different partials based on some view logic. Imagine the following code: rendering different partial templates based on a flag on candidate.

{% highlight ruby linenos %}
<% if @candidate.distorted? %>
  <%= render 'distorted' %>
<% else %>
  <%= render 'pass' %>
<% end %>
{% endhighlight %}

and as usual you put your partials like this:

{% highlight ruby linenos %}
...
 ▾ views/
    ▾ candidates/
        _distorted.html.erb
        _pass.html.erb
        ...

{% endhighlight %}

This is almost always a bad idea. Putting view logic inside your view
file is hard to test and hard to maintain. Now lets see how exhibit
pattern can be applied here. Model objects are contain business
knowledge and view template contain the look and feel. Think of an
exhibit as a middle object between these two. It takes the model class
and added some extra behavior to it. This added behavior in our
example is the logic to render different partial views.

Lets add our exhibitors. I have created a folder called exhibits under
app and have added it in `config.autoload_path` so rails picks it up.
Here is the exhibit class for distorted:

{% highlight ruby linenos %}
require 'delegate'

class DistortedExhibit < SimpleDelegator
  def initialize(model, context)
    @context = context
    super(model)
  end

  def render_body
    @context.render(partial: "distorted")
  end
end
{% endhighlight %}

As you can see we are using the ruby simple delegator class. It simply
wraps class and delegates all method calls to the wrapped class. Now you
can simple add your additional behaviour to the class.

In our example I have wrapped the `candidate` model with additional
method for render body for distorted candidate. Lets add an exhibit for
pass candidates too:

{% highlight ruby linenos %}
require 'delegate'

class PassExhibit < SimpleDelegator
  def initialize(model, context)
    @context = context
    super(model)
  end

  def render_body
    @context.render(partial: "pass")
  end
end
{% endhighlight %}

Now the challenge is where to put the logic of using which exhibitor.
Instead of scattering the if/else statements lets have a single helper
witch knows which decided which exhibitor(s) apply to a given object:

{% highlight ruby linenos %}
module ExhibitsHelper

  def exhibit(model, context)
    case model.class.name
    when 'Candidate'
      if model.distorted?
        DistortedExhibit.new(model, context)
      else
        PassExhibit.new(model, context)
      end
    else
      model
    end
  end
end
{% endhighlight %}

I have also added exhibits as a helper to `application_controller.rb` file:

{% highlight ruby linenos %}
class ApplicationController < ActionController::Base
  ...
  helper :exhibits
end
{% endhighlight %}

Now we can have a much simpler view template:

{% highlight ruby linenos %}
<% candidate = exhibit(@candidate, self) %>
<%= candidate.render_body %>
{% endhighlight %}
