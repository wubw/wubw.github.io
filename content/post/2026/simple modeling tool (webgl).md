+++
author = "Binwei Wu"
title = "Simple Modeling Tool (WebGL)"
date = "2026-02-19"
description = "Simple Modeling Tool (WebGL)"
featured = true
tags = [
    "ai"
]
categories = [
    "Engineering",
]
series = "2026"
aliases = ["migrate-from-jekyl"]

+++

I have been very interested in 3D modeling and visualization techniques for a long time. In 2017, I started a GitHub project to build a simple modeling tool using raw WebGL directly. I decided against using frameworks like Three.js, mainly for educational purposes. The project progressed slowly. After struggling for several days, or maybe weeks, I mostly completed the basic visualization logic and was able to create some simple 3D models. Then, for the next nine years until now, there was almost no progress.

Recently, thanks to Claude Code, I felt it was a good time to revisit this personal project. After briefly reviewing the old repository and code, I found them to be of limited use, as I believed Claude Code could do a much better job. I deleted the old repo and decide to build from scratch. I described to Claude what I wanted to build, and Claude used its brainstorming skill to clarify my expectations and generated a [design document](https://github.com/wubw/MyModeling/blob/main/docs/plans/2026-02-18-webgl-sculpting-tool-design.md).

I then asked Claude to prepare the [implementation plan](https://github.com/wubw/MyModeling/blob/main/docs/plans/2026-02-18-webgl-sculpting-implementation.md). I made some minor modifications, and both documents captured my needs quite well. Claude then generated the code based on these two documents. After testing, I discovered some bugs and missing features, which Claude also addressed promptly. These additional features were documented [here](https://github.com/wubw/MyModeling/blob/main/docs/plans/2026-02-18-modeling-features.md).

I now have a simple modeling tool. It is not fancy but definitely meets my expectations and is far more advanced than what I built nine years ago. The entire process took only about two hours, see the [repo](https://github.com/wubw/MyModeling). It is fascinating to see how AI can drastically reduce the time required to build the same features. For people working in software engineering, it is crucial to think about one's 'moat' in this AI era.

*Written by Binwei@Shanghai*
