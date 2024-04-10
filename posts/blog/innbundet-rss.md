---
title: "Innbundet: Replacing Reddit with RSS"
description: Reddit went and did an enshittification and so I built my own RSS reader. Like you do.
date: 2024-03-24
---

## The Woes of a Distraction-Free Day

I was addicted to Reddit, I can see that now. It was somewhat fortunate that last year when Reddit shut out 3rd-party apps in an effort to further monetise their user-base [Apollo for Reddit was one of the victims](https://www.theverge.com/2023/6/8/23754183/apollo-reddit-app-shutting-down-api). I was in the habit of checking Apollo every morning when I woke up and every night when I went to bed, as well as several times a day whenever there was any free time to be had. My decision to stop using Reddit after the massacre was partially driven by a desire to take a stand against platform [enshittification](https://en.wikipedia.org/wiki/Enshittification), but also as an act of self-preservation.

Unfortunately I was then left with nothing to fill the gaps when my social-media-damaged brain needs distraction from the mundanity of existence. So to stave off the existential dread I began frequenting Hacker News, Lobsters, Instagram, and Mastodon. But it was somehow not the same. I am going to be honest and say that nothing can really replace the Reddit experience, of essentially having access to thousands of niche forums under one account. The torrent of information and opportunities to engage is unique to how Reddit was structured and it's a shame its stewards are disinterested in fostering that culture.

So I needed something different. The other services I had turned to were paltry replacements mostly due to the type of content I was going to Reddit for. I wanted a self-curated, but still novel, feed of short- and long-form articles and news stories, with the ability to drill down into specific niches depending on my mood. It was coincidentally a comment on Hacker News which sparked the inspiration: RSS still exists.

## The Future is the Past

[RSS](https://en.wikipedia.org/wiki/RSS); now that is a name I haven't heard in a long time. I remember it being heralded as the glue that would keep the web together back in the [Web 2.0](https://en.wikipedia.org/wiki/Web_2.0) days, but once everyone started migrating to siloed social media platforms that bright future disappeared. RSS has been making a comeback in recent years as many (mostly nerds) are tired of the treatment we're getting from the platforms we've based our online lives around. RSS feels like the great equaliser, as you can put your content on a feed but have no means to influence how your content is being consumed by your audience. You make the feed available and if someone likes the cut of your jib they will subscribe, using whatever platform they like. You can't push your content to someone who doesn't subscribe, nor do you have a say how your content is presented alongside others, and there is no algorithm behind the user's feed that will emphasise your content over anyone else's. In a world where even [GitHub is afraid to let you curate your consumption](https://www.theregister.com/2023/09/13/github_alienates_customers_by_force/) going back to RSS feels like a breath of fresh air.

So that's what I did! I did some research and ended up using [Reeder](https://reederapp.com/) to collect RSS feeds. I would still browse Hacker News and Lobsters, but when I found an article I liked I would look to see if the person had an available RSS feed to add to my collection. This was slow going and I've only added a few dozen feeds, but it was nice to feel like I had a bit more control over what kinds of articles I was presented.

![Screenshot of the Reeder app running in macOS. It consists of three columns, one with a list of feeds where All Items is selected, one with the list of all feed items where an entry from Boiling Steam is selected, and the final is a preview of the feed item.](/assets/blog/reeder-screenshot.png)

However, there was a small issue that kind of bothered me with my current process. Reeder has this concept of entries being "unread" so it keeps track of the feeds which I have and haven't looked at. Since I was naturally not interested in everything that was presented the number of unread articles just grew and grew and at this point I have over a thousand unread articles. Being the kind of person who likes to keep his inbox empty and who disables all notifications unless they are absolutely vital, this ever-growing number was making me a bit anxious. It wasn't until I read [an article from Facundo Olano about making their own RSS reader](https://olano.dev/2023-12-12-reclaiming-the-web-with-a-personal-reader/) where it clicked as to what I was feeling:

> Rather than the email inbox metaphor I’ve most commonly seen in RSS readers, I wanted something resembling the Twitter and Mastodon home feed.

What I wanted wasn't a list of checkboxes that I needed to tick, but an available repository of content that I could dip into when I felt like it. Their motivation resonated with me and I was thoroughly impressed with the result of Facundo's efforts. A fully-featured RSS reader which was built to be self-hosted and gave you the experience I wanted: a self-curated feed of niche content I could browse at my leisure.

So I could theoretically have cloned and setup their reader as my own, but there was something ideological in me which disagreed with their implementation. Despite saying that they wanted to pick boring, simple technology they still had a codebase which featured two separate programming languages with their own dependency trees and build systems. It may be misguided nostalgia or just JavaScript fatigue, but I find it ridiculous that modern web development has to be so inexplicably complex when all you're trying to do is present a page with some static content.

Well, if I'm going to be such a whiner about how someone else built their project why don't I just go make one myself then?

So I made an RSS reader.

## Innbundet

![Screenshot of the Innbundet web application. Shows a sidebar with a list of feeds and a main area with a set of feed items.](/assets/blog/innbundet-screenshot.png)

[Innbundet](https://github.com/michaelenger/innbundet) is a personal RSS reader written in Go and is as basic as they come. You manage the feeds using a CLI interface and then run a server which presents a website that shows all the feeds in reverse chronological order, with images and shortened descriptions. The whole thing is served on-demand using regular ol' web technology and it runs no JavaScript, with the full page (aside from the images) coming in at around 60kb.

Even if we disregard my political opinions about Single-Page Applications, I should say that it was super easy to build a website which just receives requests and responds with HTML. I also don't have to worry about progressive enhancement, tree shaking/minifying to speed things up, getting the back button to work correctly, or anything like that. If you're looking for an excuse to use modern technology to build websites like we did back in the 2000s then this is the sign you were looking for!

That being said, the website is incredibly simplistic and requires that you have access to the server where it's running to make any changes. I can imagine that adding a bit of vanilla JavaScript as a way to make it more user-friendly is something that will _eventually_ happen, but I want to get a feel for how using the site is first. I current have a short list of features which would be nice to add:

* Ability to "bookmark" or "favourite" an article in case I want to read it later or if I really liked it.
* Adding/removing feeds via the frontend.
* Toggling the visibility of any feeds while looking at the home page as some places post a disproportionately large amount of articles and they can cause others to get lost in the noise.
* Adding categories to the feeds so I can browse what I'm in the mood for.

Note that these are all features that the aforementioned [feedi project by Facundo Olano](https://github.com/facundoolano/feedi) already supports, so if you're interested in running your own reader I would recommend you check that out instead. This project was mostly a fun programming exercise for me and just like [Brage](https://michaelenger.com/blog/brage-2-point-0/) I get a kick out of building things that are to my own benefit and fit my very specific needs.

I've been running a server for it for a while now and I'm enjoying how it's such a no-pressure environment for browsing articles. It's running on a public server so you can check it out and judge my choice of feeds if you so please: [michaelenger.com/innbundet](http://michaelenger.com/innbundet)

_In case you're curious about the name: "innbundet" is Norwegian for "bound" as in how a book is bound._

## Is RSS Dead?

I would say no. RSS is a standard, which doesn't need any single entity to keep it alive. As long as people are publishing their content onto RSS (or Atom) feeds then the standard is kept alive. Which is great, because then us weirdos can be off in a corner sharing RSS feeds while the rest of the world gets radicalised by billionaires who buy social media platforms so everyone has to listen to them.

RSS is dead, long live RSS!
