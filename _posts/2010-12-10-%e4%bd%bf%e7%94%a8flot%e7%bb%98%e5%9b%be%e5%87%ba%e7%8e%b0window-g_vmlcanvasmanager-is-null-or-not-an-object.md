---
layout: post
title: 使用flot绘图出现window.G_vmlCanvasManager is null or not an Object
date: 2010-12-10 13:14
author: afeilulu
comments: true
categories: [program]
---
使用flot绘图出现window.G_vmlCanvasManager is null or not an Object

导致ie无法绘图

在firefox上不会报错

解决办法是在加载jquery.flot.js之前先加载excanvas.js

&nbsp;
