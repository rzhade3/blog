---
author: 'Rahul Zhade'
date: 2023-09-21
linktitle: Introduction to Natural Language Processing
title: Introduction to Natural Language Processing
weight: 10
tags: ['natural language processing']
---

A quick intro to the basic terminology and concepts in Natural Language Processing.

### Token

A **token** is the smallest semantic subsection of a piece of text. For example, a word. 

This is the smallest subdivision of a text that can be assigned meaning (any smaller units than a word, like individual letters, have no inherent meaning to them)

### n- gram

An **n- gram** is a collection of `n` tokens. For example, if we’re using words as the gram and using the value 2 as `n`, in the sentence:

> The quick brown fox

The following 2-grams can be extracted:

```
[the, quick],
[quick, brown],
[brown, fox]
```

The significance of an **n- gram** is that it can help model language much better than individual tokens. While tokens have meanings on their own, typically they have modifiers around them that will *add* meaning to the phrase. For example, while *fox* has inherent value, the 2- gram *brown fox* adds more information about the fox. Successively enlarging the context (length of *n*) can improve how well we’ve understood the source text, however, comes at the cost of increasing the number of n-grams that need to be processed.

### Embeddings

**Embeddings** are a way to represent a word as a vector, to make it machine understandable.

These embedding vectors usually have a large number of dimensions. This allows words to be clustered together in multidimensional space *in order of similarity*. 

I.e. words that mean similar things, like *like* and *enjoy* will sit close to one another in the embedding space.

An interesting property of these embeddings is that we can also do *vector math* with words. For example, given a modern embedding, one could perform this equation:

```
"king" - "man" + "woman"
```

To get the output “queen”.

The significance of an embedding is that it allows us to encapsulate the *meaning* of words in multidimensional space, which means that we can perform similarity searches to other words, sentences, and texts with much higher performance than traditional techniques, like bag of words. 
