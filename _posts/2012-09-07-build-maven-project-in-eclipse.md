---
layout: post
title: Build maven project in eclipse
date: 2012-09-07 09:32
author: afeilulu
comments: true
categories: [eclipse, program]
---
If you are not familiar with maven,but you want to compile a project that organized by maven.you can do as following:

	<li>Install <a href="http://http://eclipse.org/m2e/">M2E</a> plugin for eclipse.</li>
	<li>Import maven project</li>
	<li>Right click on project,click "Run as" maven clean, and after clean,run as maven install</li>

You will get what you want on source/target directory, depending on maven pom.xml file.







