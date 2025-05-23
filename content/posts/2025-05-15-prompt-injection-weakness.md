---
author: 'Rahul Zhade'
date: 2025-05-15
linktitle: Prompt Injection is a Weakness, not a Vulnerability
title: 'Prompt Injection is a Weakness, not a Vulnerability'
weight: 10
tags: [ 'ai', 'security', 'llm', 'prompt injection' ]
---

Prompt injection is the most widely discussed emergent threat for Large Language Models (LLMs). But how should organizations approach tracking and prioritizing it?

In my opinion, instead of being a vulnerability in and of itself, prompt injection is a weakness that leads to other, more potent vulnerabilities. While it is true that prompt injection is undesirable, it doesn’t necessarily lead to any business value compromises and is also (at the time of writing) unsolvable. As such, it is better to treat it as a necessary side effect of using LLMs, architect around it, and not track it as a distinct vulnerability.

## What Is Prompt Injection?
Prompt injection occurs when someone manipulates input to a language model to alter its behavior in unintended ways—whether by injecting malicious instructions or bypassing established controls. The challenge lies in defining what "unintended" actually means, as developers and end users often have different intentions for how an LLM should function.

## Why Is(n’t) Prompt Injection an issue?
Prompt injection can lead to an LLM producing “poisoned” content. This can have a few effects: leaking or bypassing of a system prompt, a malicious directive for a tool call, leaking of authorized content, inducing an MCP client to issue incorrect directives, etc.

However, all of these are issues for different reasons:
* Leaking or bypassing a system prompt is unsafe when a system prompt contains sensitive content or is the only guardrail that prevents malicious behavior. This is, unfortunately, an issue due to **prompt injection itself**.
* Poisoned outputs are unsafe when they’re unsafely interpolated into something else. For example, injecting the output of an LLM directly into a shell command can lead to command injection, or into a UI can lead to XSS. This is an issue due to **insecure output handling**.
* Leaking authorized content is unsafe when an LLM has access to content that a user otherwise does not have access to. This is an issue due to **improper authorization**.
* Inducing an MCP client to select an incorrect directive is unsafe due to it making tool calls with inadequate [human-in-the-loop controls](https://en.wikipedia.org/wiki/Human-in-the-loop). This is a complex problem, but one that ultimately occurs due to the **confused deputy problem**. 

Setting aside the first vulnerability (where prompt injection directly exposes or bypasses sensitive system prompts) above, let’s discuss why the other issues are not intrinsic to LLMs themselves.

An LLM, in a system diagram, *has no dependencies* and is *incapable of any external behavior*. It is just a black box that accepts some text as input, and spits out some text as output. As such, the only vulnerability that an LLM *natively* is capable of is leaking any context that is passed to it. Any capabilities that are bolted on to an LLM (MCP, Agents, etc.) are *independent* of the LLM itself. Any vulnerabilities in these external components should be considered vulnerabilities in the components themselves, not in the LLM.

It helps to threat model an LLM as an untrusted user. Accepting arbitrary user input is not necessarily a vulnerability— how you *treat* the user input is what causes risk. If you wouldn’t trust an arbitrary user to run shell commands on your server, or input HTML onto your page, or issue API commands on your behalf, why would you treat an AI any differently? You should apply output sanitization, add human-in-the-loop controls and monitoring, add authorization controls, and firewall appropriately. 

This does bring us back to the first point though: leaking of sensitive context or bypassing restrictions set by the system prompt. While prompt injection can exploit such weaknesses, relying on system prompts for security is fundamentally fragile—primarily because of natural language's intrinsic ambiguity, which makes robust intent enforcement nearly impossible.

## The Challenge in Robustly Addressing Prompt Injections: Natural Language Ambiguity
The challenge in determining intention is that there is *inherent ambiguity in natural language*. For example, take a look at this sentence:

> He saw her duck.

Is "duck" a noun or a verb? The meaning is only clear when you add additional context. This problem compounds when you have larger prompts, and especially when you have multiple users defining context, such as the developer of an AI app, a user, and potentially even MCP integrations.

For example, let’s say that an AI Agent is prompt injected into installing a malicious dependency. Can you definitively prove that the end user didn’t want this action? The lack of a domain-specific language for LLM interactions (comparable to programming languages) makes intent verification fundamentally intractable.

Existing mechanisms for mitigating prompt hardening (such as input and output filtering, prompt hardening, intent classification, and retrieval augmentation) try to increase context and thus alignment to a particular domain. However, all of these are nondeterministic and, more importantly, do not solve the fundamental issue of ambiguity in prompting.

As such, in my opinion, it is infeasible, in the near future, to solve the AI alignment problem, and thus prompt injection at large, with better system prompts. Given this, we must reconsider how we approach managing prompt injection—not as a solvable vulnerability but as a persistent weakness that needs to be architected around.

## Managing Prompt Injection
From a theoretical perspective, the only inherent risk posed by prompt injection is the bypassing or leaking of the system prompt. All other risks and vulnerabilities are due to downstream behaviors that are *enabled* by prompt injection. 

> Prompt injection is the *weakness* that leads to data exfiltration, spoofed MCP tool calls, and leaking of authorized content, not the vulnerability itself.

From a practical standpoint, treating prompt injection itself as a vulnerability creates problems. Tracking vulnerabilities in a security program should ideally imply that adequate mitigations exist. If not, these vulnerabilities must ultimately be continually risk-accepted. This will lead to:
* Executive fatigue from continual security alerts,
* Engineering resources wasted on unsolvable problems,
* Diminished attention to addressable security concerns

Prompt injection alone doesn't necessarily compromise business value if systems are designed properly. Prompt injection is best addressed with proper product design:

* Unauthorized Command Injection and XSS can be resolved with output sanitization.
* Data exfiltration can be resolved with firewalling and human-in-the-loop controls.
* The Confused Deputy Problem can be mitigated with [the Agent Security Framework](https://medium.com/data-science-collective/mcp-is-a-security-nightmare-heres-how-the-agent-security-framework-fixes-it-fd419fdfaf4e) and human-in-the-loop controls.

If product designs are insecure, the business risk results from any of the above downstream vulnerabilities. Furthermore, it’s much easier for an executive or engineer to understand the business value risk from a data exfiltration issue than from the more abstract prompt injection. There is a direct value compromise caused by the data exfiltration, whereas a prompt injection may or may not lead to any financial or reputational impact.

With these points in mind, here are some practical recommendations for addressing prompt injection in your organization:

## Recommendations

For those responsible for AI security:
1. **Track downstream impacts separately** - Focus on the specific vulnerabilities that prompt injection enables rather than treating prompt injection itself as the primary issue.
2. **Improve underlying models** - Continue advancing the state-of-the-art in prompt injection mitigation, but with realistic expectations about what's possible.
3. **Rethink confidentiality assumptions** - Don't consider system prompts confidential, as prompt injection cannot be robustly mitigated anyway. Architect your systems assuming that any context provided to the LLM will be revealed to an end user.

By shifting focus to the concrete business risks that stem from insecure LLM design patterns rather than the abstract concept of prompt injection itself, security teams can make meaningful progress in protecting AI systems while using resources efficiently.

_**Edits:** Edits were made to the original for additional clarity, see [the commit history](https://github.com/rzhade3/blog/commits/main/) for the original version._
