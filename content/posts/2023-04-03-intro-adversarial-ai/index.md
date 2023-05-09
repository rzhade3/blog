---
author: 'Rahul Zhade'
date: 2023-04-03
linktitle: Introduction to Adversarial AI
title: Introduction to Adversarial AI
weight: 10
tags: ['machine learning', 'artificial intelligence']
---

The following is a transcription of a talk I've given internally at GitHub. The talk, and [slides](https://zhade.dev/slides/presentations/adversarial-ai), are about the basics of adversarial AI, and how it can be used to attack machine learning models.

## Slides

<a href="https://zhade.dev/slides/presentations/adversarial-ai"><iframe src="https://zhade.dev/slides/presentations/adversarial-ai" width="100%"></iframe></a>

## Transcript

### How do Machine Learning Systems work?

For the purposes of this talk/ blog post, we’re not going to go too in depth into the technical details of machine learning algorithms. However, from a security perspective, it is important to note the flow of data.

![Machine Learning System Flowchart](./images/ml-system-flowchart.png)

1. We start with some raw data. This data can be sourced from any number of places, but typically can be from the Internet, or some internal data source.
2. This data is then *cleaned* and *preprocessed* to make it usable by the model. This can include things like removing punctuation, normalizing the data, or even removing data that is not relevant to the model.
3. This data is then split into two sets, the *training* set, and the *testing* set. The training set is used to train the model, and the testing set is used to evaluate the model.
4. The model is then trained on the training set. This is where the actual machine learning happens. The model is trained to recognize patterns in the data, and make predictions based on those patterns.
5. The model is then evaluated on the testing set. This is where we can see how well the model performs on data it has never seen before. This is important because it allows us to see how well the model generalizes to new data.
6. Finally, when we are satisfied with the performance of the model on the testing set, we deploy it to production.

This is highly abbreviated, however the important thing to note here is the number of moving parts in this system. Any system with a large number of moving parts and trust boundaries is very interesting from a security perspective.

### What is Application Security?

Application Security is a discipline that ensures the security of software applications from security misuses. The main three classes that these kinds of misuses are classified into are: **Confidentiality**, **Integrity**, and **Availability**. 

**Confidentiality** attacks attack the privacy of data in the system, so for example stealing data, or leaking data.

**Integrity** attacks attack the integrity of data and the system itself, which can maliciously change the behavior of the system. For example, spoofing or man in the middle attacks.

**Availability** attacks affect the accessibility or the _availability_ of the system itself. For example, Denial of Service. 

If you’ve ever seen the OWASP Top 10, that’s a framework that maps these high level concepts down to specific vulnerabilities that manifest in applications. Unfortunately this framework doesn’t encompass vulnerabilities in Machine Learning systems, but luckily we'll be talking through those in the next few sections.

### How does Application Security apply to Machine Learning?

Well, Machine learning systems are applications like all other software. As such, they're also still vulnerable to the same CIA triad that was mentioned earlier, however in some slightly different ways.

| Similar | Different |
| ------- | --------- |
| ML Infrastructure is built on top of normal code | Machine Learning models tend to be black boxes with often non deterministic behavior |
| DoS, Supply Chain issues also still manifest | Lots of side channel information is disclosed. |

Since ML systems are built with software the same way that other applications are, they're vulnerable to all of the same classes of attacks that other applications are. These include business logic vulnerabilities in the code surrounding the model, DoS attacks, and supply chain attacks for whatever dependencies might be used to run the model.

However, where things become more interesting are in the model code itself. As this code is non deterministic, and often a black box, it can be difficult to reason about the security of the model itself. This is where adversarial AI comes in. By the nature of these systems, they tend to leak a lot of information about the model and it's internal state, which can be used to attack the model itself.

### Confidentiality Attacks

The following attacks impact the confidentiality of the system. These include the privacy of the data used to train the model, and the privacy of the model itself.

#### Model Inversion

Model Inversion attacks involve an attacker trying to reconstruct the training data used in the construction of the model. This is done by querying the model multiple times, and using the output of the model to reconstruct the training data. This is possible because the model leaks information about the training data through its output.

![Model Inversion Attack](./images/model-inversion.jpeg)

In the above image, on the right we see an image that was used to train the machine learning model. On the left, we can see an image that was constructed by an attacker who simply queried the model multiple times, and used the output of the model (a confidence score) to reconstruct the training data.

Other examples of this attack my include attackers using Copilot to mine personal access tokens from GitHub, or ChatGPT outputting personally identifiable information from users that were in its training dataset.

##### How to Fix?

