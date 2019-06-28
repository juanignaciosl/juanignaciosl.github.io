---
layout: post
title: '"Conventional wisdom"'
date: 2019-06-28 9:18:57
categories: ['micro']
tags: [Software Development, thoughts]
excerpt: |
---

If you read [Optimizing Schema for Cloud Spanner](https://cloud.google.com/spanner/docs/whitepapers/optimizing-schema-design) you'll find this (italics are mine):

> Application developers love using database sequences (or auto-increment) to generate primary keys. Unfortunately, this habit from the RDBMS days (called surrogate keys) is almost as harmful as the timestamp ordering anti-pattern described above. (...) _Contrary to RDBMS conventional wisdom_, we recommend that you use real-world attributes for primary keys whenever it makes sense. This is particularly the case if the attribute is never going to change.

Telling that to my Database teachers at University would make them angry, as surrogate keys technique was (is?) actually an anti-pattern (and a way to fail the exam). Surrogate keys are either the lesser evil or bad practice.

_Please note that this is not a critique to Google documentation but a random thought about "conventional wisdom"._

As projects become more complex we tend to commoditize the wisdom into technology, often without understanding what's happening under the hood or even learning best practices. If several developers are asked about the same (supposedly standard) topic, contradictory answers can be collected. This leads to poor decisions and, in the long term, an industry without solid foundations.

Taking some wisdom as "conventional" is a risky assumption. Maybe we should strive for deeper, long-lasting knowledge before taking anything for granted.
