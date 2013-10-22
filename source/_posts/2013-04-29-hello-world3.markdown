---
layout: post
title: "my favorite 'prompting' tips"
date: 2013-04-29 08:46
comments: false
categories: 
---

## Something has failed alert

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

``` bash
prompt="\w$(__git_ps1)""
```

## Don't mess with the commit

``` bash
git status 2> /dev/null | grep -c : | awk '{if ($1 > 0) print "⚡"}'
```

## BONUS: Jumping to git root directory

http://stackoverflow.com/questions/957928/is-there-a-way-to-get-to-the-git-root-directory-in-one-command

``` bash
alias git-root='cd $(git rev-parse --show-toplevel)'
```

Since I can't search properly into my feeds, I cannot give a shout to any of the usefull blogs, which I took those tips from.

#### Resources ####

- probably the most useful [page](http://mywiki.wooledge.org/BashPitfalls) about bash on the whole internet
- pretty interesting [interview](http://www.computerworld.com.au/article/222764/a-z_programming_languages_bash_bourne-again_shell/)