capybara-screenshot gem
=======================

[![Build Status](https://travis-ci.org/mattheworiordan/capybara-screenshot.png)](https://travis-ci.org/mattheworiordan/capybara-screenshot)
[![Code Climate](https://d3s6mut3hikguw.cloudfront.net/github/mattheworiordan/capybara-screenshot.png)](https://codeclimate.com/github/mattheworiordan/capybara-
screenshot)
[![Gem Version](https://badge.fury.io/rb/capybara-screenshot.svg)](http://badge.fury.io/rb/capybara-screenshot)

Using this gem, whenever a [Capybara](https://github.com/jnicklas/capybara) test in [Cucumber](http://cukes.info/), [Rspec](https://www.relishapp.com/rspec) or Minitest  fails, the HTML for the failed page and a screenshot (when using [capybara-webkit](https://github.com/thoughtbot/capybara-webkit), [Selenium](http://seleniumhq.org/) or [poltergeist](https://github.com/jonleighton/poltergeist)) is saved into $APPLICATION_ROOT/tmp/capybara.

This is a huge help when trying to diagnose a problem in your failing steps as you can view the source code and potentially how the page looked at the time of the failure.

_Please note that Ruby 1.9 is required to use this Gem.  For Ruby 1.8 support, please see the [capybara-screenshot Ruby 1.8 branch](https://github.com/mattheworiordan/capybara-screenshot/tree/ruby-1.8-support)_

Installation
-----

### Step 1

Using Bundler, add the following to your Gemfile

    gem 'capybara-screenshot', :group => :test

or install manually using Ruby Gems:

    gem install capybara-screenshot

### Step 2

For **Cucumber**, in env.rb or a support file, please add:

    require 'capybara-screenshot/cucumber'

For **RSpec**, in spec_helper.rb or a support file, after the require for 'capybara/rspec', please add:

    # you should require 'capybara/rspec' first
    require 'capybara-screenshot'
    require 'capybara-screenshot/rspec'

For **Minitest**, typically in 'test/test_helper.rb', please add:

    require 'capybara-screenshot/minitest'

For **Test::Unit**, typically in 'test/test_helper.rb', please add:

    require 'capybara-screenshot/testunit'

Manual screenshots
----

If you require more control, you can generate the screenshot on demand rather than on failure. This is useful
if the failure occurs at a point where the screen shot is not as useful for debugging a rendering problem. This
can be more useful if you disable the auto-generate on failure feature with the following config

	Capybara::Screenshot.autosave_on_failure = false

Anywhere the Capybara DSL methods (visit, click etc.) are available so too are the screenshot methods.

	screenshot_and_save_page

Or for screenshot only, which will automatically open the image.

	screenshot_and_open_image

These are just calls on the main library methods.

    Capybara::Screenshot.screenshot_and_save_page
    Capybara::Screenshot.screenshot_and_open_image


Driver configuration
--------------------

The gem supports the default rendering method for Capybara to generate the screenshot, which is:

	page.driver.render(path)

There are also some specific driver configurations for Selenium, Webkit, and Poltergeist. See [the definitions here](https://github.com/mattheworiordan/capybara-screenshot/blob/master/lib/capybara-screenshot.rb). The Rack::Test driver, Rails' default, does not allow
rendering, so it has a driver definition as a noop.

Capybara-webkit defaults to a screenshot size of 1000px by 10px. To specify a custom size, use the following option:

    Capybara::Screenshot.webkit_options = {width: 1024, height: 768}

If a driver is not found the default rendering will be used. If this doesn't work with your driver, then you can
add another driver configuration like so

	# The driver name should match the Capybara driver config name.
	Capybara::Screenshot.register_driver(:exotic_browser_driver) do |driver, path|
	  driver.super_dooper_render(path)
	end


Custom screenshot filename
--------------------------

If you want to control the screenshot filename for a specific test library, to inject the test name into it for example,
you can override how the basename is generated for the file like so

	  Capybara::Screenshot.register_filename_prefix_formatter(:rspec) do |example|
	    "screenshot_#{example.description.gsub(' ', '-').gsub(/^.*\/spec\//,'')}"
	  end

By default capybara-screenshot will append a timestamp to the basename. If you want to disable this behavior set the following option:

    Capybara::Screenshot.append_timestamp = false


Custom screenshot directory
--------------------------
By default screenshots are saved to the current working directory. If you want to customize the location, override the file path as:

    Capybara.save_and_open_page_path = "/file/path"


Information about screenshots in RSpec output
---------------------------------------------

By default, capybara-screenshot extend RSpec’s formatters to include a link to the screenshot and/or saved html page for each failed spec. If you want to disable this feature completely (eg. to avoid problems with CI tools), use:

    Capybara::Screenshot::RSpec.add_link_to_screenshot_for_failed_examples = false

It’s also possible to directly embed the screenshot image in the output if you’re using RSpec’s HtmlFormatter:

    Capybara::Screenshot::RSpec::REPORTERS["RSpec::Core::Formatters::HtmlFormatter"] = Capybara::Screenshot::RSpec::HtmlEmbedReporter

If you want to further customize the information added to RSpec’s output, just implement your own reporter class and customize `Capybara::Screenshot::RSpec::REPORTERS` accordingly. See [rspec.rb](lib/capybara-screenshot/rspec.rb) for more info.


Example application
-------------------

A simple Rails 3.1 example application has been set up at [https://github.com/mattheworiordan/capybara-screenshot-test-rails-3.1](https://github.com/mattheworiordan/capybara-screenshot-test-rails-3.1)
Git clone the app, and then run Cucumber `rake cucumber`, RSpec `rake spec` and Minitest `rake test` and expect intentional failures.
Now check the $APPLICATION_ROOT/tmp/capybara folder for the automatic screen shots generated from failed tests.


Common problems
---------------

If you have recently upgraded from v0.2, or you find that screen shots are not automatically being generated, then it's most likely you have not included the necessary `require` statement for your testing framework described above.  As of version 0.3, without the explicit require, Capybara-Screenshot will not automatically take screen shots.  Please re-read the installation instructions above.

Also make sure that you're not calling `Capybara.reset_sessions!` before the screenshot hook runs. For RSpec you want to make sure that you're using `append_after` instead of `after`, for instance:

    config.append_after(:each) do
      Capybara.reset_sessions!
    end

[Raise an issue on the Capybara-Screenshot issue tracker](https://github.com/mattheworiordan/capybara-screenshot/issues) if you are still having problems.

Repository
----------

Please fork, submit patches or feedback at [https://github.com/mattheworiordan/capybara-screenshot](https://github.com/mattheworiordan/capybara-screenshot)

The gem details on RubyGems.org can be found at [https://rubygems.org/gems/capybara-screenshot](https://rubygems.org/gems/capybara-screenshot)

About
-----

This gem was written by **Matthew O'Riordan**

 - [http://mattheworiordan.com](http://mattheworiordan.com)
 - [@mattheworiordan](http://twitter.com/#!/mattheworiordan)
 - [Linked In](http://www.linkedin.com/in/lemon)

License
-------

Copyright © 2012 Matthew O'Riordan, inc. It is free software, and may be redistributed under the terms specified in the LICENSE file.
