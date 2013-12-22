---
layout: post
title:  "TDD with Mocha in Nodejs"
date:   2013-12-15 20:14:25
categories: post
tags: bdd mocha nodejs
---

My journey with Nodejs has just started but I don't want to throw away all the best practices and knowledge I have accumulated through the years working with other programming languages and technologies. If I had to summarize  all knowledge assimilated in all those years in one word it would be: TDD.

What better way to start learning a new technology than creating a new project using it? In the next weeks I plan to set some time aside to work on this simple project so I can address a need I currently have while I learn Nodejs, JavaScript and some other cool new front-end technologies.

I want to build the simplest time tracking tool possible. The immediate need I have when starting a new project is a testing framework. So after some research I found [Mocha] (http://visionmedia.github.io/mocha/). I just added it as a development dependency on my node package description.

`package.json:`
{% highlight json %}
{
  "name": "crispy",
  "description": "Nice and crispy timesheet",
  "version": "0.0.1",
  "author": "George Anderson <guimaraes.george@gmail.com>",
  "dependencies": {
  },
  "devDependencies": {
    "mocha": ""
  },
  "keywords": [
    "crispy",
    "nodejs",
    "timesheet"
  ]
}
{% endhighlight %}

And then let `npm` do its work and download the dependencies for me:

{% highlight console %}
$npm install -d
{% endhighlight %}

Next step was to add a call to mocha in my `Makefile` using the **BDD** interface. By default mocha will look for tests under `./test/*` folder so we don't need to specify a path.

{% highlight makefile %}
test:
	@./node_modules/.bin/mocha -u bdd

.PHONY: test
{% endhighlight %}

Ready to start rocking! Crispy will be Project centric, so time should be logged against Projects. The least expected behavior of a new Project would be:

{% highlight javascript %}
/**
*	Test spec for Project
*/

var assert = require('assert')
	, crispy = require('../lib/crispy');

describe('Project', function() {
	var cp = new crispy.Project();

	beforeEach(function() {
		cp = new crispy.Project();
	})

	describe('Default New Project', function() {
		it('should have a name', function() {
			assert.equal('New Project', cp.name);
		})
		it('should have 60 minutes of effort', function() {
			assert.equal(60, cp.remaining());
		})
		it('should update effort after time is logged', function() {
			cp.log(10);
			assert.equal(50, cp.remaining());
		})
		it('should update effort after successive time is logged', function() {
			cp.log(10);
			cp.log(10);
			assert.equal(40, cp.remaining());
		})
		it('should update effort after time is elapsed', function(done) {
			cp.start();
			setTimeout(cp.stop(), 1000); //1 sec
			done();
			assert.equal(59, cp.remaining());
		})
	})
})
{% endhighlight %}

And after many rounds of failures, this is the nice output we all expect to see:

{% highlight console %}
george@optiplex:~/dev/projects/crispy$ make test

  ․․․․․

  5 passing (8ms)


{% endhighlight %}

The experience has been very smooth so far. I will start using should.js as my assertion library going forward to have better support for BDD style. For complete code, you can browse, clone or fork the project at [GitHub](https://github.com/georgeanderson/crispy).