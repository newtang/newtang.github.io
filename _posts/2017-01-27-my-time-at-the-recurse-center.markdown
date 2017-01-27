---
layout: post
title:  "My time at the Recurse Center"
date:   2017-01-24 11:48:00 -0800
categories: recurse programming
---

In the latter half of 2016 I knew I was going to have some free time on my hands, and among the things I wanted to do was improve as a programmer. After being admitted, I decided to attend the [Recurse Center](https://www.recurse.com/) (RC), a sort of self-guided retreat for programmers located in SoHo in Manhattan. 

### Application
The application process was pretty straightforward. There's an online form I filled out which includes writing fizzbuzz and submitting another piece of code of my choice. I chose a portion from [Plumfeed](https://plumfeed.com). The next step was a Skype interview where we chatted about programming, and what I hoped to get out of Recurse. It was more of a personality interview. The final task was to submit a basic coding project from a few choices and do a pair-programming Skype interview. Then I was accepted! Typically, batches are twelve weeks which is a pretty long time. I elected to do a half-batch at six weeks starting in November of 2016. There was a break for Thanksgiving and it ended around the 14th of December. Most of my batch-mates remained for the entire twelve weeks.

### The people
There were about 60 people from all walks of life who attended Recurse while I was there. The batches overlap, so half the people were new, and half had already been there for six weeks. There were some with years of programming experience like myself. Others were only a couple years out of school and wanted to see if software was for them instead of their chosen major. Some were exploring career changes. Since it requires a lot of time off, everyone was in a sort of life crossroads. Generally it skewed younger, but as a 33-year old I didn't feel out of place. It didn't hurt that RC really tries to filter for friendly, decent human beings, so it's easy to feel comfortable.

### The environment
The Recurse Center looks like a typical startup office. There's a big open space with desks in different configurations, a small kitchen, bathrooms, a few conference rooms, and a small area reserved for the few Recurse employees. There's a space for larger gatherings, and they have all the various wires, connectors, adapters and plugs you need. Even though it's an open floor plan, it's kept pretty quiet. Groups typically huddle in a conference room or chat quietly to the side. I found it to be very conducive to productivity. There are also a few social rules they ask everyone to follow, most notably, no feigning surprise. Basically avoid this:

> Person A: You should use ExpressJS for this.<br/>
> Person B: What's ExpressJS?<br/>
> Person A: You don't know what Express is!?

Naturally, people aren't going to know everything, especially in a diverse group with a wide variety of programming expertise. Even the more experienced among us have gaps in our knowledge. Personally, I love this rule. It makes everyone more comfortable asking questions which is obviously perfect for a learning environment. I hope to carry this with me.

### Day-to-day
Most people arrive in the morning, find a place to sit with their laptop and get to work. Everyone is on [Zulip](https://zulip.org/) which is kind of like Slack. It's handy for organizing lunch outings, asking questions, seeing if anyone is free for a code review, or just private chat. At some point in the afternoon, folks typically have lunch, either takeout or packed from home. After that it's more of the same. Variety is really up to the student. In addition to building my primary project (more on that soon), I also spent some time doing code reviews, interview prep, participating in [SICP](https://mitpress.mit.edu/sicp/full-text/book/book.html) discussions, and just getting to know my batch-mates. There's also some Recurse organized events including game nights, speakers, and people share quick presentations on what they're working on every Thursday. There's also several ad-hoc events that Recursers themselves organize like going to an Islanders game, karaoke, watching Mr. Robot, or enjoying a potluck dinner.

### Location
Recurse is not far from the Canal St. subway stop which has several subway lines going through it. In SoHo there are several nearby coffee shops, restaurants, and food carts. The lamb over vegetables at the Halal cart at the corner of Houston and Broadway was the best. Ask for the green sauce.

### Costs
Recurse is free to attend. The only thing they ask participants is first ask their on-site recruiting team first if they are looking for a job after RC. That's it. They have several connections to companies in NY and SF and a few elsewhere. If they match someone with a job, they earn a recruiting finder's fee from the company. This is their primary source of revenue.

The largest expense for participants is housing. I found my Airbnb in Brooklyn, near some friends (Hi Eric!) and the Prospect Park subway station which gave me a convenient 30 minute commute. Admittedly, I was eager to find a place to stay and I probably paid a little too much. If I was more patient, I might have been able to share a room with a Recurser who was already there. There are also some need based scholarships available.

### My project and experience
I really wanted to do something lower level. Most of my programming career has been within the friendly confines of virtual machines, so I wanted to do something less comfortable: I decided to write a keyboard driver. Originally, I considered trying it in Rust, but James, a resident, convinced me that C would be a more prudent choice (and he was right). I didn't exactly understand what I was getting into, but, well, that was the point. I was fortunate enough to meet [Andrea Orru](http://andreaorru.com/) at RC who happens to be a gifted systems programmer and an all-around friendly guy. He pointed me to a few tutorials for compiling to bare-metal, writing a small bootloader, and finally, a keyboard driver. These tutorials assumed different operating environments, C libraries, and even had different assemblies. It was no small task to tie them together (at least for me), so again, thanks Andrea for all the help!

![Keyboard driver in action]({{ site.url }}/assets/recurse-center/keyboardDriver.png)

To summarize, I had to:
* Recompile GCC to be a cross-compiler for bare metal. This means that the C compiler couldn't assume there would be any libraries that are typically provided by an OS
* Use [QEMU](http://qemu.org) (pronounced Q - EM - YOU), which is a virtualizer for the x86 processor. This is much nicer than having to reboot a computer to test my little OS all the time
* Get more comfortable with C. I knew enough, but it was helpful to get more familiar with it.
* Learn assembly. I'm still not great at it, but I understand it better now
* Build secondary bootloader layer, (QEMU has a thin first layer I believe)
* Learn about Descriptor tables, displaying stuff on screen with VGA, and interrupt handling
* Handle all the things we take for granted in a basic text editor like backspace, return, and shift

I also added an Easter egg where if you slam on the keyboard, it'll print the message, "Relax, it's going to be ok."

It's all [here in my Github repo.](https://github.com/newtang/bare-metal-hello)

Additionally, I built a small under-featured [prototype of an IRC server in C](https://github.com/newtang/cIRCServer) to learn more about [sockets](https://beej.us/guide/bgnet/output/html/singlepage/bgnet.html).

### Recommendations for future Recursers
I loved my time at the Recurse Center, and if you have some free time I highly recommend it. I got to befriend some great people, and I had the time, space, and resources to learn a ton. I felt that I was able to participate and be of use to others. Additionally, as an alumnus, I'm a part of their alumni community, so I feel like I have a great group of people I can reach out to on Zulip for programming, work, or even life questions. Alumni in the New York area frequently visit the space after hours to work on their own projects, check out what people are working on, or even just mingle. If you have the time, I would suggest 12 weeks over 6 weeks. I felt like I was really starting to make some connections just as it was time for me to leave. If you do a 6 weeks, I would suggest having a strong idea of what project you want to work on ahead of time. A lot of people spend some time early in their batch tinkering and thinking about what to do, but the 6 weeks goes by much faster than you would expect.

Hooray for Recurse!




