+++
author = "Binwei Wu"
title = "Brain X"
date = "2026-06-12"
description = "Brain X"
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

After my proof of concept (POC) on creating graphs from activity signals and then reasoning over the graph data, we've seen a significant improvement in LLM query quality. Soon, an internal project will kick off, which I'm calling "Brain X." In addition to building on my POC, the new project also draws inspiration from:

The wiki concept shared by Karpathy [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), which suggests that users primarily ingest raw resources, write clear guidelines for the LLM about the workflow, and let the LLM generate wiki content.

[gbrain](https://github.com/garrytan/gbrain), which introduces a sleep-based process that gathers meetings, emails, tweets, voice calls, and original ideas overnight, then constructs a knowledge graph for users to reason over.

I envision that Brain X can incorporate ideas from all these projects. We can use engagement level on topics as a proxy for importance, helping filter out noise. For the input interface, Obsidian vaults could serve as a primary source—if I consistently write notes on a topic, that clearly signals its importance. These two elements will serve as key grounding truths.

During the "sleep" process, the LLM will also identify uncertainties and generate a list of questions to ask users for feedback. This optional step could help Brain X better understand individual users.

If executed well, Brain X will be able to provide highly reliable and well-grounded data to other applications. This promises to be a truly exciting project.

*Written by Binwei@Shanghai*