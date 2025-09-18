---
layout: post
comments: true
title:  "LLMs Set the Floor—Not the Ceiling—in Developer Tooling"
date:   2025-09-18 15:30:00
categories: research
---

Large Language Models (LLMs) are often framed as potential replacements for traditional software development tools supporting tasks such as bug fixing, refactoring, and code documentation. I don't think we are quite there yet and it is not clear whether we will ever be. At the moment, I believe a more accurate way to see them is as baselines for these tools rather than direct substitutes. An LLM can produce results that are “good enough” in areas like linting, test generation, bug fixing, and refactoring, but these results are rarely specialized or rigorous enough to replace domain-specific tooling altogether. Instead, they establish a minimum viable capability against which traditional or new tools can be compared.


## Why LLMs act as baselines

Large language models (LLMs) are **general** in the sense that they can identify and fix some bugs and antipatterns, generate test cases, perform refactorings, document code, and more without being explicitly programmed for those tasks. Multiple research papers suggest that they are not deeply optimized for any of these tasks, specially if they go beyond programming competition examples, even when fine-tuned with coding-related tasks in mind. However, what they *can* do is still useful. LLMs are also **accessible**, since they offer out-of-the-box functionality without needing extensive integration or setup. For instance, a developer can ask an LLM to *“write unit tests for this function”* and get something useful immediately. On top of this, LLMs are **flexible**. They can reason across domains—natural language, code, design, documentation—making them suitable for situations where specialized tools would normally fall short. This creates an interesting dynamic: if an LLM can already do “most of the job” of a software development tool, then the tool must justify its existence by being better in one or many ways: accuracy, scalability, workflow integration, etc.

Specialized tools offer potential benefits that can justify their use. First they can be more precise, as they enforce strict rules or rely on well-understood, well-accepted heuristics or even formal models. Moreover, they can provide guarantees if their assumptions are met, e.g., in the absence of concurrency and dynamic dispatch, an Extract Method refactoring can guarantee behavior preservation. LLMs may hallucinate or simply miss corner cases. In addition, specialized tools are (usually) deterministic: their outputs are reproducible. LLMs are unpredictable: they may produce as output much more or much less text than is necessary and not necessarily adhere to required patterns and standards, which makes them hard to reliably integrate into CI/CD pipelines and large developer workflows. Post processing can alleviate this problem, but not solve it completely. Furthermore, we can usually understand why a specialized tool produces a certain result. LLMs are very good at explaining things, but their reasoning is beyond our reach and, when they explain their "thought process", it's usually too verbose and oftentimes incomplete and incorrect.

It is not clear to me whether resource use is a point in favor of LLMs or against them when it comes to software development. On the one hand, a typical linter is usually much cheaper to use even than a relatively small (3-4B parameters), self-hosted language model. On the other hand, many of the analyses required to support tasks such as bug detection in concurrent systems, automated bug fixing, and even checking for refactoring preconditions are expensive, if they are also expected to be precise. Since LLMs are general and flexible, though, I would still expect their regular use to support a variety of tasks to outweigh the costs of these more sophisticated tools. The sheer frequency of use would be the cause fo this.

The bottom line is that now there is a single general tool that is capable of doing the same thing that many other tools supporting software development combined can do. Thus the incentives are in favor of using this single tool, unless the other tools can justify their existence. 


## Two critical questions

Instead of asking “Will LLMs replace these tools?”, I think we should be asking two questions. The first is *"How much can we improve upon what the LLM provides?"* If a linter detects 95% of common issues and an LLM only catches 70%, then the linter clearly outperforms. Complementarily, if both can reach a similar recall but a specialized linter is able to reduce the number of false positives, that also gives it the edge, in terms of usefulness. Moreover, in safety-critical or compliance-heavy domains, the uncertainty that comes from using LLMs may be unacceptable. In this case, predictability and explainability are paramount. Improvements can come along multiple dimensions and what matters is whether they justify the usage of a specialized, potentially harder-to-use tool. The second question is *"Is the cost worth it?"* Developing and maintaining specialized tools can be expensive in terms of development and training costs. If an LLM gets “close enough,” many organizations may decide that the marginal benefit of a custom tool does not justify the investment. Conversely, the best LLMs require subscriptions that are likely to become more and more expensive to compensate for the sophisticated infrastructure on which they run and their resource expenditure. Also, the costs of mistakes must be accounted for: no one would want to fly on an airplane whose control systems were partially LLM-generated. In this scenario, the cost of failure is just too high and legal liability matters.


## What about hybrid tools?

This is not necessarily a dichotomy. It is also possible to build specialized tools that leverage LLMs as an important component. Specialized tools can benefit from enormous power of LLMs to expand their capabilities while avoiding some of the typical problems that come with LLM use. This is currently an active area of research, as are LLMs themselves. In any case, I think the previous considerations are still relevant; we would just be considering specialized tools built in a different way. 


## Concluding 

LLMs can be a next step in software development productivity, just like compilers, libraries, frameworks, and automated testing have been in the past. Their power and flexibility makes them capable of supporting a wide variety of software development tasks. They do have limitations though and, in this context, they serve as a baseline against which other tools can be compared. I believe that, where applicable, new improvements in software development tools and techniques should be framed as improvements over what existing LLMs can already provide.
