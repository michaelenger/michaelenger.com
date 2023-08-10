---
title: "Brage 2.0: Redesigning My Static Site Generator"
date: 2023-09-10
---

## The Beginning

Recently I was inspired by some friends to start a blog. This won't be my first foray into the Blogosphere™ as I ran a personal blog from about 2005 to 2011 where I wrote about games, programming, and all sorts of nerd stuff. The blog came to an unceremonious end when my GoDaddy domain expired and instead of telling me about it they sold it to some rando who scraped all my content and resurrected the blog under his control alongside ads for brain enhancing drugs. I stopped using GoDaddy and stopped blogging...

Until now! More than a decade has come and gone, I'm at a different job in a different country and I have a lot more tattoos. So the way I am approaching this new blog is going to be a bit different than last time. What I'll write about will probably be more of the same, but the more interesting thing is what tech I want to use.

The old blog was run on various systems, at one point Tumblr, at one point WordPress, and for a long time I was managing a home-made PHP application. But as time has changed me it has also changed web technology. I was there to witness the commercial world go from PHP monoliths to Node.js microservices to Single-Page Applications, and if I am honest the progress has made me weary. To say I have JavaScript fatigue is to grossly misrepresent my resentment.

However, one trend that I have embraced fully is static site generators. They take source files, usually written in [Markdown](https://en.wikipedia.org/wiki/Markdown), and combine them with some assets and some styling to spit out flat HTML files. Some of them still insist on also running an SPA on top, but most just produce a bunch of files that can be uploaded to any number of hosting services.

Enter [Brage](https://github.com/michaelenger/brage), a static site generator I wrote in Swift and then re-wrote in Go. I created it because the other static site generators I was looking into (like [Jekyll](https://jekyllrb.com/) and [Hugo](https://gohugo.io/)) felt too heavy and cumbersome for my needs. Why were they too heavy? Ironically because they were focused on supporting blogs whereas I just wanted simple websites.

I originally built Brage to help me maintain the websites for [Young Fatigue](https://youngfatigue.com/) (a band I'm in) and [Morten Myklebust](https://mortenmyklebust.com/) (an artist I'm friends with), alongside my own. I wanted to be able to create the pages in HTML and Markdown, and have the generator combine it with a layout so that I didn't have to change every page manually every time I made stylistic changes. I also wanted to be able to provide some data (in YAML format) and have a template engine turn it into HTML. For example, [my portfolio](https://michaelenger.com/code/) is defined as a bunch of lists containing meta data for each entry. This makes it super easy to add or remove things. The source for this website is [available on GitHub](https://github.com/michaelenger/michaelenger.github.io) if you're curious as to how it all fits together.

But now that I _do_ want to blog my options are to either use an existing generator, or to modify Brage to support it. And what kind of massive nerd would I be if I didn't choose the latter?

## The Design

_Note: I'm writing this post after the work has been completed, so I can (and do) omit any mistakes or bad ideas I originally had._

Like all good software projects I started with a list of requirements... which I subsequently ignored when the real work began. I like using lists to collect my thoughts, but as this project was for my own personal need it was bound to changed as I went along. The overall goal was to support some sort of time-based content with the following traits:

* They all live under a specific URI (it ended up being `/blog`).
* They contain meta data: title and date. I also wanted to have tags at some point, but they seemed to add a lot of complexity so I dropped it.
* I will be able to list them all out in one of my templates using some variable (which turned out to be `site.posts`).

Since I was already going to do a bunch of work I also had some improvements that I wanted to implement:

* Support for other file extensions. I originally only supported `.html` and `.markdown`, but I very much wanted to use `.md` for Markdown files.
* Change from using Go templates to using [Mustache](https://mustache.github.io/). This was because I wanted the templating to be independent of the programming language I used. When Brage was written in Swift I used [Stencil](https://stencil.fuller.li/) so when I changed to Go I had to change every single template in all the sites. I needed to do that again if I were to migrate to Mustache, but at least I won't have to do it a fourth time if I ever get tired of Go.

I want to take a moment to consider the philosophy behind a bunch of my decisions. As this project has a target user base of one person I don't really have to take into consideration how others _may_ want to use it. I know exactly how I would want to use it so I could avoid a lot of complexity by not implementing any other options. I know that frameworks like Ruby on Rails gets some flak for their adherence to [Convention over Configuration](https://en.wikipedia.org/wiki/Convention_over_configuration) but in this case it didn't really matter. If nobody other than ever uses Brage it would still be a successful projects, so I can save a lot of time by just making it do only what I want.

This is the justification for a decision that sets my site generator apart from many others out there: it doesn't use themes. With other generators you define your content and it will combine that with a theme, either pre-made or created by you, to generate the final HTML. Since I wanted Brage to help me manage a small handful of wildly different sites it was overkill to add any sort of theming support. The styling, structure, and content all live in a single directory and I just point Brage at it and we're done.

However, a drawback to not having themes is that I then can't share templates between projects, but that was an easy problem to just ignore. I already had support for partials so I could easily copy files between projects if I needed to. Brage has already been super useful for the past two years, so I had no desire to add any sort of complexity for a feature I'd probably not use.

## The Implementation

When I started working on version 2.0 I _almost_ made the mistake that a lot of developers make: I almost started from scratch.

I was really close, too. I had created a `v2` branch in the project's repository and had removed everything other than the `README`, `LICENSE`, and `main.go` files. Then I though I didn't want to redo a bunch of the grunt work so I copied over some utility files from the `main` branch... followed by a bunch more files... and then some more, before I finally stopped and realised that I was being an actual idiot.

This may be my personal project with a target demographic of one, but am I not a professional programming person? Haven't I been doing this for like two decades? There is an inexplicable pleasure associated with starting fresh new projects, but I have, like many of my peers, a trail of abandoned half-finished code bases behind me. Ones that once had me enraptured for weeks but were eventually left to languish as my fickle attention was turned elsewhere. I realised that throwing it all out and starting with a blank slate was a recipe for not getting it done.

So I bit the bullet, got all the deleted files back, and waded into my own many-months-old legacy code to make changes. And given that I am writing this after having successfully completed Brage 2.0, I do not regret my decision at all.

The work went surprisingly fast, probably due to the fact that the code was relatively new and my patterns hadn't had time to change much. I worked in a way that I felt was ambitious but allowed for clear, gradual progress: Brage has an `init` command which spits out an example site that works as boilerplate, but also can function as documentation for how to structure the site. So I just changed the command to spit out a site in a format that I wanted Brage to eventually support. That way I would have a static target to aim for while I made changes.

This ended up being a great idea as I was essentially doing Test-Driven Development without writing any tests. The test was just whether the site looked like I expected and didn't have any errors when rendering. Of course as I was modifying the generator to work with the new templates and configurations I was also cleaning up and refactoring as I saw fit. This was my own project after all, so I could do exactly as I pleased.

## The Result

[Brage](https://github.com/michaelenger/brage) is now in version 2.0 and supports blog posts! It uses Mustache and Markdown templates, and lets you define any kind of extra data using YAML. It comes as a single executable and with it you can easily create sites that sit in a single self-contained repository with no dependencies.

I didn't get to do all the things I wanted, but I try to follow a philosophy of focusing on getting the first 90% done and leaving the other 90% for later. It's incredibly rewarding to have a project that I can actually use, and if I continuously use it I will get inspired to work on it even more later. That way I can (maybe) avoid adding it to the pile of ~~corpses~~ abandoned projects I've left in my wake.

As to whether this is the beginning of a decade of continuous writing, or if I will end up publishing one thing every few years and then giving up is yet to be seen. The most important thing is that I had fun building Brage. For myself.
