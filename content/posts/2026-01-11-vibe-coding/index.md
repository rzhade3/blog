---
author: 'Rahul Zhade'
date: 2026-01-11
linktitle: Lessons from a Month of Vibe Coding with Copilot CLI
title: Lessons from a Month of Vibe Coding with Copilot CLI
weight: 10
tags: ['vibe coding', 'copilot cli', 'ai']
---

One of the greatest perks to working at GitHub is unlimited access to Copilot tokens, allowing me to vibe code as much as I want:

![Unlimited Copilot tokens](./images/unlimited-copilot-tokens.png)

With my unlimited AI, I spent most of my holiday break vibe coding up small utilities and games that I’d wanted to work on before but never had the time to finish.

Here are a few lessons that a holiday season with irresponsible access to premium models taught me about vibe coding with [Copilot CLI](https://github.com/features/copilot/cli):

### 1. Stage often, commit often

Version control is twice as important for vibe coding as normal collaboration, as the agent can iterate much faster than a human. 

Catastrophic failure is an unfortunate reality of vibe coding. To prevent an agent’s mistake from destroying all your progress, I try to stage changes as soon as they’re satisfactory. This helps me revert to a previously working version easily.

I don’t let Copilot stage or commit any changes; the only Git command I allow it to run is `git diff`. Allowing staging could allow the agent to clutter up your version control, breaking it.

### 2. Planning and docs save a million tokens

Docs-driven Development is the best way to have a successful vibe coding session. By the time I begin vibing out code, I have a fully fleshed out `docs/` folder.

I start each vibe coding session by chatting through the architecture with Copilot. I find this useful to validate deployment strategies, cloud primitives to use, and general code architecture. This step is also useful because it lets Copilot understand all and properly document these decisions in a format that is clear for subsequent agents to follow. 

Giving the agent information about the deployment strategy also helps it write code that is idiomatic to the platform in which you decide to deploy. For example, agents tend to write serverful code. Giving the agent context that your project will be deployed to Azure Functions helps it write code idiomatically and correctly the first time.

### 3. Build atomically with clear contracts

Vibe coding works best with microservice or [package-oriented](https://www.ardanlabs.com/blog/2017/02/package-oriented-design.html) architectures, as they’re easier to understand and distribute work across.

When a codebase isn’t properly decomposed, agents very quickly lose alignment. As such, avoiding a monolith is crucial for fast and successful development. 

In the planning stage, I ask the agent to break down the architecture into packages and folders. Then, I ask it to `mkdir` each of these folders, and document the API contract in a README in each folder. This gives each subsequent agent a clear context for how code should be written and integrated.

### 4. Don’t let conversation history get too long

The behavior after Copilot CLI’s conversation history gets truncated is *very* difficult to predict. It is difficult to pinpoint exactly what parts of the conversation history are truncated, so it’s best to prepare for when you run out of context. 

When you arrive at a logical stopping point, have Copilot write down instructions for future agents in a README and end the session. This way, you don’t have to deal with a session getting overly long and losing context, and you can control exactly what context an agent has access to.

### 5. Determinism wins over non-determinism

AI is unreliable, so it’s much better to ask it to do things deterministically, rather than rely entirely on the agent’s AI. For example, [Tally’s](https://github.com/davidfowl/tally) workflow relies on regexes that are generated deterministically but are validated by an agent. This kind of workflow works much better than relying entirely on the agent’s AI brain doing all computations. 

I think about whether something can be done deterministically before delegating it to an agent. I rely heavily on JSON schemas, linters, and any other ways in which I can deterministically validate correctness. 

### 6. Use custom instructions 

Copilot’s default instruction set is pretty good for vibe coding, but custom instructions help fine-tune it for my specific workflow. For example, I find that Copilot overly documents every change that it makes. I use a [custom instructions file](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli) that I paste into all new repositories that I create to tell Copilot *not* to document every feature change with a separate Markdown file.

## Conclusion
Clear communication and atomicity are always the best ways of getting big projects done. As such, most of these lessons should not be a big surprise to anyone who is used to doing engineering with a large team. Vibe coding just makes these more important due to the speed of development and the number of agents involved in a session.

*Note: all opinions are my own and do not reflect those of my employer.*
