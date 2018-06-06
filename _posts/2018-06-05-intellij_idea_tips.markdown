---
layout: post
title: "Intellij IDEA Tips"
date: 2018-6-5 13:18:57
categories: tools
tags: [tools, IntelliJ IDEA, IDE, Software Development]
excerpt: "IntelliJ IDEA is a great tool, let's get the most out of it"
---

I had been a happy Eclipse user for years until I hit major performance issues with
Scala. That made me do the switch to IntelliJ IDEA that everybody was suggesting.

I've been using it for a while for all kind of languages (Scala,
Ruby, Python, Javascript...), but I wasn't getting the most
out of it at all. I've never felt as comfortable or efficient with it as I did
with Eclipse, and that's my fault. So, lately I've been pushing my skills
forward, and here are some tips that might help others as well:

- Install [Key Promoter X](https://plugins.jetbrains.com/plugin/9792-key-promoter-x).
  It shows a popup when you use the mouse for an action that can be replacecd
  with a keyboard shortcut.
- Watch [42 IntelliJ IDEA tips and tricks in 45 minutes](https://www.youtube.com/watch?v=EhZwkOawKHU).
- Read [IntelliJ IDEA Pro Tips](https://www.jetbrains.com/help/idea/pro-tips.html).
- Specific topics:
  - [Selection](https://www.jetbrains.com/help/pycharm/selecting-text-in-the-editor.html).

## Must-have shortcuts

- `Cmd + shift + a`: action search (meta shortcut, Eclipse "magic shortcut").
- `Alt + up`: "smart" expand selection.

## Some customizations

I'm not a big fan of large customizations, because defauls are often good enough
and it makes easier changing between computers. Anyway, there are still some customizations
that I had to do:

- Enable [vim mode](https://plugins.jetbrains.com/plugin/164-ideavim).
  Integration is great, you keep amortizing your knowledge and you don't
  forget it.
- Setup [IDE Settings Sync plugin](https://www.jetbrains.com/help/idea/sharing-your-ide-settings.html)
  (to mitigate the switching concern).
- A shortcut for window splitting, I often work with 3 splits.
  - I't a shame that currently you can't change splits size with keyboard. If
    you agree, upvote [the feature request :)](https://youtrack.jetbrains.com/issue/IDEA-160472).
- In CARTO (specially if you work around dataservices) you do a lot of database
    work. I'm beginning to replace the good, old `psql` with the database
    console at IntelliJ for some tasks. Having difference execution shortcuts
    there (`Cmd + Enter` for single statement, `Ctrl + Alt + Cmd + Enter` for
    everything from the caret) is really convenient if you're not only running
    queries but also installing extensions and so on. [Read about it](https://www.jetbrains.com/help/idea/settings-tools-database.html#execute_on_caret).
