+++
author = "Binwei Wu"
title = "LLM stability"
date = "2026-05-15"
description = "LLM stability"
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

I’ve used LLMs in development, daily operations, and product offerings within the company. They are powerful—able to interpret even vague requirements and still take meaningful action. However, the same prompt can produce different outputs each time. Sometimes this leads to pleasant surprises, but it can also introduce challenges.

For example, I’ve worked with note-taking operations where some models handle the task well, while others fail and produce poor results. 
In another case, I used an LLM as a judge to evaluate a project, but switching to a newer, more advanced model led to completely different scores. 
Even within the same model, I’ve seen variability when using LLMs to analyze codebases. Sometimes the quality is inconsistent, partly due to my own lack of domain knowledge, which makes it harder to provide clear guidance.

In a human-in-the-loop setup, this variability is usually manageable, since people can review the results, ensure quality, and course-correct when needed. That said, unexpected outputs can still create a less-than-ideal experience. In agentic systems, however, this becomes a much bigger concern. Model stability must be carefully managed.

When building a system, it’s important to decide which parts truly require an LLM. If a workflow is deterministic—or needs to be deterministic—it’s better to rely on code rather than an LLM. That doesn’t mean you must start with deterministic implementations. LLMs can still be valuable early on for exploration and learning. But as you gain experience and recognize the importance of avoiding LLM-related risks, it becomes easier to transition those components to code.

For workflows that genuinely benefit from LLMs, it’s essential to invest in harness engineering around them. One particularly inspiring article is https://martinfowler.com/articles/harness-engineering.html. With the right safeguards in place, you can detect and mitigate issues such as functionality drift caused by model upgrades.

*Written by Binwei@Shanghai*