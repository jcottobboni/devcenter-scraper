---
title: Specifying a Ruby Version
slug: ruby-versions
url: https://devcenter.heroku.com/articles/ruby-versions
description: Specifying a particular version of Ruby via your app's Gemfile.
---

## Selecting a version of Ruby

<div class="callout" markdown="1">
You'll need to install `1.2.0` of bundler to use the `ruby` keyword.
</div>
<div class="callout" markdown="1">
See a complete list of supported <a href="https://devcenter.heroku.com/articles/ruby-support#ruby-versions">Ruby versions</a>
</div>

You can use the `ruby` keyword of your app's `Gemfile` to specify a particular version of Ruby.

    :::ruby
    source "https://rubygems.org"
    ruby "1.9.3"
    # ...

When you commit and push to Heroku you'll see that Ruby `1.9.3` is detected:

    -----> Heroku receiving push
    -----> Ruby/Rack app detected
    -----> Using Ruby version: 1.9.3
    -----> Installing dependencies using Bundler version 1.2.1
    ...

Please see [Ruby Support](ruby-support#ruby-versions) for a list of available versions.

<div class="warning" markdown="1">
If you were previously using `RUBY_VERSION` to select a version of Ruby, please follow the instructions above to specify your desired version of Ruby using Bundler.
</div>

## Troubleshooting

### Migration
Applications that migrate to a non-default version of Ruby should have `bin` be the first entry in their `PATH` config var. This var's current value can be determined using `heroku config`.

    :::term
    $ heroku config -s | grep PATH
    PATH=vendor/bundle/ruby/1.9.1/bin:/usr/local/bin:/usr/bin:/bin

If absent or not the first entry, add `bin:` to the config with `heroku config:set`.

    :::term
    $ heroku config:set PATH=bin:vendor/bundle/ruby/1.9.1/bin:/usr/local/bin:/usr/bin:/bin

If the `PATH` is set correctly you will see the expected version using `heroku run`:

    :::term
    $ heroku run "ruby -v"
    Running `ruby -v` attached to terminal... up, run.1
    ruby 1.9.2p290 (2011-07-09 revision 32553) [x86_64-linux]

If the `PATH` is not setup correctly, you might see this error:

    Your Ruby version is 1.9.2, but your Gemfile specified 1.9.3

### Bundler

If you're using a Bundler `1.1.4` or lower you'll see the following error:

    undefined method `ruby' for #<Bundler::Dsl:0x0000000250acb0> (NoMethodError)

You'll need to install bundler `1.2.0` or greater to use the `ruby` keyword.

    $ gem install bundler