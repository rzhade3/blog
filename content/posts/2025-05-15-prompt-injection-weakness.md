---
author: 'Rahul Zhade'
date: 2025-05-15
linktitle: Prompt Injection is a Weakness, not a Vulnerability
title: 'Prompt Injection is a Weakness, not a Vulnerability'
weight: 10
tags: [ 'ai', 'security', 'llm', 'prompt injection' ]
---

Prompt Injection is the most widely discussed emergent threat in AI Systems. But how should organizations approach tracking and prioritizing it?

In my opinion, instead of being a vulnerability in and of itself, prompt injection is a weakness that leads to other, more potent vulnerabilities. While it is true that prompt injection is undesirable, it is (at the current time) unsolvable, and as such tracking it as its own vulnerability leads to more overhead and less desirable security outcomes. 

## What is Prompt Injection?
Prompt injection occurs when someone manipulates input to a language model to alter its behavior in unintended ways—whether by injecting malicious instructions or bypassing established controls. The challenge lies in defining what "unintended" actually means, as developers and end users often have different intentions for how an LLM should function.

## The Challenge: Natural Language Ambiguity
The challenge in determining intention is that there is *inherent ambiguity in natural language*. For example, take a look at this sentence:

> He saw her duck

Is duck a noun or a verb? The meaning is only clear when you add additional context. This problem compounds when you have larger prompts, and especially when you have multiple users defining context, such as the developer of an AI app, and a user, and potentially even MCP integrations.

For example, let’s say that an AI Agent is prompt injected into installing a malicious dependency. Can you definitively prove that the end user didn’t want this action? The lack of a domain-specific language for LLM interactions (comparable to programming languages) makes intent verification fundamentally intractable.

Existing mechanisms for mitigating prompt hardening, such as input and output filtering, prompt hardening, intent classification, and retrieval augmentation try to increase context or adherence to a particular domain. However, all of these are non deterministic and, more importantly do not solve the fundamental issue of ambiguity in prompting.

## Weakness vs Vulnerability
There’s two types of security issues, weaknesses and vulnerabilities.
* A **Security Weakness** represents a condition or property that isn't directly exploitable but could potentially lead to security issues.
* A **Security Vulnerability** is a specific flaw that **can be exploited by threat actors** to gain unauthorized access, disrupt services, or cause harm.

Prompt injection, by itself, doesn’t necessarily lead to any business value compromises. The only inherent risk is that of System prompt leakage.

There are, however, several other more tangible dangers that prompt injection can lead to due to insecure design.

## Downstream Vulnerabilities
The most significant business risks come not from prompt injection itself, but from what it enables:

### Unauthorized Command Injection
If the LLM is integrated with tools (APIs, code interpreters, or function calling), prompt injection can trick the model into issuing commands outside intended use.

This can be partially mitigated by output filtering, tool validation, and human-in-the-loop controls.

### Data Exfiltration
When agents fall victim to prompt injection, they may leak sensitive context; such as through slopsquatted dependencies or poisoned web searches. 

This vulnerability can be partially mitigated using firewalls and human-in-the-loop controls.

### Confused Deputy Problem
Model Context Protocol (MCP) configurations face particular vulnerability to the [Confused Deputy Problem](https://embracethered.com/blog/posts/2025/model-context-protocol-security-risks-and-exploits/), where malicious MCP servers can inject prompts that trigger incorrect tool calls, potentially leaking data. 

While currently still an active research topic, proposals like the ~[Agent Security Framework](https://medium.com/data-science-collective/mcp-is-a-security-nightmare-heres-how-the-agent-security-framework-fixes-it-fd419fdfaf4e)~ offer potential paths forward to address this issue.

## Practical Security Management
From a practical standpoint, treating prompt injection itself as a vulnerability creates problems. Tracking vulnerabilities in a security program should imply that adequate mitigations exist. If not, these vulnerabilities must ultimately be continually risk-accepted. This will lead to
* Executive fatigue from continual security alerts
* Engineering resources wasted on unsolvable problems
* Diminished attention to addressable security concerns

Most importantly, prompt injection alone doesn't necessarily compromise business value if systems are designed properly. Prompt injection is best addressed with proper product design; if product designs are insecure (lack of human in the loop controls, etc.) the business risk is resultant from any of the above downstream vulnerabilities. It’s much easier for an executive or engineer to understand the business value risk from a data exfiltration issue than that of a more abstract prompt injection. 

## Recommendations

For those responsible for AI security:
1. **Track downstream impacts separately** - Focus on the specific vulnerabilities that prompt injection enables rather than treating prompt injection itself as the primary issue.
2. **Improve underlying models** - Continue advancing the state-of-the-art in prompt injection mitigation, but with realistic expectations about what's possible.
3. **Rethink confidentiality assumptions** - Don't consider system prompts confidential, as prompt injection cannot be robustly mitigated anyway.

By shifting focus to the concrete business risks that stem from insecure LLM design patterns rather than the abstract concept of prompt injection itself, security teams can make meaningful progress in protecting AI systems while using resources efficiently.
