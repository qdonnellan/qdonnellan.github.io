---
layout: post
title:  "Building Web Analytics at SpiderOak"
date:   2016-01-13 08:00:00
categories: blog
tags:
    - spideroak
---


A few weeks ago, SpiderOak President and CMO Mike McCamon posted an [article][1] on how we ditched Google analytics in favor of our own *home-brewed* solution. That post
generated a good discussion on Hacker News where many asked how we implemented
it, and why we didn't just choose something off the shelf. As the
person responsible for the implementation, I wanted to take a moment to reveal
some of the insights behind the technical decisions we made.

### Why write something ourselves?
Web analytics is not really a new field; in implementing
our own analytics solution it's not like we're doing something that hasn't been
done thousands of times before. I believe my brother said it best:

> "Dude, don't implement your own analytics solution."

My brother is a very smart guy, and I believe that to be great advice to any 
company out there trying to do what we've done. I've extended that
advice to the following:

1. Don't implement your own analytics.
2. If you can't do #1, then make it as simple as possible.

Before we wrote any of our own analytics code
we worked hard to answer the question: **Is there something out there that we
could just use today?** It is silly to re-invent something
if you really don't have to. In fact, it's not just silly, it's expensive. Our web
team is very small and we don't have much time to work on things that don't 
improve the bottom line.

### Technical requirements
We set out looking for something that already existed. These are the things we decided were important:

1. Self-hosted: none of the data we collect could ever be sent to a third party.
2. The solution should re-use technology that currently exists in our 
stack: PostgreSQL, Python, Django, Go, InfluxDB (which we were
already using for system monitoring), and Redis.

I honestly didn't think that these requirements were that strict; many companies
out there use some combination of Python/Postgres/Redis, so I assumed that there
would be a mature, open-sourced, self-hosted solution in the world that we could
used.

There wasn't. Piwik was encouraging, but we really, *really* didn't want
to stand up a PHP server on our ecosystem. (In fact, if you add **"no PHP"** to the requirements list above,
you kill nearly all open source analytics solutions!)

We concluded that we had to write something ourselves. 

### InfluxDB: the early leader
We were already using InfluxDB for some internal server monitoring, so my first thought
was to just spin off a new InfluxDB instance and send AJAX post requests to it via our
frotnend. 

We quickly realized a couple issues:

1. At the time, there was no way of preventing a user from issuing DELETE requests on behalf
of our web application (since DELETE shared the same permissions as POST). 
2. At the time, InfluxDB documentation was incomplete and rapidly changing; I was anxious about
biting down on a platform that was very early on in it's development.

We decided that InfluxDB was very awesome, super simple, but too immature
for our case. A lot has changed with Influx since we decided to write our own analytics backend, 
and I'm still very curious about developing a web analytics solution in the future that uses InfluxDB; but we had moved on.

### Django: keeping things dead simple
The biggest take-away from our examination of InfluxDB was that analytics events
are simple (time + data); and implementing web-analytics should be as simple as time-stamping a dictionary and inserting that as a new row in Postgres.

It's also worth noting that our email-marketing tool is written in Django, and we recently
revamped our CMS to be entirely self-hosted in Django as well; so the notion of rolling
our own analytics into the same Django application was an incredibly appealing thought. 

So that's what we did. Essentially this how it works:

1. Receive JSON packet from the front-end.
2. Clean the data and time-stamp it. 
3. Save the event to Redis.
4. During a regularly scheduled cron-job, scrape new events in Redis and save them
into Postgres.

That's it, nothing fancy. We've been using this method for over 6 months and haven't had to change
anything - it's stable, simple, and hasn't broken... yet. 

### The hard part: visualizing the data
I realize now that saving a timeseries of event data is not really all that complicated, 
and querying that data in Postgres is likewise not very complicated. But it doesn't take very long
for your analytics table to grow to millions of rows, and doing raw queries for very specific or complex
event behavior quickly becomes tricky.

Thus, our next big project was a dashboard. To give you a sense of the difficulty associated with
creating an analytics dashboard, consider this:

1. Building the analytics backend took less than a week (that was six months ago).
2. We're still building the dashboard!

Actually, we'll probably always be working on the dashboard because as a company we continuously identify
new measures we deem important. If anything, this is where using a third-party analytics solution really
pays for itself - all those pre-built visualizations are ready to go on day 1. Google and Piwik make it easy for a non-developer to create new chart widgets and drill-down into their data sets to pull out any visualization you want.

On the flip side, I believe that Google and Piwik probably have *way too many* features for small companies,
and there is indeed some utility in only displaying the bare minimum metrics in the beginning. At the very least, that's what we decided to do: we wrote down a list of five or so metrics that were the *most important to us* and we built a dashboard that only visualized those things. 

Essentially, this is how our dashboard works:

1. We use cron jobs and [Luigi][2] to extract the analytics data from our dashboard (and now other places like
our git repos and our sales platform) and transform the data into visualization-specific datasets, and save those into Redis.
3. Our frontend consumes the datasets in Redis and produces visualizations using [Highcharts][3].

A side benefit of having to write our own analytics dashboard is that we can include data from any company
source (not just the analytics pipelines), so we're able to display sales data in the same visualization as our website funnel. 

### Takeaways
Building our own analytics pipeline has been an incredibly interesting project. Before coming to work at SpiderOak I would have probably just used Google Analytics on every one of my web projects without a second thought. Now I'm proud to say that, as a company, we're able to gain valuable business insights from our customers *without giving that data to anyone else*. 

In fact, that's the key message I'd like to impart: whether you decide to build your own analytics solution in house or use a third-party solution, please inject consideration of your customer's privacy into your decision process.

Now, if you are considering rolling your own analytics solution in-house, here are the 
things you should consider:

1. It's not hard to collect data.
2. It's hard to summarize and visualize data.
3. Don't write your own ETL (we used Luigi since we're Python based, I'm sure there's something for your stack).

It was somewhat cathartic watching my own web visits dashboard spike with a surge of visitors when Mike's blog post about our analytics solution hit the front page of Hacker News. It was also good that nothing broke!

[1]: https://spideroak.com/articles/yeah-we-ditched-google
[2]: https://github.com/spotify/luigi
[3]: http://www.highcharts.com/
[4]: https://spideroak.com/articles/building-web-analytics-at-spideroak
