---
layout: post
date: 2015-02-23 01:00:00
title: Android RubyMotion Screen Transitions
---

[See full source code](https://github.com/shaurya947/android-rubymotion-screen-transitions)

If you haven't heard about [RubyMotion](http://www.rubymotion.com): it is an innovative Ruby compiler toolchain that lets you write cross-platform apps for iOS, Android and OS X in Ruby.

I just started experimenting with RubyMotion a few weeks back to make an Android app. Today, I completed a basic app that demos screen (or activity) transitions. To get a basic Android up and running using RubyMotion, the guide [here](http://www.rubymotion.com/developers/guides/manuals/android/getting-started/) is an excellent resource. Once you have your blank app running, follow on...

First off, let me talk a bit about the directory structure. The "app" folder contains the meat of your code: the .rb files that define activities. By default, this folder will have a file named "main_activity.rb," which is for the default main activity of your app. When the app is built through the command line, an *android-readable* package for the app is created inside the "build" folder, which is then installed on the device. Lastly, the Rakefile (in the root directory) can be used to edit the AndroidManifest.xml file (a very important file that defines properties of your app such as theme, activities, target version etc).

Here is the Rakefile code:

~~~ruby
# -*- coding: utf-8 -*-
$:.unshift("/Library/RubyMotion/lib")
require 'motion/project/template/android'

begin
  require 'bundler'
  Bundler.require
rescue LoadError
end

Motion::Project::App.setup do |app|
  # Use `rake config' to see complete project settings.
  app.name = 'HelloWorld'

  #the child method returns the first child of the passed-in type from the manifest
  app.manifest.child('application') do |application|
    application['android:theme'] = '@android:style/Theme.Holo.Light.NoActionBar'
  end

  #add SecondActivity to manifest file
  #SecondActivity will be defined later
  app.sub_activities += %w(SecondActivity)
end
~~~

Here is the ruby class file for MainActivity, which is the launch activity (the first activity that is launched when the app starts):

~~~ruby
#inherit from Activity class in the Android SDK
class MainActivity < Android::App::Activity

  #onClick defines action for onClickListener
  def onClick(view)
    #start another activity using intent
    i = Android::Content::Intent.new(self, SecondActivity)
    startActivity(i)
  end

  def onCreate(savedInstanceState)
    super

    relativeLayout = Android::Widget::RelativeLayout.new(self)

    @button = Android::Widget::Button.new(self)
    @button.text = "go to screen 2"

    @button.onClickListener = self

    relativeLayout.addView(@button)
    self.contentView = relativeLayout
  end
end
~~~

The ruby class for SecondActivity is quite similar. Here it is, without redundant comments:

~~~ruby
class SecondActivity < Android::App::Activity
  def onClick(view)
    i = Android::Content::Intent.new(self, MainActivity)
    startActivity(i)
  end

  def onCreate(savedInstanceState)
    super

    relativeLayout = Android::Widget::RelativeLayout.new(self)

    @button = Android::Widget::Button.new(self)
    @button.text = "go to screen 1"

    @button.onClickListener = self

    relativeLayout.addView(@button)
    self.contentView = relativeLayout
  end
end
~~~

That should be sufficient. Build the app using command line and run it on a device / emulator. The buttons on either screen should transition between the two screens. I found this resource quite helpful in completing my app: [Navigating from one screen to another screen in Android using RubyMotion](http://demoscripts.info/28081417/navigating-from-one-screen-to-another-screen-in-android-using-rubymotion.html)