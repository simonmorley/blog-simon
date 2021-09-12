---
title: ⌨️ My Lil' Gatsby Blog On Cloudflare Workers.
date: "2021-09-12T10:40:32"
template: "post"
draft: false
slug: "gatsby-blog-cloudflare-workers"
category: "Code"
tags:
  - "Software"
  - "Tutorials"
description: "Over the last 10 years I've successfully managed to not keep a blog published. I was fussy where to hosting it - wanted to avoid Medium, Wordress etc. After 5 or so years using Cloudflare, I discovered their workers and thought they'd be a perfect (and cheap) solution."
---

For as long as I can remember, I've been trying to buy the domain simonmorley.co.uk. Alas, every time I checked it was taken. The whois records were always hidden. Sometime in 2020, I was looking through our list of domains owned by my company - and there it was. We'd been renewing it since 2008 or something. Who'd have thought!

Writing regular (interesting) blogs is time consuming. Hosting them even more so. I supposed I could have put it on Medium but that's not my thing.

In 2019, I took a year off to train for Ironman (see other posts). It seemed fitting to start a blog - I created simontrains.com because I hadn't realised I owned simonmorley.co.uk :| Anyway, we still had some Kubernetes services running so I just used that.

After shutting the company down, the luxury of Kubernetes was no longer available and I'd discovered Cloudflare workers when looking around for something to create a landing page with. Workers seemed pretty flexible so I thought it'd see if we could run a blog on them too.

Here I'll talk about installing and using a framework that I don't like very much to create a blog for myself.

## The Technology

- Gatsby - tool
- Cloudflare workers - hosting
- My MacBook - laptop

## Getting Started

Ok, so create yourself a Cloudflare account if you don't have one. If you don't have one, go get one now anyway and make sure all your sites are protected by them. They've literally changed the way we work - I'm not affiliated with them at all.

Once you have a site, create an API token and grab your zone ID too - it's on the front page.

## Choosing Gatsby

Initially I started with Hugo. It's written in Go, which is my 'go' to language of choice these days. It's fast, simple and generates html - not some javascript nonsense. However, I really couldn't find a nice template for it - yeah I'm that shallow.

So I settled on Gatsby. The html it renders (or doesn't) sucks because it's React, but alas - I'm not looking for SEO.

We're moving our company site from the horrendous Wordpress service we're using right now - Hugo will do nicely for that - will create another blog for this.

**The Bad With Gatsby**

I followed the guides over [on the Gatsby site here](https://www.gatsbyjs.com/docs/tutorial/part-0/). But things weren't going so well after I installed a theme. Usually I like to start from scratch with everything. However, I remembered this was supposed to be a hobby project and didn't require me to understand every line of code. I still hate myself for this a little.

I had to downgrade Node to 14.something which annoyed me a lot.

This is why I prefer Hugo - none of this added npm / node / react gatsby version misery.

**Installing Stuff**

Do this first (after you've installed brew, nodejs etc).

`
npm install -g gatsby-cli
`

Very good, I will continue.

If you're in a hurry, **don't** do this:

`gatsby new`

Instead, find a template you like. Then do this:

`gatsby new blog-simon https://github.com/alxshelepenok/gatsby-starter-lumen`

That'll install & melt your laptop in the process. Seriously, another reason to hate myself for using Gatsby.

`cd` into the directory you just created and start the server:

`gatsby develop`

Visit your site on `http://localhost:8000` or something like that. Winning at life. I hope it works for you!

## Creating Content

I added my markdown files into > posts > year because that's what I wanted to do. You can use whatever structure you like, Gatsby will pick them up.

![](/media/2021/0912/filesystem-01.png)
*Yes I'm using atom to write my posts, don't judge me. It's just for the blog*

The post looks like [this](https://github.com/simonmorley/blog-simon/blob/main/content/posts/2021/0901-01-drone-pics-bromley.md). In case you're interested.

## Using Wrangler To Deploy

[Wrangler](https://github.com/cloudflare/wrangler), created by the Cloudflare folks, lets you fiddle with your Workers.

Install it first, pretty obvious:

`npm install -g @cloudflare/wrangler`

Then run `wrangler config` to get things rolling.

And then finally, you need to set things up for your Gatsby project:

`wrangler init --site blog-simon`

This should generate a wrangler.toml file like this!

```
account_id = '39fxxxxx'
name = "blog-simon"
type = "webpack"
route = 'simonmorley.co.uk/*'
usage_model = ''
compatibility_flags = []
workers_dev = false
zone_id = 'xx'
site = {bucket = "public", entry-point = "workers-site"}
```

Add the bits you need.

Gatsby needs to build everything before your deploy. The following command will do that:

`gatsby build`

And now run `wrangler publish` which should look like this:

```
✨  Built successfully, built project size is 13 KiB.
🌀  Using namespace for Workers Site "__blog-simon-workers_sites_assets"
✨  Success
🌀  Uploading site files
✨  Successfully published your script to
 simonmorley.co.uk/* => stayed the same

💁  Deleting stale files...
```

I had to go add a CNAME record to my CloudflareDNS settings. I also upgraded my plan so the whole blog now costs about $5 per month.
