+++
author = "Binwei Wu"
title = "Reading by AI"
date = "2026-04-17"
description = "Reading by AI"
featured = true
tags = [
    "ai"
]
categories = [
    "PKM",
]
series = "2026"
aliases = ["migrate-from-jekyl"]

+++

This blog documents my recent practice of using AI to enhance my reading process and the benefits I’ve gained from it.

I maintained my book list in a spreadsheet, which helped me track what I’ve read, what I’m currently reading, and what I plan to read. While this works well, I wanted to improve a few things:
- Group books with similar content
- Have a better tool for writing reading notes, spreadsheets aren’t ideal for that
- Preview a book’s content before committing time to read it fully
- Enhance my reading experience, such as digesting key information more effectively

AI helped me address all of the above. Here’s how I did it:
1. First, I converted my book list into individual markdown files, one per book.
2. I used Claude to review each markdown file and generate a book summary, giving me a first impression of the content.
3. I asked Claude to group books by relevant topics, such as investing or AI. This was possible because each book now had a summary, allowing AI to cluster them automatically—saving me significant manual effort.
4. For each topic, I created a separate markdown file that links to the relevant book files using Obsidian’s linking format, making it easy to navigate relationships.
5. I then created a project in NotebookLM for each topic and uploaded the related digital books.

For each book, I ask the following set of questions:
1. Full Overview Prompt​: 
Can you help me get familiar with everything I need to know? I’m looking for a full overview. Tell me about key characters, plot points, themes, chapter summaries, and any important quotes or turning points.
2. Chapter-by-Chapter Summary: ​
Provide a detailed chapter-by-chapter summary. For each chapter, include key plot points, character development, and any themes or symbols introduced. Note any foreshadowing or callbacks to earlier chapters.
3. Core Arguments Extraction​: 
What are the 5 most important claims in this book? For each claim, quote the exact sentence and cite its location.
4. Essential Questions​: 
Based on all inputs, generate 5 essential questions that, when answered, capture the main points and core meaning of the book.
5. Surprising Insights​: 
What are the most surprising or counterintuitive pieces of information in the book? Focus on findings that contradict common assumptions, and quote the relevant passages.

Based on the answers, I explore further questions to deepen my understanding.
This process gives me a solid grasp of the book, helping me decide whether to stop there or invest more time in a thorough read - saving me considerable time in the long run.

I can also treat an entire topic project as a whole and ask broader questions, such as:
- What are 5 core mental models used by experts in this field?
- Where do experts fundamentally disagree? What are the strongest arguments on each side?
- Generate 10 questions that distinguish deep understanding from rote memorization.


*Written by Binwei@Shanghai*