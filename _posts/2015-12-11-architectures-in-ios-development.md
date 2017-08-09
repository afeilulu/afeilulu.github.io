---
layout: post
title: architectures in ios development
date: 2015-12-11 22:40
author: afeilulu
comments: true
categories: [IOS, program, Xcode]
---
In every ios project,you will see architectures i386, armv7, armv7s, arm64 are set.But what they exactly mean.

I386 is not an architecture for iOS, and not in the list of architectures in your XCode settings, however it is required for running in the simulator. When code runs in the simulator, it is running just as if it were a native app on your Mac, there's no ARM emulation or anything like that. Therefore, it has to be built with the Mac Intel architecture. For 32bit that's i386, for 64bit it's x86_64. When you're developing and running on the simulator, your app is built with the i386 and/or x86_64 architectures as well. That is why, if you want to run your app on the 64bit simulator, your app has to be built with the x86_64 architecture, and all libraries you use also have to have that architecture or you will get linker errors. That is why your seeing "missing required architecture x86_64 in file", because it needs that architecture to run in the 64bit simulator.

If, in your XCode settings, you set "Build Active Architecture Only" to "Yes" for Debug builds, then you won't have a problem including 64bit in your architectures, as long as you don't try to run your app on the 64bit simulator, as it will only build the x86_64 architecture if you've chosen the 64bit simulator:

<img src="https://camo.githubusercontent.com/482e3e68f40706f79e5673fb2d6c27f2db6b61b4/68747470733a2f2f662e636c6f75642e6769746875622e636f6d2f6173736574732f343137353736362f313331393930332f34383932373234362d333332322d313165332d393530352d3536613035343130633030332e706e67" alt="archs" />

So, in one word: the problem with the library not including the x86_64 arch, is just that you can't test your app in the 64bit simulator. There is no problem at all shipping an arm64 build of your app.
