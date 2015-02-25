---
layout: post
date: 2015-02-24 23:40:45
title: RubyMotion for Android - Layouts, Drawables and other resources
---

[See full source code of sample app](https://github.com/shaurya947/android-rubymotion-xml-layout-drawable)

Having had some experience in developing Android apps using Android Studio, when I initially started with RubyMotion for Android a few weeks back, I was terrified at my initial perception that RubyMotion has no support for xml layouts, resources, drawables and other resources, and that **everything** had to be done in code. Thankfully, I was wrong. This [cool webcast](https://motioninmotion.tv/screencasts/46) by the folks over at motioninmotion was eye-opening.

Bottom line, in the root directory of your RubyMotion android app, you can create a `resources` folder, which can have subdirectories such as `values`, `layout`, `drawable` etc. (essentially mimick the Android Studio directory structure). You can then access these resources in your ruby code as follows:

~~~ruby
resources.getIdentifier(<resource-name>, <type-of-resource>, <package-in-which-resource-exists>)
~~~

So for instance, if I had a layout called `activity_main.xml` inside `/resources/layout/`, and if my app package was called `com.example.myfirstapp` (you can find this in `AndroidManifest.xml` file in the `build` directory after building your RubyMotion app once), then to set the layout on the activity, I would call:

~~~ruby
setContentView(resources.getIdentifier('activity_main', 'layout', 'com.example.myfirstapp'))
~~~

Another example which is commonplace in Android development (to access view elements in code):

~~~ruby
findViewById(resources.getIdentifier('hello_button', 'id', 'com.example.myfirstapp'))
~~~

This makes life so much easier! With such an approach, one could define their xml in a WYSIWYG editor (such as the one in Android Studio), and then simply copy that xml file for RubyMotion. Since the button in my app has a custom background, I could also copy over the drawable resource corresponding to the background and just put it in the right place (`resources/drawable/`) and simply refer to it in my layout xml file.

With all these good things being said, there is one caveat though (well not really caveat, but just something I haven't yet been able to find a workaround for). When writing code in Android studio, I could simply do this for a button in the layout xml file:

~~~xml
<Button
        android:onClick="buttonClicked />"
~~~

And then define a method in the java file as:

~~~java
public void buttonClicked(View v)
{
    //do something corresponding with click here
}
~~~

My (slightly incomplete) understanding of this concept is as follows: a method called buttonClicked is registered with the onClick listener associated with the button view object. Note here that the listener is that of the button itself. 

RubyMotion doesn't like this. It doesn't like if I go on to declare a buttonClicked method in the `activity.rb` file. In fact, that method never even makes it to the final java file in the build for me. So a workaround is as follows:

~~~ruby
@button = findViewById(resources.getIdentifier('hello_button', 'id', 'com.yourcompany.helloworld'))
@button.setOnClickListener (self)
~~~

This is essentially registering the click event for the button with the on-click listener of the activity so that now the action for the button click can be defined inside the `onClick` method for the activity as follows:

~~~ruby
#this is the onClick for the activity
def onClick(view)
  if @button.getText == 'hello'
    @button.setText("world")
  else
    @button.setText("hello")
  end
end
~~~

This works, but I feel it would be nicer to be able to use the Android Studio way. Until I can find a workaround..

So there ya go! Hope you have fun experimenting with RubyMotion!