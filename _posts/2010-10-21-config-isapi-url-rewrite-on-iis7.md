---
layout: post
title: Config isapi url rewrite on IIS7
date: 2010-10-21 16:33
author: afeilulu
comments: true
categories: [program]
---
IIS7有自己的url rewire module,原来在IIS5 或者 IIS6 上面使用的isapi url rewrite 方法，一般就会被弃用了。但是，升级到IIS7你需要重新编辑rules,在rules特别多的情况下，这是一件繁琐的工作，你得测试每一个rule，以确保站点能正常工作。
尽管一般人都不会这样降级操作，但是知道怎么做，总比不知道好。
在handle mappings模块中增加下面两项 Add managed handler
1. request path : WebResource.axd
type:System.Web.Handlers.AssemblyResourceLoader
name:as you wish
verb:GET

2. request path:*
type:URLRewriter.RewriterFactoryHandler, URLRewriter
name:as you wish
verb=GET,POST
