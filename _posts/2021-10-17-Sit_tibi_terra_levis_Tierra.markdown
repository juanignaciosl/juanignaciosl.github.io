---
layout: post
comments: true
title: 'Sit tibi terra levis, Tierra'
date: 2021-10-17 01:18:57
categories: ['Tierra']
tags: ['Software Development']
excerpt: |
    In August, 24th, I received an unexpected, sad piece of news: we were closing Tierra. This is a brief farewell.
image:
    feature: tierra.png
---

In 2019, July, I received a cold call through LinkedIn from an unknown person, Marusia Musacchio, who had been
redirected to me by an old colleague. She explained that they were building an analytical tool with crime data
for making data-driven safety decisions. Violence and crime are terrible problems in Latin America, and the idea was
trying to come up with a solution that helped to reduce that (and making a profitable company, otherwise you
couldn't help). After some meetings, I was in love with the offering and the team, and my journey as Tech Lead at Tierra
begun.

_As a side note, I had to reject an offer from another great company, an offer that I had been baking for many months,
but I had a realization: I cared way more about project and product than about technology._

# The challenge

They already had a good-looking product, mostly designed by Chris Wetherell (yup, the creator of Google Reader, and I
was going to have the honor of working with him) that showed risk scores and incident counts on a map. It was a great
MVP. There were some problems in the frontend, mostly because of limitations of the Google Maps SDK, and in the
backend, because of a DB design that couldn't support some use cases that had appeared since the original design, and
because of the absence of a reproducible process of creation. It was a manually crafted DB from sources, and the
release of new versions implied replacing the full database. No infrastructure-as-code either, and some downtimes had
been way too long.

# The job

Frontend was problematic because Google Maps SDK is not great for data-intensive applications. My first attempt was
removing Google Maps, but its geocoding service is orders of magnitude better than any other at Mexico, and because
of draconian clauses you can't easily mix mapping providers, so we had to keep it. Luckily enough,
[Deck.gl added official support for Google Maps](https://medium.com/vis-gl/using-deck-gl-with-google-maps-9c868d18e3cd).

Then, we improved the DB schema (["There is always a schema"](https://javisantana.com/fastdata/40-things-I-learned-about-data.html), 
but you don't have the full picture at the beginning, so be ready to evolve it) and crafted tooling that allowed
minimum manual work from data acquisition to production deployment, including data QA and incremental release.

And there was a team to lead and manage, and a pandemic is not something that we're trained to deal with, so we did
our best.

# The result

We came up with a working product that we were proud of, and many customers used in a daily basis. The team was
performant and happy. It felt like it was our dream job.

But life is sometimes unfair. I can't disclose details, but despite everybody, from bosses and board to the full team,
was happy with what we had built, the engine had to be switched off. Sometimes things just can't work.

# The outcome

Closing was a shock, but I'm happy. Everybody agrees that we did the best that could be done.

I've got the most out of many learnings at previous jobs and improved my developing and management skills.

But most importantly, I've met and worked with incredibly skilled professionals, which also happen to be the kindest team of people you
could imagine. I could also get in touch with people I already knew and loved.
I'd work with all of them again (and I'm lucky, I already know that I will, at least, with some).
Every single person deserves many words of thanks here, but I'll just say something about our CEO:
Marusia is the best leader I've ever met.

![Tierra team](/img/tierra-team.jpg)

The next step is already decided, and it'll begin tomorrow. Excited! It'll be challenging and, hopefully, fun :).

I love you, _Terricolas_ ♥.
