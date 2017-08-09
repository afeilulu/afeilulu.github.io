---
layout: post
title: get only one specified file from subversion
date: 2014-04-10 14:18
author: afeilulu
comments: true
categories: [program]
---
In same case, you don't want "garbage" files except the one you need with specified version. so, how to get it in command ?

1.checkout a emtpy directory including the file
svn co   --depth empty

2. cd 

3. svn up  -r

That's save your lots of time.
