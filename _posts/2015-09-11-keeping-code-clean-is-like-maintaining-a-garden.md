---
layout: post
title:  "Keeping Code Clean is like Maintaining a Garden"
date:   2015-09-11 08:00:00
categories: blog
tags:
    - technical
---

A few years back I installed a sprinkler system in my future wife's backyard. We also built a small little retainer wall for a raised planter and plopped down a couple trees. I was very happy with the result, and on day zero it looked great.

For whatever reason, we've done a fairly good job of maintaining one side of the garden while we've let the other side go wild, and the other day, while I was picking weeds on the "good" side I concocted this analogy: maintaining a garden is not much different from keeping a codebase clean. 

On the "good" side of the garden, our plants are easily defined and separated from each other by clean mulch. The irrigation system is easy to identify and when leaks occur I can quickly find the holes and mend them. Moreover, weeds are easy to identify and remove. I can take a step back and immediately recognize all the working parts of my garden, which parts need attention and which parts are working well. 

![The Good Side of the Garden]({{ site.url }}/images/good-side-of-garden.jpg)

Clean code is not too dissimilar: functions and methods are easy to identify and differentiate from each other. Bad bits of code are easy to identify and are simple to remedy. I can take a step back from my code and immediately recognize all the working parts and which parts need attention. 

Just like any garden, any code will spring "weeds". Weeds in a clean codebase stick out like a sore-thumb, are simple to remedy, and don't have very deep roots.

On the other side of our garden, well, is a different story...

![The Bad Side of the Garden]({{ site.url }}/images/bad-side-of-garden.jpg)

Here, I cannot identify working parts of my garden. Is there a drip system under there? Are these all weeds, or is there a non-weed plant down there somewhere? When I go to pull up one of these weeds it is difficult; the roots are extensive, and invasive. Probably I will destroy some non-weed plants when I pull up the weeds and it may be in my best interest to just start over (pulling everything up at once). If there was a leak in my irrigation system, or an ant mound that started to pile up - I would have absolutely no idea.

For anyone who has ever worked in a non-clean codebase I think the point here is quite poignant. Messy code, even from a distance, is a disaster. You cannot tell the good parts from the bad, and the roots of the "weed" code run throughout the entire codebase; it's often impossible to simply remove the bad code from the good, because they are so interconnected. 

Gardening is easy, and fun, as long as you maintain your garden. Pick the weeds when they are small and easily discernible from everything else; this means picking them daily! The same thing can be said about your code; clean code is a daily process, pruning bad things before they overtake your entire project. 

For those who are new to clean code, I highly recommend Robert C Martin's book, ["Clean Code: A Handbook of Agile Software Craftsmanship"][1].

[1]: http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882