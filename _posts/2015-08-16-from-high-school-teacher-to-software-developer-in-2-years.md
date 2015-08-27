---
layout: post
title:  "From High School Teacher to Software Developer in 2 Years"
date:   2015-08-16 12:00:00
categories: blog
---

More often than not, when someone hears I that I used to be a high school teacher they ask me *"why did you go from teaching to web development?"* That's a tough question to answer and eventually I'll write a succinct narrative on that topic. For now I'd rather address the overview of *how* I made the transition.

### What I knew before I started
For this journey to make sense, it's probably required that you understand *what I knew* before I started. It would be unfair to say "I jumped into software with absolutely no previous experience", though I believe that someone could. 

In 2007 I graduated from Texas A&M with a degree in Aerospace Engineering. Coursework for that degree required that I learn Fortran and MatLab; I mostly resent the instruction I was given in those two languages, largely because there wasn't any - I taught myself nearly all -  and it was presented entirely from an Aerospace perspective (that is - I didn't learn any computer science fundamentals, only that I could hack together some scripts to solve complicated math problems instead of doing them in Excel on on my calculator).

I believe that our AERO department at A&M could have done a much better job at giving me a software foundation, and that such a foundation would have benefited me greatly in my current endeavors. But then, I'm not currently an Aerospace Engineer and many of my friends who are currently working in that industry are successful despite of their lack of software knowledge.

At any rate, I did know *some* software in 2012. I could type, understood was a terminal was, and had a background in solving problems. Like any good nerd at the time I also had some high school experience hacking together some HTML/CSS (mostly things that looked horrible). Such was my skill set at the time. 

## Overview
Here's the quick-and-dirty path I took, for those who don't want to read all the details below:

1. April 2012 - took Udacity course on web development
2. Summer 2012- built my first web application for my classroom
3. September 2012 - built a homecoming voting application for Senior Class at my high school
4. December 2012 - built a web application for reviewing camera lenses
5. February 2013 - built a simpler lesson management application
6. March 2013 - started work on my first SAAS, lessonwell
7. Spring 2013 - founded the EdTech DFW meetup
8. Summer 2013 - continued work on lessonwell, lots of business-specific learning
9. Fall 2013 - learned Ruby, Go, and some other random stuff
10. December 2013 - decided to leave teaching, began looking for a job
11. February 2014 - horrible interview with FogCreek
12. April 2014 - accepted first job

### Udacity - April 2012
In 2012 I was teaching Astronomy and Calculus at a public high school in Fort Worth, TX. Among my many frustrations with being a teacher was the lack of a good website; a website with which I could quickly prototype lessons and show them to my students. 

At the same time, the wonderfully awesome edu-online startup Udacity was offering their inaugural [web development course][1]. My brother - who also transitioned to software during the same period, and who currently works in web development at [AthenaHealth in Austin, TX][2] - discovered this Udacity course and suggested that we take it together. So we did.

During this course I learned Python, how to deploy web applications to Google App Engine, and how to actually write websites with HTML/CSS. I also picked up Twitter Bootstrap which helped me to prototype web pages super quickly. 

### My First Web Application - Summer 2012
Toward the end of that summer, and as I was completing the Udacity web development course, I began my work on the first web application for my classroom, [qCalculus][3]. This was written in Python, hosted on Google App Engine, and all the front-end work was done with Twitter Bootstrap. 

