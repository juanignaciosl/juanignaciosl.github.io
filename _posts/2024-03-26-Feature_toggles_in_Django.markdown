---
layout: post
comments: true
title: 'Feature toggles in Django'
date: 2024-03-25 01:18:57
categories: ['Development']
tags: ['Software Development', 'Python', 'Django']
excerpt: | 
    List of available solutions for feature flagging in Django
image:
    feature: toggles_1388x800.jpg
---

## Feature flagging libraries for Django

|     | Library               | Doc                                             | Repo                                                   | Last update | Pypi                                                    | Last release |
|-----|-----------------------|-------------------------------------------------|--------------------------------------------------------|-------------|---------------------------------------------------------|--------------|
| 💀  | django-feature-toggle |                                                 |                                                        | 2021-11-11  | [Pypi](https://pypi.org/project/django-feature-toggle/) | 2018-02-25   |
| 💀  | django-experiments    |                                                 | [Repo](https://github.com/mixcloud/django-experiments) | 2023-07-25  |                                                         | 2016-09-16   |
|     | django-flags          | [Doc](https://cfpb.github.io/django-flags/)     | [Repo](https://github.com/cfpb/django-flags)           | 2024-03-19  |                                                         | 2023-06-08   |
|     | django-waffle         | [Doc](https://waffle.readthedocs.io/en/stable/) | [Repo](https://github.com/jazzband/django-waffle)      | 2023-12-11  | [Pypi](https://pypi.org/project/django-waffle/)         | 2023-12-11   |

This [Django packages listing about feature flipping](https://djangopackages.org/grids/g/feature-flip/) is useful too.

## Related libraries

|     | Library                         | Repo                                                   | Last update | Pypi                                                    | Last release |
|-----|---------------------------------|--------------------------------------------------------|-------------|---------------------------------------------------------|--------------|
|     | wagtail-flags (django-flags UI) | [Repo](https://github.com/cfpb/wagtail-flags)          | 2024-01-08  | [Pypi](https://pypi.org/project/wagtail-flags/          | 2023-03-10   |

## Django-flags vs Django-waffle

|                                | Django-flags | Django-waffle |
|--------------------------------|--------------|---------------|
| Flag definition                | Code         | Admin         |
| Flag condition setting         | Admin        | Admin         |
| Flags at URLs                  | Yes          |               |
| Condition: boolean             | Yes          | Yes (switch)  |
| Condition: user                | Yes          | Yes           |
| Condition: anonymous           | Yes          | Yes           |
| Condition: URL parameter       | Yes          | Yes           |
| Condition: path                | Yes          | No            |
| Condition: language            | No           | Yes           |
| Condition: group               | No           | Yes           |
| Condition: superuser/staff     | No           | Yes           |
| Condition: percent             | No           | Yes           |
| Condition: date (before/after) | Yes          |               |
| Condition: custom              | Yes          |               |
|                                |              |               |

Please note that 👆🏻 is about out-of-the-box features, and many of them are easily implementable.

## Django-Waffle

It looks like a winner because of feature set and more recent updates.

- Three different types: flags, switches and samples.
- Custom Flag model.
- Support for JS and JSON.
