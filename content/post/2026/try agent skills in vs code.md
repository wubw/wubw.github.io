+++
author = "Binwei Wu"
title = "Try Agent Skills in VS Code"
date = "2026-01-19"
description = "Try Agent Skills in VS Code"
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

Recently, a new trend has emerged in utilizing agent skills, as seen at: https://agentskills.io/home. It appears to be quite straightforward to assign specific tasks to an agent, provided that your requirements are clearly outlined in the skill.md file.

I decided to give it a try. As an avid book lover, I occasionally come across potentially interesting new books. Typically, I research the ratings on well-known sites like Douban and Amazon to gauge overall reader feedback and the total number of ratings. I then apply a personal formula to determine a final rating, helping me filter out lower-rated books that don’t meet my standards.

When my wife suggested using an agent to automate this process, I thought it was a great idea. So, I spent about half an hour with Agent Skills to set up a basic version.

First, I need to enable agent skills in VS Code
To use skills, you must be on VS Code Insiders and enable the feature:
1. Open VS Code Insiders
2. Go to Settings → Search: chat.useAgentSkills
3. Turn it ON

Using the VS Code agent, I asked GitHub Copilot to generate Python files, such as book_rating_fetcher.py, to fetch the rating data. I was amazed at how well it understood my requirements—within 10 minutes, the code was ready. Here’s what I achieved:

```
PS D:\Repo_try\skills\.skills\fetch-doubanbook\scripts> python .\book_rating_fetcher.py "黑猫警长"
Searching for: 黑猫警长
--------------------------------------------------
✓ Book: 黑猫警长.三
✓ Author: 戴铁郎
✓ Rating: 8.9/10
✓ Number of Raters: 683

  Rating Distribution:
    ★★★★★ 60.3%
    ★★★★☆ 30.7%
    ★★★☆☆ 8.6%
    ★★☆☆☆ 0.1%
    ★☆☆☆☆ 0.1%

  Book URL: https://book.douban.com/subject/2253235/
  ```

I then create a .skills folder, with a subfolder named fetch-doubanbook. The overall folder structure looks like this:
![image](images/agent_skills_folder.png)

The skill.md looks like the following:

```
# Skill: fetch-doubanbook

## Purpose
Fetch the book data from douban web site.

## When to use
Copilot should load this skill automatically whenever the user asks:
- "douban book"
- "book"

## Workflow
1. The user give the book name like douban book: "三体"
2. Run the python script in /scripts/book_rating_fetcher.py + the book name.
2. Capture the output.
4. Show the output
```

Next, in the VSCode chat window, I type something like "douban book: 鹿川有许多粪". The skill is automatically discovered, loaded, and the relevant Python script is executed. I also receive a helpful response similar to the following:

![image](images/agent_skills_response.png)

Yes, this is a very basic version of using agent skills, but it clearly shows how easy it is to configure skills and workflows using natural language. I'll continue experimenting to learn more.

*Written by Binwei@Suzhou*