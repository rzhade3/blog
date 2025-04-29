---
author: 'Rahul Zhade'
date: 2025-04-29
linktitle: Secure Generative UIs with Grammar Constrained Decoding
title: 'Secure Generative UIs with Grammar Constrained Decoding'
weight: 10
tags: [ 'ai', 'security', 'llm', 'ui' ]
---

LLMs are very powerful for generating arbitrary text, but they really shine when you ask them to generate structured output. One such use case is generating rich UIs and text.

It’s much easier to read the output of an LLM if it’s properly styled and interactive. Reading large blobs of text can get unwieldy, and adding styling and interactive elements dynamically makes the output much more user-friendly and personalizable. This is known as a [Generative UI](https://www.nngroup.com/articles/generative-ui); a User Interface that is generated in real-time by a generative AI.

Naively generating rich UIs with an LLM, however, can be fraught with danger. While you could have an LLM one-shot a Single Page App and directly render this to a user, this could:

* Produce invalid output by way of invalid HTML; or worse
* Malicious content that could lead to XSS or UI redressing for end users. 

Generating small sections of a page can be done securely enough with just Markdown. But what if you want to generate an *entire* web page with AI? 

What is the proper way to generate rich text and UIs with an LLM? How can we guarantee correctness and safety? How can we generate content for the lowest cost and the highest amount of extensibility?

## Rich Text and UIs
To build a full web UI, we need to format the content using a rich text formatting schema. Rich Text is any text with formatting attached, like bold, italics, potentially hyperlinks, images, etc. There are several ways to define rich text:

#### Markdown
Probably the easiest way to write rich text manually, Markdown is a human-readable format that contains certain directives, such as links, images, and lists. It has a very small subset of [control directives](https://daringfireball.net/projects/markdown/syntax), making it extremely compact to write (and generate).

Markdown excels at defining prose, as it has control directives for almost every kind of text formatting. It is less useful for defining other interactive elements that a web page may need, such as arbitrary styling, JavaScript, or page metadata. It is *technically* extensible to do these things, as you can embed arbitrary HTML into it, but this may be inadvisable to do in an LLM due to reasons we’ll discuss soon. If you do not need to support constructs outside those in vanilla Markdown (such as arbitrary styling, page metadata, or more), Markdown is the best data interchange format for rich text. If you can just use Markdown, you need not read the rest of this blog, as the core spec is (mostly) safe[^markdown safety] to turn into HTML. 

#### HTML/ XML
HTML/XML is the way that the web renders content. As such, it is the *most extensible* way to render rich UIs; if you can think of it existing on the web, you can define it with HTML and XML.

By definition, HTML is extremely extensible. You could have an LLM generate an inline script for you to execute inside a page generated on behalf of a user; you could have the LLM generate arbitrary styling for a page based on a user’s preferences. These may otherwise be inadvisable due to the nature of XSS and UI redressing but could be a reasonable use case for some sort of temporary SPA or non-production web app.

Unfortunately, HTML and XML can be very verbose due to the additional characters involved in opening and closing tags. As such, it can be expensive to generate with an LLM (compared to Markdown, JSON or YAML).

#### JSON and YAML
JSON and YAML aren’t *typically* used for defining rich text. However, as JSON and YAML can be translated directly to XML (and thus HTML), they could be used as the rich text formatting layer.

The advantage of using JSON and YAML is that they’re [*significantly* more compact](https://medium.com/better-programming/yaml-vs-json-which-is-more-efficient-for-language-models-5bc11dd0f6df) than HTML and XML. JSON is roughly *twice* as efficient as HTML/XML for describing an equivalent data structure, and YAML is roughly *twice* as efficient as JSON (JSON could be more efficient for deeply nested content, which an HTML document will be). So, all else being equal, if you are looking for maximal cost efficiency, you may consider using YAML or JSON as the formatting language.

It may be slightly harder to prompt the LLM to generate something to be structured in HTML using JSON or YAML, so it’s more often better to use JSON to generate small sections of pages as opposed to the entire UI at once. 


To better compare these options at a glance, here are the tradeoffs of each rich text format:

| Spec      | Compactness | Extensibility | Strictness |
|-----------|-------------|---------------|------------|
| Markdown  | Very High   | Low           | None       |
| HTML/ XML | Low         | Very High     | Very High  |
| JSON      | High        | Very High     | Very High  |
| YAML      | Very High   | Very High     | High       |

## Structured Outputs in LLMs
Now that we've explored the various rich text formats, let's look at how LLMs can be leveraged to generate these structured outputs effectively.

LLMs are well aware of various formats natively, such as JSON, YAML, Markdown, XML and HTML, so you could instruct the LLM to directly generate structured content. However, relying on instruction alone is not the best way of generating structured content, as the output is not *guaranteed* to be properly formatted. An unconstrained LLM can be prone to hallucination, or just otherwise produce invalid content.

Enter [Grammar Constrained Decoding](https://arxiv.org/abs/2305.13971). Grammar Constrained Decoding is a technique to force LLMs to produce structured content that adheres to a particular formal grammar. The way it works is by *constraining* the vocabulary that an LLM can use after any given turn to a particular subset; if you know only a few tokens will be valid to use in an output, you can force the LLM to choose the next token only using that subset of its vocabulary.

Grammar Constrained Decoding is an excellent way to produce structured outputs like JSON, YAML and HTML/XML, as these can be described with a formal grammar. You can force most LLM to [respect a formal grammar](https://docs.fireworks.ai/structured-responses/structured-output-grammar-based).

While Grammar Constrained Decoding works excellently for formats with formal grammars like JSON, YAML, and HTML/XML, Markdown is unfortunately [too permissive to be constrained by a formal grammar](https://roopc.net/posts/2014/markdown-cfg/). This does mean that there’s no such thing as invalid Markdown, though, so you *can* generate Markdown natively with an LLM with no issue.

## Safe UI Generation
Now that we know how to generate structured outputs with an LLM, how do we safely generate them for UIs? 

The threat model for generative UIs is that the LLM may try to generate unsafe HTML due to hallucination or prompt injection. As such, we need to prevent XSS and UI redressing.

We can do this in one of two ways:

1. Generate unconstrained content, then run it through a sanitization filter
2. Generate a constrained subset of the content (i.e. only allowing certain HTML/XML tags and attributes to be generated)

### Generate Unconstrained Content then Sanitize
The easiest way to generate a safe rich UI is by generating arbitrary content (structured or otherwise), then running it through a sanitization filter (like [DomPurify](https://github.com/cure53/DOMPurify)), and then displaying it on a page. This will *guarantee* safety, as DomPurify will ensure the output is safe before it gets injected onto a page, with full customizability. 

The main issue with this method is that it may *cost you more money*. Since you are generating tokens and then throwing them away, this is more money than if you were to simply not generate them in the first place. This brings us to the next method:

### Generate Content Using a Subset of Vocabulary (then maybe sanitize)
Since we can define grammars to define HTML, XML, JSON, and YAML, we can *constrain* these grammars to only support certain tags or attributes. Instead of supporting the *entire* HTML specification with our formal grammar, we can only tell the LLM about a certain subset of valid characters.

This means that we’d not necessarily be throwing any tokens away; if we have constrained the grammar properly, we get an automatically “safe” UI directly as the output. It would be recommended to do another round of sanitization as a sanity check (the cost of sanitizing HTML is negligible compared to generating content with an LLM), but this is not technically needed at all.

You can take a look at my [DoLLMPurify](https://zhade.dev/dollmpurify/) project which makes it easy to generate constrained grammars for HTML based on an equivalent DomPurify configuration for use in your project.

This technique is feasible for generating content with HTML, XML, JSON, and YAML. While you *could* do something similar for Markdown, if you intend on supporting any “extensions” (HTML tags embedded in Markdown), the grammar becomes unwieldy to the point that it’s not worth doing.

## Conclusion

In conclusion, generating extensible UIs and rich text with an LLM can be perilous due to both error-prone output generation, as well as potential security vulnerabilities from XSS or UI redressing vulnerabilities. These dangers can be mitigated through safe architecture choices, such as using Grammar Constrained Decoding to guarantee the validity, and potentially the safety, of the output. This, combined with other methods such as HTML sanitization, ensures you can build powerful, extensible, and rich UI experiences using entirely generative content. 


[^markdown safety]: Stay tuned for a future blog post