![qCalculus](https://camo.githubusercontent.com/83a1eb57c4049aae07eac281000e89d5521399e5/68747470733a2f2f7472656c6c6f2d6174746163686d656e74732e73332e616d617a6f6e6177732e636f6d2f3532663534333466363038633336333434633937663665382f3532663534363234376138373237656337663366333438392f66633031633838316663653235633361336665393438333634663336643934612f53637265656e5f53686f745f323031342d30322d30375f61745f322e34342e30305f504d2e706e67)

### My First Clients - High School Girls
In the fall of 2012, it became known at our school that Mr. D (as I was referred to) could write web applications. So the senior class girls came up to me and asked if I could make a website that allowed people to vote for the homecoming court. 

> Senior Girls: Hey Mr. D, can you build us a voting website for Homecoming?

> Me: Yeah sure no problem

> Me (silently, a week later): Holy hell, what did I get myself into?!?!

I'm so glad I said yes to this; I eventually learned that much of software development is building products to a given spec against a hard deadline. Homecoming in Texas is not something you can push back (not as a Calculus teacher at least). And screwing up the mechanism by which some mom's daughter get's to ride in a parade was simply not an option. 

Eventually this homecoming application was used for subsequent Proms and Homecoming votes for the next 2 years.  

### Speculess - A break from Edu - December 2012
At some point during the fall semester I had to stop development on qCalculus - it was just too difficult to actively manage a full-fledged web application while also being a full time teacher. And just like any good fledgling developer, I dropped one project and picked up another one, [speculess][4].

Speculess was my attempt to distract myself from education. I wanted to work on something that had nothing to do with teaching so that my nights and weekends weren't totally filled with the same stuff over and over. At the same time I was very intrigued by the breakout of the micro-four-thirds camera standard. So I made a web application that managed user reviews for camera lenses. 

![speculess](https://camo.githubusercontent.com/6a17cdf946529a7fb52990e0ae6d0240fb80dcbf/68747470733a2f2f7472656c6c6f2d6174746163686d656e74732e73332e616d617a6f6e6177732e636f6d2f3532663534333466363038633336333434633937663665382f3532663534373534356130653665303431633362353664392f37313633346564343437386232323765316533643630386536613135343665612f53637265656e5f53686f745f323031342d30322d30375f61745f322e34392e35315f504d2e706e67)

I was still using a combination of Python/Bootstrap/AppEngine. When I started development of speculess, I was using mercurial for version control and was storing everything on [Kiln][7]

It was also about this time that I created my account on [GitHub][5]. Apparently, however, it would be a whole 'nother year before I would commit my first repository on GitHub. In the interim I kept all my projects on Kiln and used mercurial for version control.

### Back to Edu-Web-Apps - February 2013
I learned a lot about writing a web application for my classroom with qCalculus, and I didn't want to repeat the major mistakes. Namely, if I was going to be successful managing a web application while being a full-time teacher, I need to write something that was super simple to maintain, that helped my students, and that made my life easier as a teacher. So I envisioned an app that did the following:

1. No user accounts - I didn't want to have to manage students-as-users. It had to be simple, static pages with content provided entirely by me.
1. Content could be written entirely in Markdown - I was adept at that shorthand by now, and I wanted to write my lessons as quickly as I was writing README's - this would actually save me time. 

What I came up with was [Calculus On Chrome][8], and I debuted the website for the finale of that school year; the same students who got qCalculus in August got Calculus on Chrome in the spring. Its launch was significantly more successful and it read more like a textbook than a web application. 

It's also obvious that I began to seriously work on my design skills; while I was still using Twitter Bootstrap for the layout of things, it was around this time that I started to experiment with custom CSS files. 

### The Birth of Lessonwell - March 2013
Calculus on Chrome was a simple application, and so it was successful. I spent most of my time writing content - something my students appreciated. I was able to fully complete my lessons for the year with that simple site. 

It was around this time that I started getting requests from other teachers to build their websites. I had an idea - hey I could build a software platform that other teachers could use! I could build my first SAAS! So I started working on Lessonwell during the spring of 2013. I was still developing entirely in Python/Bootstrap and hosting everything on Google App Engine. My version control was still entirely mercurial on Kiln. 

### The Summer of Stripe & Meetups
In the summer of 2013, I started learning more about software as a business. I spent countless hours learning about Lean Startup practices, how to collect money, how to market, how to do customer service; if Lessonwell was going to be successful I had to wear all of those hats (while also teaching full time). 

Among the many discoveries I made that summer was the payment service [Stripe][9]. Discovering stripe had a profound influence on how I wrote Python and how I developed my own API's. I recommend that any new web developer take a look at [Stripe's API documentation][10]. 

It was also about this time that I began connecting with other folks in the area on the topic of Education & Technology. I eventually founded the [EdTech DFW Meetup][11] along with the co-founder of [Nepris][12] (Sabari Raja). 

### Juggling A Million Side Projects, Fall 2013
In the Fall of 2013 I started to branch all over the place. I finally pushed my first repository to GitHub (a [boilerplate for bootstrap][13], because the world definitely needs more that...). 

As lessonwell started to get more complicated, I realized that I needed to actually learn JavaScript (that's right, I had successfully built web applications for a year and a half without actually doing any significant work in JavaScript).

It was also around this time that I was developing an afterschool program to teach students programming using Ruby and the Sphero Robot. One of my early GitHub repos was a demonstration for using [ruby to control the sphero][14]. 

Near the end of the fall, I started teaching myself Go and had fun developing [some simple web applications][15] using that very un-pythonic language. 

### Christmas break 2013 - The Decision to Leave Teaching
While on vacation with my family in December 2013, and occupied by what seemed like an unbearable number of side-projects (all related to programming in some way), I came to the realization that I wanted to do web development full time. So over that break I made a resolution to start looking for a job. 

It was around this time that I made my first contribution to StackOverflow: an answer to a question about Jinja template formatting that has [still not received any points][16]!

It was also around this time that I started porting all my mercurial projects on Kiln over to github. 

### February 2014 - the Job Search Begins
I figured that if I wanted to be employed as a software developer by the time my teaching contract was up, I had to give myself a good 6 months of job hunting before I landed a job. As the new school year started in August, I decided that February was the point-of-no-return for applying to jobs. 

### My First Interview: FogCreek
Because I had used Kiln and Trello for some time to manage my own software projects, and because I rather admired Joel (founder of StackOverflow, FogCreek) for his [wonderful words over the years][17] on the subject, I decided to go all out and apply for a job that was way over my head: at the universally reknowned hard-to-get-into FogCreek. 

I created a jumbo Trello board that basically recapped this same story (from my aerospace degree to lessonwell) and shared the board with Joel himself. To my surprise, later that afternoon Joel personally commented on my Trello board, and an hour or so later I got an email from someone in their HR deparment that they were interested. After a couple of phone screens I had my first coding interview. 

I scrambled and read [Prorgamming Interviews Exposed][18] as quickly as I could. I had literally no experience writing algorithms on the spot, and I second-guessed myself constantly; what was I, a high school teacher, doing in a job interview for FogCreek?

I totally bombed that coding interview. I have serious doubts to this day if I even conveyed to that poor employee that I even knew what Python was. 

In hindsight, getting my foot into the door that was FogCreek's interview process was invaluable later in my young career. I used that lesson to eventually make it several steps through Khan Academy's interview process, and eventually land a job at my current employer (SpiderOak) where I'm the web development lead. 

But man, that interview sucked.

### My First Job - April 2014 - 2 Years Later
My journey eventually led to my first job, as a full-stack developer at the very tiny BurkeSoftware. Refreshingly, I didn't have to show any sort of skill on the spot for this job - there was no sort of algorithm-based coding screen - instead David (founder of BurkeSoftware) focused on what I had built over the last 2 or so years - qCalculus, speculess, CalculusOnChrome, lessonwell, founding the EdTech DFW meetup, running a Ruby on Robots after-school program. All of those projects, now living on GitHub, were tremendous ammo for landing my first job. 

I was eventually offered a part-time job while I finished the school year (working late nights and weekends) which transitioned to a full-time job over that summer. 

In the end, I had secured a job as a web developer after initially pursuing the thing as a hobby - as something to help me be a more efficient teacher. I don't think I really knew that I would leave teaching when I started that Udacity Course in 2012, and it seems so long ago now that I look back. 

### Big Takaways

#### Its a long process
Looking back, 2012 seems so long ago. But then again, I didn't start out thinking that I would get a job. Another person could probably condense this entire process to less than a year if they were focused entirely on learning software. Then again, I do believe that my experience of building actual businesses (and not just code) eventually led me to my first job.

#### Work on something relevant
All of my projects were relevant to me; they were either education related (my full time job) or they related to one of my hobbies. I think this helped keep me motivated, I wasn't just writing software for the sake of writing software, I was writing software to make my life easier. It's easy to get burned out writing code, and easy way to avoid it is to make it relevant.

#### Always be building
I cannot stress enough that the most important thing I did was to **build stuff**. I built stuff and shared it with the world. Some stuff was crap, some stuff was maybe good, but it was visible, and I could point to it during interviews. Do this. 

#### Meet People
In the beginning my sole focus was to build code in the den of my office. Toward the end of my journey I eventually started meeting actual human beings. I wish I had done this earlier in the process. UIn the end jobs are given by people to people, not by robots scanning your StackOverflow accounts!



[1]: https://www.udacity.com/course/web-development--cs253
[2]: http://www.athenahealth.com/blog/2015/07/31/a-developers-look-inside-health-care
[3]: http://qcalculus.appspot.com/
[4]: http://speculess.appspot.com/
[5]: https://github.com/qdonnellan
[6]: https://github.com/qdonnellan/bootstrap_template
[7]: https://www.fogcreek.com/kiln/
[8]: http://calculusonchrome.appspot.com/
[9]: https://stripe.com/
[10]: https://stripe.com/docs/api#intro
[11]: http://www.meetup.com/edtechdfw/
[12]: https://www.nepris.com/
[13]: https://github.com/qdonnellan/bootstrap_template
[14]: https://github.com/qdonnellan/artoo_sphero_test
[15]: https://github.com/qdonnellan/fun_with_go
[16]: http://stackoverflow.com/questions/18426944/how-to-i-include-all-files-from-inside-a-directory-in-jinja2/21007242#21007242
[17]: http://www.joelonsoftware.com/
[18]: http://www.amazon.com/Programming-Interviews-Exposed-Secrets-Landing/dp/1118261364
