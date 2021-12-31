---
layout: post
comments: true
title: 'December 2021 reads'
date: 2021-12-30 01:18:57
categories: ['reads']
tags: ['Software Development','AWS', 'Engineering Management']
excerpt: |
    Some interesting content that I ingested during December 2021.
image:
    feature: navidad2020_1388x800.jpg
---

Some interesting content that I ingested during December 2021.

# PostgreSQL

- [The Surprising Impact of Medium-Size Texts on PostgreSQL Performance](https://hakibenita.com/sql-medium-text-performance).

# Engineering Management

- [**On Schelling Points in Organizations**](https://medium.com/@komorama/on-schelling-points-in-organizations-e90647cdd81b).
- [**Software economics** book](https://leanpub.com/software-economics), by Luis Artola.

# Bonus: moved personal blogs to S3

[Simple site hosting with Amazon S3 and HTTPS](https://www.freecodecamp.org/news/simple-site-hosting-with-amazon-s3-and-https-5e78017f482a/)
is a good guide.

1. Created two buckets, with and without `www.`, with the content at `www.` and the other being a redirect.
2. Used [wwwizer.com](http://wwwizer.com/) so the empty domain points to `the www` one. This seems convoluted, but
   my DNS provider won't allow me using `CNAME` for the bucket. Thanks to
   [this SO answer](https://stackoverflow.com/questions/8312162/static-hosting-on-amazon-s3-dns-configuration).
3. [Configured bucket policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/website-hosting-custom-domain-walkthrough.html#root-domain-walkthrough-create-buckets).
4. Configured a `A` DNS registry to `174.129.25.170` (wwwizwer), and a `CNAME` with `www` to the `www.` bucket.
5. [Check this for SSL and S3](https://towardsdatascience.com/static-hosting-with-ssl-on-s3-a4b66fb7cd00) and
   [this for multiple domains](https://aws.amazon.com/premiumsupport/knowledge-center/redirect-domain-route-53/).

Bonus 2: [Marginalia search engine](https://search.marginalia.nu/)

> In recent years, something has been simmering: Some call it the "Small Internet". I hesitate to call it a movement, that would imply a level of organization and intent that it does not possess. It's a disjointed group of like-minded people that recognize that the Internet has lost a certain je ne sais quoi, it has turned from a wild and creative space, into more of shopping mall. Where ever you go, you're prodded to subscribe to newsletters, to like and comment, to buy stuff.
>
> ...
> 
> The purpose of the tool is primarily to help you find and navigate the non-commercial parts of the internet. Where, for sure, you'll find crack-pots, communists, libertarians, anarchists, strange religious cults, snake oil peddlers, really strong opinions. Yes all manner of strange people.