Fixing this issue is very difficult, as side channel information disclosure is often a fundamental property of most modern machine learning systems. However, the best way to mitigate this attack is to reduce the amount of sensitive data that a model is trained on using a practice called [Differential Privacy](https://en.wikipedia.org/wiki/Differential_privacy). This practice involves a variety of techniques to *anonymize* individual data points while still maintaining general trends for the model to learn from.

#### Model Stealing

Model Stealing attacks involve an attacker attempting to reconstruct the model itself. This is done by querying the model multiple times, and using the output of the model to reconstruct the model itself. This is possible because the model leaks information about its internal state through its output. The simplest way to do this is to query a model multiple times to generate a training dataset, and then simply train a new model on that dataset.

This attack is extremely damaging to the model owner, as the intellectual property of the model is usually what provides most of the business value. If an attacker can steal the model, they can use it for their own purposes, or even sell it to competitors.

##### How to Fix?

Fixing this issue involves rate limiting your model very heavily. An attacker needs to query your model a lot of times in order to steal it with high fidelity, so applying appropriate rate limits can help mitigate this attack. However, this is not a perfect solution, as an attacker can simply spin up multiple instances of your model to get around the rate limit. More robust solutions to this attack are still currently being researched.

### Integrity Attacks

These attacks affect the integrity of the model itself. This includes attacks that can change the behavior of the model, or cause it to make incorrect predictions.

#### Dataset Poisoning

Dataset poisoning attacks involve an attacker attempting to manipulate the training data used to train the model. This is done by injecting malicious data into the training dataset, which can cause the model to learn incorrect patterns, and make incorrect predictions.

Unfortunately, this class of attack is extremely common with many large machine learning models these days, as the largest source of information to train from (the Internet) is a hostile and very adversarial place. This is especially true for models that are trained on user generated content, such as ChatGPT, or Copilot.

##### How to Fix?

Fixing this issue involves ensuring that the training data used to train the model is high quality, and is not maliciously manipulated. This can be done by ensuring that the data is curated by a trusted source, and that the data is not tampered with in transit.

#### Evasion Attacks

Evasion attacks involve an attacker modifying inputs to a model in slight ways to cause the model to make incorrect classifications. This is distinct from dataset poisoning, as the attacker is not modifying the training data, but rather the input to the model itself (this problem manifests at training time rather than data sourcing time).

![Evasion attack](./images/evasion-attack.png)

In the above image, we start with an image of a panda bear, which our model has correctly identified with 57.7% confidence. However, after we add some adversarial noise to it using the middle image (an **"adversarial perturbation"**), our model now thinks that the input image is a gibbon with 99.3% confidence.

This problem manifests due to the fact that machine learning models have a tendency to *memorize* rather than *learn*, and can tend to overfit the training data. This means that the model is not able to generalize to new inputs, meaning that slight changes in unexpected ways may cause the model to make completely incorrect classifications.

##### How to Fix?

In order to fix this issue, we need to ensure that our model is not overfitting the training data. This can be achieved by perturbing the training data to the model. For example, when we clean our data, we should also add some amount of noise to it, such as rotating the image slightly, or adding some random noise to the image. This will help the model learn to generalize to new inputs, rather than memorizing the training data.

#### Prompt Injection

Prompt injection is a special class of vulnerability that manifests only in large language models (LLMs). This class of attack involves an attacker injecting a malicious input to an LLM that causes the model to output malicious or otherwise unexpected content.

This attack occurs because large language models are not able to distinguish between a *prompt* (crafted by an engineer or an otherwise trusted source) and an *input* (crafted by a user). This means that an attacker can craft an input that causes the model to output malicious content.

![Prompt Injection](./images/prompt-injection.jpeg)

As an example of this attack, in the above picture we can see a user crafting an input to a LLM which bypasses the restrictions set by the prompt (to only respond about remote jobs) to instead output a malicious response (a threat against a public figure).

##### How to Fix?

Unfortunately, as of the time of writing, there are no known ways to prevent prompt injection attacks. As such, it is important to ensure that your usage of LLMs appropriately considers this attack vector as a credible threat


### Takeaways

So what can we do to protect against these kinds of attacks? This is currently an active area of research, but there are a few general principles that should always be followed when building secure machine learning systems.

1. Monitor your models in production. 
	- This is important for a number of reasons, but it allows you to detect when your model is being attacked, and allows you to take action to mitigate the attack.
	- You should be monitoring (i) who's querying your model; (ii) how often your model is being queried; (iii) what data is being sent to your model; (iv) what predictions your model is making.
2. Rate limit your models. 
	- This is important to prevent DoS attacks, and also helps mitigate model stealing attacks.
3. Train your model with robust, curated data.
	- Be very cognizant of the data that you're using to train your model. Ensure that it is (a) high quality, and contains vetted information from non malicious users, and (b) that you have applied differential privacy to your dataset to ensure that personal information is not used to train your models.
	- This is an incredibly difficult step to get right, as modern models are typically trained with terabytes of data sourced from the Internet (a very adversarial environment). However, we can apply best effort data cleaning (regexes to remove PII, etc.) on this data to ensure that it is at least as private as possible.
4. Implement other supplementary security controls
	- Ensure that your models are only accessible by authorized users, and that dependencies are up to date.
	- Remember, ML Systems are just normal code, so secure it as you would anything else!

### Further Reading

For more information, check out my [Adversarial AI Reading List repository](https://github.com/rzhade3/adversarial-ai-reading-list) on GitHub. It contains a list of papers, blog posts, and other resources that I've found useful when learning about adversarial AI.
