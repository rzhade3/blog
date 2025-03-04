---
author: 'Rahul Zhade'
date: 2025-03-04
linktitle: Writing in the Age of AI
title: 'Writing in the Age of AI: My AI-enabled Process for Writing'
weight: 10
tags: [ 'thoughts', 'writing', 'ai' ]
---

Is writing a dying art in the ChatGPT era? How I think about using AI in my writing and my AI-enabled process.

LLMs have decimated the role of a professional writer. We’ve seen layoffs among [translators](https://www.cnn.com/2024/01/09/tech/duolingo-layoffs-due-to-ai/index.html), [journalists](https://www.wypr.org/2024-05-30/wall-street-journal-layoffs-continue-despite-lucrative-ai-deal-and-record-profits), paralegals; many job fields for which writing is the core responsibility have either been completely obviated or are on their way there in due course. Even as an engineer, I’ve been able to increase my writing efficiency almost to the order of [0.5 FTE](https://www.indeed.com/hire/c/info/full-time-equivalent)— reports and other prose that I might’ve previously needed another engineer to help with, I’m able to do alone in half the time and twice as well.

Despite these shifts, I find writing to be therapeutic and useful to rubber duck ideas. I can construct better arguments and come to much better conclusions when I write out my thoughts, than when I come to a snap judgment on a Zoom call. Narratives are much easier to challenge when I can write them down and read them back.

In the broadest sense, AI kills that part of writing that makes you a better thinker, that part of writing that is therapeutic, and that part of writing that is personal.

At the same time, the efficiency gains of AI cannot be understated. I *am* definitely twice as efficient when I use Copilot to write code, likely thrice as efficient when I use Perplexity for search, and probably ten times as efficient when I use ChatGPT to generate my reports and my weekly snippets.

All these conflicting thoughts beg the question—how do I reconcile AI with my desire and need to write?

## My Flow
I’ve codified my writing flow into four steps:
1. Outlining
2. Drafting
3. Grammarly
4. Claude

### Outline
I begin everything I write with an outline. I don’t believe in “word maps”, or “mind mapping” (sorry Ms Hansen from 7th grade English!); I stick to hierarchical lists with sections. No AI involved—maybe some Perplexity for search—but for the most part just pen to paper.

The outline is where I do most of my rhetorical thinking. *Does this argument make sense? Is there enough information here?* This stage is light enough for quick iteration, but also substantial enough that it can be shared for feedback.

For example, my outline for this post looked something like this:
```markdown
Potential Titles:
* Writing in the age of AI
* 
Content:
* Intro
  * Why Use AI
  * Why I need to use AI meaningfully
* My Flow
  * Outlining
  * First Draft
  * Grammarly
  * Claude
* Concessions
  * Need to be more AI Native
  * How I plan on improving my flow
    * Figure out how to get AI inline
```

### Drafting
Drafting is where I turn my outline into stream-of-consciousness words on paper. I don’t care about grammar, correctness, or style in this stage; it’s just about fleshing things out.

I use the [Bear app](https://bear.app) to do my drafting because it is completely offline and supports Markdown. I’ve worked at GitHub for long enough that my internal monologue is basically in Markdown, so drafting this way feels very natural.

### Grammarly
Once the draft is complete, I plop it into [Grammarly](https://app.grammarly.com/) for grammar corrections. 

Why not directly into an LLM? Well, Grammarly doesn’t overly rewrite the text, especially the free version. This step is only for syntax and grammar corrections, ensuring a solid foundation before I focus on style. This approach also saves me some tokens— Grammarly is much cheaper to iterate within than an LLM.

### Claude
The final step is to run my essay through Claude. I’ve found Claude works better than ChatGPT for prose editing, but your mileage may vary. 

I do two passes with Claude:

#### Evaluation
The first pass checks for consistency and narrative flow between paragraphs. In the past, this was something I’d relied on peer review for, but now I get instant feedback from an LLM. Here’s the prompt I use for this:
```
Please evaluate the following prose for consistency and flow between paragraphs. Drop notes on any paragraphs that need better transitions.
```

#### Copy Editing
The second run is to edit and rewrite my prose for style. Here’s the prompt I use for this:
```
Please lightly edit the following prose for grammar, technical correctness, and style according to the following style guide.
```
To generate my style guide, I ran several of my past writings through Claude. Here’s mine for this blog:
```markdown
# Personal Blog Style Guide

## Tone and Voice
* Write like a human
* Write in first person using contractions (I've, don't, it's)
* Balance technical authority with conversational accessibility
* Use direct questions to engage readers
* Maintain candid authenticity without excessive formality

## Structure
* Use clear, concise headings (one to five words)
* Start with a personal anecdote or problem statement
* Use consistent heading hierarchy (H1 for title, H2 for sections, H3 for subsections)
* Organize content from foundational concepts to more advanced applications

## Formatting
* Bold key concepts when first introduced
* Format code, commands, or technical terms with backticks (algorithm)
* Use blockquotes (>) for summaries, takeaways, or emphasized recommendations
* Create bullet lists for examples, options, or multiple related items
* Use numbered lists for sequential steps
* Include specific numbers, metrics and timestamps where applicable

## Content Approach
* Structure around problem-solution frameworks with practical, actionable advice
* Define technical terms at first mention while respecting reader intelligence
* Vary paragraph length (2-5 sentences) with clear topic sentences
* Balance technical terminology with everyday language and occasional contemporary phrases
* Link to relevant external resources with descriptive anchor text
```

I separate Evaluation and Editing into two steps to ensure that the final piece still sounds like me— the goal is to be AI-enabled; not AI-powered. Most of the prose writing be written by a human, with the LLM being an editor rather than a ghostwriter. 

## Concessions
I’ll admit that, as a pre-LLM era LLM user, I’m not as LLM-native as the kids are. There are definitely ways to simplify and enhance my flow even further.

For example, using AI for pre-drafting—rubber ducking ideas before writing or outlining—could be a great usage. Chatting through a narrative to check that the thesis makes sense, and thus put together the structure of an outline, would save a lot of time.

Eventually, I will need to embrace using AI for drafting too. The efficiency gains of doing so make this almost inevitable. I’d like to integrate AI-powered inline suggestions, similar to Copilot Code suggestions. Using a well-trained style guide and examples should make the output close enough to human writing. Sure, this might take some of the soul out of writing, but let’s be honest, most of what I write (progress reports, issue write-ups, etc.) doesn’t need much of a soul at all.

Ultimately, AI is a tool just like any other. Used well, it makes you better. Used poorly, it makes you worse. Striking the right balance comes down to discipline and forethought.
