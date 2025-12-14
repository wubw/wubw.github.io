+++
author = "Binwei Wu"
title = "Restart Personal Blog by Using Hugo"
date = "2025-12-14"
description = "Restart Personal Blog by Using Hugo"
featured = true
tags = [
    "blog", "opensource"
]
categories = [
    "ComputerScience",
]
series = "2025"
aliases = ["migrate-from-jekyl"]

+++

Previously, I used Ablog to set up my personal blog and maintained the content using Sphinx syntax. That effort ended around 2021—not something I’m proud of. Recently, I’ve decided to restart blogging to capture my learnings, insights, and reflections. However, I’m hesitant to continue with the old system because Ablog is no longer actively maintained, and Sphinx is less popular compared to Markdown. I don’t want to invest time in learning two syntax systems.

My goal is simple: rebuild my personal blog using Markdown for content maintenance. The platform should support features like tagging and search. I tested MkDocs and Hugo and found MkDocs lacks some functionality I need, such as tagging, while Hugo fits my requirements well.

Migrating Sphinx content to Markdown is now straightforward. I delegated the tedious work to Copilot. It took only a few minutes, and the migration quality was excellent. Most content transferred correctly. The only issue was table formatting, which didn’t convert properly and required manual fixes.

# What is Hugo?

[Hugo](https://gohugo.io/) is a static site generator that builds content from Markdown files. To get started, follow the [Get Started](https://gohugo.io/getting-started/quick-start/)  to install the Hugo CLI. After the initial setup, you can run the site locally with:

```bash
hugo serve
```

It’s a convenient experience—you can add, modify, or delete Markdown files without restarting the Hugo process, and the local site reflects the changes instantly. Occasionally, some updates require a restart, but overall, the experience is excellent.

## Theme

Choosing a theme that meets your needs is essential. Hugo offers plenty of options, and I selected [Hugo Clarity](https://themes.gohugo.io/themes/hugo-clarity/) because it provides features like categories, recent posts, tagging, and search.

After adding Hugo Clarity via `git submodule`, you’ll find examples in the `exampleSite` folder. These are helpful for getting started. Follow the examples, adjust Markdown files to understand how the theme works, then copy and modify existing files as templates for your own content. It’s much easier to build on top of working examples.

## Deploy

There are several ways to host a Hugo site. I chose [Github Pages](https://gohugo.io/host-and-deploy/host-on-github-pages/). Initially, I tried hosting the built *public* folder, but the UI looked messy and didn’t work properly. After following the instructions to configure the GitHub repository and Hugo settings, I still had to manually trigger the build process from the Actions page to make the blogs work. I believe there’s a way to automate this, which will be my next area of investigation.

# Summary

Overall, I’m satisfied with Hugo because of its rich functionality. With LLMs, many tedious tasks, such as syntax migration, content polish, have become much more efficient. There’s still room to optimize the blogging system, but this has been a great starting point to resume my blogging practice.

*Written by Binwei@Shanghai*
