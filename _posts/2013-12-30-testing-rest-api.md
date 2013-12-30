---
layout: post
title:  "Testing my REST API"
date:   2013-12-30 06:16:11
categories: post
tags: bdd mocha nodejs express supertest
---

[Early]({% post_url 2013-12-15-tdd-mocha-nodejs %}) this month I have started a journey with Javascript on the server side and Nodejs by creating this simple time-tracking tool application. After initial domain design specification - DDD yeah, I wanted to specify a REST API that would provide a simple CRUD set of operations. 

Again, I wanted to accomplish that applying TDD and BDD techniques. I'm using mocha as my testing framework but it doesn't provide support to HTTP assertions, which is what one need in order to build a robust REST API. This is where [SuperTest](https://github.com/visionmedia/supertest) comes to rescue with its fluent and super helpful API.

Below is an example of 2 operations: findById and addProject.

{% highlight javascript %}
/**
*	Test specs for Server
*/

var should  = require('should');
var assert  = require('assert');
var request = require('supertest');
var server  = require('../server');

describe('Server', function () {
	var url = 'http://localhost:3000';

	describe('GET /projects/:id (findById)', function () {
		it('responds with json', function (done) {
			request(url)
				.get('/projects/1')
				.set('Accept', 'application/json')
				.expect('Content-Type', /json/)
				.expect(200, done);
		})
		it('has one project', function (done) {
			request(url)
				.get('/projects/1')
				.set('Accept', 'application/json')
				.expect('Content-Type', /json/)
				.expect(200)
				.end(function handle_res (err, res) {
					if (err) throw err;
					res.body.should.have.property('id', 1);
					res.body.name.should.equal('Crispy');
					done();
				})
		})
	})
	describe('POST /projects (addProject)', function () {
		var body = { name: 'Crispy' }
		it('creates a new Project', function(done) {
			request(url)
				.post('/projects')
				.send(body)
				.expect('Content-Type', /json/)
				.expect(201)
				.end(function handle_res (err, res) {
					if (err) throw err;
					res.body.id.should.be.an.Number;
					res.body.name.should.equal(body.name);
					done();
				});
		})
	})
})
{% endhighlight %}

This approach allowed me to implement my API from the bottom up, validating every aspect of a REST operation without having to open a web browser to test. The full test and implementation can be found in the project repository on [Github](https://github.com/georgeanderson/crispy).