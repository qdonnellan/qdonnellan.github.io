---
layout: project
title:  "mpc-client"
subtitle: "A python API for asteroids"
duration:   "2015"
categories: project
---

I ran across the [Minor Plant Center][2]'s web portal one day and thought it was super useful. So I whipped up a Python API to query their data using their developer tools. I'm quite happy with the solution I came up with as I think it does a fairly good job of exposing the MPC's data in an intuitive manner:


{% highlight python %}
from mpc_client.query import Query

q = Query()
q.filter(eccentricity_max=0.95)
q.limit(10)

for asteroid in q:
    print asteroid.name, asteroid.orbit.semimajor_axis
{% endhighlight %}



---

[Source Code][1]

[1]: https://github.com/qdonnellan/mpc-client
[2]: http://minorplanetcenter.net/web_service