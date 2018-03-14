---
layout: post
title:  "API Testing With VCR.py - Why Have I Not Done This Before?"
date:   2015-08-15 20:00:00
categories: blog
tags:
    - technical
---

A few months ago a co-worker suggested that I mock my API tests using **VCR.py** ([here's a link to the library on github][1]). At the time, I had no idea what **VCR.py** was, or how to use it; I was, however, totally aware that my API tests were taking too long and causing needless hits to our various third party (and internal) API services. So I looked into this **VCR.py** thing - and I just had to write a blog post about it because it's that awesome.

## VCR mocks calls to your API's - automatically
In the past, I've played around with mocking API calls by manually saving an expected API response to a json file and loading that file instead of making the actual request. This process was clunky and generally resulted in me not wanting to mock my API calls. So I didn't. I just made actual API calls during my tests.

VCR effectively takes all the hassle out of creating mock API responses, because it automatically generates those hard coded files! Essentially, after your tests run the first time, any HTTP request that is generated is caught by VCR.py and the associated response is saved in a local file. Every test thereafter will use the saved file (called a `cassette`) and your tests will no longer generate real HTTP requests. It's awesome. 

## Getting Started with VCR.py
First, install VCR.py from pip,

    pip install vcrpy


Then, just decorate any test that issues an HTTP request with the `use_cassette` decorator

{% highlight python %}
import unittest
import vcr
import requests


class MyTest(unittest.TestCase):

    @vcr.use_cassette('test-stupid-request.yml')
    def test_stupid_request(self):
        """testing a stupid simple example of using vcr"""
        response = requests.get('http://qdonnellan.com')
        self.assertEqual(response.status_code, 200)
{% endhighlight %}

The above is a really simple example of how vcr works, but if you understand it you should be able to extend it to whatever particular use-case you need. Now, what's going on is I have a really simple test which asserts the response from my website `http://qdonnellan.com` is an `HTTP 200`. So I make the call, and test the assertion. However, I'm decorating the entire test with this little jewel:

{% highlight python %}
@vcr.use_cassette('test-stupid-request.yml')
{% endhighlight %}

What's going on is that **VCR.py** let's the request happen the first time, but then it records that request (and it's response) in a local .yml file. The second time I run the test, no HTTP request is made, instead vcr mocks the request by using the saved cassette. The proof is in the pudding: I have the exact code above saved in a file called `test_vcrexample`, and I'm going to test it using [Nose][2]:

First Test:
{% highlight bash %}
$ nosetests test_vcrexample
.
----------------------------------------------------------------------
Ran 1 test in 0.337s

OK
{% endhighlight %}
Second Test (using cassette):
{% highlight bash %}
$ nosetests test_vcrexample
.
----------------------------------------------------------------------
Ran 1 test in 0.015s

OK
{% endhighlight %}

Now, making a request to this super lightweight blog server is not very time consuming, but that second request, since it never had to leave my machine is **22 times faster**; now imagine using these mock cassettes on calls to actual API services that take several seconds to complete. 

## Complete Example Using Stripe
Not convinced by this silly little test, well let's look at a more detailed example of a test that requires several calls to a real API service. In this case let's use [Stripe's API][3] (because it is one of my favorite)

In this example I've got a small little Stripe client that creates customers and charges cards. I want to write a functional test that proves all my methods work together, which will require several API calls to complete. 


{% highlight python %}
""" simple_stripe_client.py - A simple Stripe Client, duh """

import stripe

stripe.api_key = 'sk_test_mYstRIpeApiTestKeynoTsharing'


class MyStripeClient(object):
    """a simple stripe client for making customers and subscribing
    them to plans"""

    def __init__(self, customer_email):
        self.customer = self.create_customer(customer_email)

    @staticmethod
    def create_customer(customer_email):
        """create a customer on stripe"""
        return stripe.Customer.create(email=customer_email)

    def save_card(self, card_dict):
        """save the card to the customer's account on Stripe"""
        return self.customer.sources.create(source=card_dict)

    def subscribe(self, plan_id):
        """subscribe this customer to the plan with the given id"""
        return self.customer.subscriptions.create(plan=plan_id)
{% endhighlight %}


{% highlight python %}
""" test_stripe_client.py - Test our simple Stripe client """

import unittest
import vcr
from simple_stripe_client import MyStripeClient


class MyStripeClientTest(unittest.TestCase):
    """testing my stupid simple stripe client"""

    @vcr.use_cassette('test-subscribe.yml')
    def test_subscribe(self):
        """a customer with a valid card should be able to subscribe to our
        basic-plan, which is already on file at Stripe"""
        client = MyStripeClient("test@example.com")
        client.save_card({
            "object": "card",
            "currency": "usd",
            "number": '4242424242424242',
            "exp_month": "09",
            "exp_year": "2019"
        })
        response = client.subscribe('basic-plan')
        self.assertEqual(response['status'], 'active')
{% endhighlight %}

So in this test I'm creating a customer, adding a card to that customer, and subscribing that customer to a plan. That's 3 API calls to our Stripe account. Let's see what happens when I run this test the first time:

{% highlight bash %}
$ nosetests test_stripe_client
.
----------------------------------------------------------------------
Ran 1 test in 1.525s

OK
{% endhighlight %}

One-and-a-half seconds is not too bad for 1 test; but if your billing system has a hundred or so of these tests, that could take several minutes to run. Let's try running the exact same test a second time (I've changed nothing, I'm literally running this test right after the first test - remember all of those API requests have been saved in a cassette for me to replay any time). 
{% highlight bash %}
$ nosetests test_stripe_client
.
----------------------------------------------------------------------
Ran 1 test in 0.021s

OK
{% endhighlight %}

Whoa! That's over **72 times faster** - because we aren't making any HTTP requests the second time around. 

## It's not just saving time

### Entire responses are saved as a transcript
I don't know how everyone else writes API tests, but when I first get to it I'm not even sure what the API responses are going to look like (and documentation is not always clear). So the first time I write a test I literally print the entire response to the console - I'm never surprised to see the myriad different ways developers like to represent simple things like "status code"

> Why is my test not working??? **status_code** should be 200!!! 
>
> (PRINTS RESPONSE TO CONSOLE)
>
> Oh freaking hell, it's **http_int_status** in this API, good to know

But when you save every response to a cassette file, you can read the entire response, in its entirety from a text editor. This is awesome. 

### Offline testing works
The next time I'm flying to meet the team, I can still run all my functional tests at 40,000 feet - as long as they have an associated cassette that was run before I left my home office. New tests never need HTTP, so they work anywhere. 

## Why have I not used this before?
I wish I had found **VCR.py** many years ago when I first starting writing tests that interacted with API's, and I strongly recommend anyone who is working on a python project that does interact with API's to use a mocking library that is at least as good as this one. 



A huge thanks to the folks who wrote this library, you've changed the way I write tests!

[1]: https://github.com/kevin1024/vcrpy
[2]: https://nose.readthedocs.org/en/latest/
[3]: https://stripe.com/docs/api#intro