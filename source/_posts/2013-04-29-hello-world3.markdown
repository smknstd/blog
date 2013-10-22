---
layout: post
title: "my favorite 'prompting' tips"
date: 2013-04-29 08:46
comments: false
categories: 
---

![prompt demo screencap]({{ root_url }}/images/promptdemo.png "prompting tips demo")

Who don't spend too much time on the command line ? Here are some findings. But unfortunatly since I can't search properly into my feeds [anymore](http://blog.chron.com/techblog/files/2013/03/googlereaderrip_thumb.jpg), I cannot give a shout to any of the usefull blogs, which I took those tips from.

## Something has failed alert

When something go wrong, you can notive with colored prompt. Of course it is red alert + you can use [unicode](http://goetter.fr/unicode/) !

``` bash
COLOR_RED="\[\e[0;31m\]"
COLOR_NONE="\[\e[0m\]"

if test $? -eq 0
then
    PS1="${prompt}➔ "
else
    PS1="${prompt}${COLOR_RED}➔${COLOR_NONE} "
fi
```

## Current git branch

Isn't it so comfortable to always know which branch you're working on ?

``` bash
prompt="\w$(__git_ps1)""
```

## Don't mess with the commit

And of course, wouldn't it be even more comfortable to know when you have uncommited changes ?

``` bash
git status 2> /dev/null | grep -c : | awk '{if ($1 > 0) print "⚡"}'
```

## BONUS: Jumping to git root directory

``` bash
alias git-root='cd $(git rev-parse --show-toplevel)'
```



#### Resources ####

- probably the most useful [page](http://mywiki.wooledge.org/BashPitfalls) about bash on the whole internet
- pretty interesting [interview](http://www.computerworld.com.au/article/222764/a-z_programming_languages_bash_bourne-again_shell/)
- [stackoverflow](http://stackoverflow.com/questions/957928/is-there-a-way-to-get-to-the-git-root-directory-in-one-command)'s always right