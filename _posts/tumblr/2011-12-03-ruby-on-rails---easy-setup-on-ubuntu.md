---
layout: post
title: Ruby on Rails - Easy Setup on Ubuntu
categories: post
tags: ruby rails linux
---

Today I had my first experience with Ruby on Rail in a RHOK event. After a terrible experience trying to setup the environment on a Windows 7 box, I came home and started to build my own environment on a Ubuntu 10.10 system.
The project I am up to contribute is the letshelp.it and it has a few version constraints:
Ruby 1.8.7
Rails 3.0.7 
They also have sqlite3-ruby as a requirement.
Here is the installation process I took to have the basic environment necessary to start contributing to this project:

{% highlight bash %}
$sudo apt-get install ruby ruby1.8-examples

$sudo apt-get install rubygems1.8

$sudo gem install rails —version 3.0.7

$export PATH=/var/lib/gems/1.8/bin:$PATH
{% endhighlight %}

With this you have the basic Ruby on Rails tool set ready. Ruby examples package is optional in the first line - I like to learn from examples. You may want to include the last command into your .bashrc file, so you have your PATH set in the next log on.
Last step is to download the project and install its dependencies.

{% highlight bash %}
$git clone git@github.com:gelias/letshelp.it.git
{% endhighlight %}

I found some important packages that need to be installed or dependencies will fail.

{% highlight bash %}
$sudo apt-get install ruby-dev libsqlite3-dev libsqlite3-ruby
{% endhighlight %}

Then finally:

{% highlight bash %}
$bundle update
{% endhighlight %}

Done. Project ready to go. 
I hope this can be useful to someone else.

George.
