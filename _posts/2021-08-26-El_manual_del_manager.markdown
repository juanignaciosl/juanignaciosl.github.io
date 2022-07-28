---
layout: post
comments: true
title: "The Manager's Manual (El manual del Manager)"
date: 2021-08-27 11:18:57
categories: ['learning']
tags: ['Engineering Management', books]
excerpt: |
  Notes about The Manager's Manual book.
image:
  feature: el_manual_del_manager.png
---

I've managed teams of different sizes and expertise for more than 10 years now, under mixed roles, still being
a hands-on developer as well. For example, being a Technical Lead includes responsibilities regarding
managing the Engineering team, with 1 on 1s, leading Engineering Replenishment meetings decisions, ORKs, Performance
Reviews or technical strategy. I've learned by doing and reading, but I hadn't opened a new management book in a while. While I love classics
like [Peopleware](https://juanignaciosl.github.io/learning/2018/01/12/peopleware.html),
[Management 3.0](https://www.juanignaciosl.com/ingenieria-del-software/resena-management-3-0),
[Agile Retrospectives](https://www.juanignaciosl.com/ingenieria-del-software/agile-retrospectives)
or [The Mythical Man-Month](https://www.juanignaciosl.com/ingenieria-del-software/las-mismas-discusiones-durante-33-anos),
this is a good moment for a refresher and new topics, and this book by [Keyvan Akbary](https://twitter.com/keyvanakbary),
[Félix López](https://twitter.com/flopezluis) and [Álvaro Salazar](https://twitter.com/xala3pa) looked like the perfect
candidate.

~~Disclaimer: the book is in Spanish (although I'd bet that they'll eventually translate it,
[its domain is in English](https://managersmanual.com/) 😬), and it's only
"45% complete" (last updated on 2021-04-03).~~

New disclaimer: the book is available both in [Spanish](https://leanpub.com/el-manual-del-manager) and
[English](https://leanpub.com/the-managers-manual), and it's still in progress. This blogpost is based on the Spanish
version. My plan is updating this post as the book grows.

28/7/2022 UPDATE: added "Career path" chapter.

# Summary/notes

## The Manager Role

The chapter starts with a brainstorming of tasks and skills related to the role, such as empathy, dealing with
people that you don't actually agree or the slower feedback cycle (compared to technical roles).
The last section is a good ending for the chapter, with an in-depth description of what an Engineering Manager is,
including the difference with Tech Leads.

One of the things that I wasn't sure about the expectations of this role is whether you're expected to keep coding.
According to the authors, you should (as much as your other duties allow you), with a catch: don't get into
the critical path of the team. I really like this. I've followed this approach by investing my developing time
on fixing bugs. That allowed the rest of the team to focus on new features while I maintain a close understanding
of the codebase.

## Feedback

I definitely see this chapter as one that I'll revisit from time to time. It has a lot of recommendations that you
can't disagree with, and might be obvious, but they're easy to overlook under some situations like stress of lack of
motivation: include positive, specific feedback but don't use it as a sandwitch around bad one; prepare the feedback
depending on the listener, ask for explicit confirmation...

It really surprised me knowing that the Engineering Manager course at Google tells not to give third party feedback. Is
that all, or they suggest beginning some kind of research? I think that the proactive approach that the authors dig into
is WAY more valuable (this might be one of the best sections in the book).

It ends with the SBI (Situation, Behaviour, Impact) framework, a nice guide/template for structuring a healthy conversation.
The suggestions about balancing mentorship and coaching depending on the person you're talking to are key. In my experience,
this is one of the hardest things to do, as no size fits all, or not even the same size for the same person in two
different moments.

## One on one meetings

1:1 meetings are an absolute need if you want to build trust relationships, and this chapter explains very well the
whys (burnout, confidence, personal development...) and the hows (frequency, reinforcement feedback during the
conversation, shared document...).

Personal note: a classic and great recommendation is not relying on plain, dull "how are you?" or "how are things going?"
questions. But I wouldn't ban them. Once you've built a solid relationship with the team, and people trust you and
understand the value of the meeting, many reports get to the meeting with a really open mindset, making things flow
naturally. While it's definitely not something I would do with everybody, 1:1 within my last team become very pleasant
and useful quite soon. The suggested "Do you have an agenda or anything that you'd like to do?" was often enough for
valuable conversations. I prefer the open, not yes/no alternative of "What would you like to talk to?" or similar, though.

It contains a large list of question suggestions that are very useful if you want to introduce changes and you don't have
many ideas, or as food for thought about how to tackle problems depending on the topic.

The last section of "other kind of 1:1s" is very interesting and new for me: catchups and skip-levels.
I've already experienced some of them, but having structured information about them is useful. BTW, I'd be explicit
and tell managers that I'll do skip-levels, because some people might see it as a sign of lack of trust.

## Career path

When this chapter was added to the book I expected a dull suggestion for a career ladder (a table or a template that
you're told to apply), but it's more interesting
than that (although maybe too short): it explains why, when and how you should create one.

The _why_ part is split
among "associated problems to the lack of" (expectation management, collaboration...) and "the importance"
(retention, commitment, hiring...). A broken expectation management is one of the main causes of burnout.

A reactive approach is implicitly suggested in the _when_ section: the need must exist, because it has a cost, so
context matters. A small company probably doesn't need it at all, for example.

I'd highlight that the _how_ section starts mentioning that it's an alive document, and that the creation should be
done with the collaboration of all the organization. And the path shouldn't become a goal itself, it should be a
learning lever. An interesting topic I haven't heard much about are the terminal levels: levels that must be reached.
It's similar to the "test period", but in a longer time window, and includes progression.

## Product-oriented development

This chapter explains the difference between corporate and end-user software, and the mindset and skills required for
them. The section about empowered product teams is very interesting, because the frontier between some approaches
is quite blurry and fragile. I wonder whether you need to reach a given size to have actually empowered teams, since
early startups should probably have specific hypothesis to be tested, not yet a user base to take care of.

I'd love to have live conversations with the authors to actually understand some sentences. For example, they say that
using speed of delivery (lead time, I guess) as a success metric is a smell of being a "feature factory" (opposed to
empowered team). While I definitely agree the the _lack_ of user impact metric is, the _presence_ of lead time metric
doesn't look bad to me.

There's a sentence which is an iceberg gem: "las ideas con potencial necesitarán de varias iteraciones
para entregar el valor necesario que justifique el esfuerzo" (the ideas with potential will need several iterations
to deliver the needed value that accounts for the effort). In other words, you must plan in iterations, until you can
prove or dismiss that the idea is good enough, but you should not stop too soon.

While most of the chapter is written as a company-wide description of the product-oriented approach, there are bounds
to the role of Engineering Manager, such as using OKRs.

This chapter is probably the one I'll reread the most as a retrospective tool.

# MHO

This is valuable content from many people. I'd say that it's a must-read for anybody facing a potential role
change. I wonder whether more work on the structure would pay off, though. For example, some chapters feel like
brainstormings from conversations or tweets. On one hand, it might be very friendly for somebody that
wonders if the job change would be enjoyable. But on the other, if you have some experience you might prefer more
in-depth thoughts. They're quite honest about this on the "book audience" section, so I guess that it's a matter of taste.

For experienced profiles, it provides a good amount of "next steps", with links and mentions to research and topics.
Given the approach the book has, I'd love an explicit table of extra sources at the end of each chapter, as the book Facts and
fallacies of Software Engineering has, for example.

I loved the feedback one. It's very detailed, goes beyond the obvious and it's very actionable. Product-oriented one
is also great if you want to set a very high quality standard in your organization.

Final comment: it's written using "generic feminine". RAE should lead the abolition of the generic masculine, but
if they won't, we should. RAE is not _prescriptive_, it's _descriptive_. This kind of actions pushes the language
forward.
