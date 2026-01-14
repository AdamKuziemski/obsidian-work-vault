---
tags:
  - ai
author: Wei Xing
source: https://theconversation.com/why-openais-solution-to-ai-hallucinations-would-kill-chatgpt-tomorrow-265107
read: true
---
[OpenAI's latest research paper](https://openai.com/index/why-language-models-hallucinate/) diagnoses exactly why ChatGPT and other [large language models](https://en.wikipedia.org/wiki/Large_language_model) can make things up – known in the world of [artificial intelligence](https://www.sciencealert.com/artificial-intelligence) as "hallucination". It also reveals why the problem may be unfixable, at least as far as consumers are concerned.

The paper provides the most rigorous mathematical explanation yet for why these models confidently state falsehoods. It demonstrates that these aren't just an unfortunate side effect of the way that AIs are currently trained, but are mathematically inevitable.

The issue can partly be explained by mistakes in the underlying data used to train the AIs. But using mathematical analysis of how AI systems learn, the researchers prove that even with perfect training data, the problem still exists.

**Related: [Why Does AI Feel So Human if It's Just a 'Calculator For Words'?](https://www.sciencealert.com/why-does-ai-feel-so-human-if-its-just-a-calculator-for-words)**

The way language models respond to queries – by predicting one word at a time in a sentence, based on probabilities – naturally produces errors. The researchers in fact show that the total error rate for generating sentences is at least twice as high as the error rate the same AI would have on a simple yes/no question, because mistakes can accumulate over multiple predictions.

In other words, hallucination rates are fundamentally bounded by how well AI systems can distinguish valid from invalid responses. Since this classification problem is inherently difficult for many areas of knowledge, hallucinations become unavoidable.

It also turns out that the less a model sees a fact during training, the more likely it is to hallucinate when asked about it. With birthdays of notable figures, for instance, it was found that if 20 percent of such people's birthdays only appear once in training data, then base models should get at least 20 percent of birthday queries wrong.

Sure enough, when researchers asked state-of-the-art models for the birthday of Adam Kalai, one of the paper's authors, DeepSeek-V3 confidently provided three different incorrect dates across separate attempts: "03-07", "15-06", and "01-01".

The correct date is in the autumn, so none of these were even close.

## The evaluation trap

More troubling is the paper's analysis of why hallucinations persist despite post-training efforts (such as providing extensive human feedback to an AI's responses before it is released to the public).

The authors examined ten major AI benchmarks, including those used by Google, OpenAI, and the top leaderboards that rank AI models. This revealed that nine benchmarks use binary grading systems that award zero points for AIs expressing uncertainty.

This creates what the authors term an " [epidemic](https://www.sciencealert.com/pandemic)" of penalizing honest responses. When an AI system says "I don't know", it receives the same score as giving completely wrong information.

The optimal strategy under such evaluation becomes clear: always guess.

The researchers prove this mathematically. Whatever the chances of a particular answer being right, the expected score of guessing always exceeds the score of abstaining when an evaluation uses binary grading.

## The solution that would break everything

OpenAI's proposed fix is to have the AI consider its own confidence in an answer before putting it out there, and for benchmarks to score them on that basis.

The AI could then be prompted, for instance: "Answer only if you are more than 75 percent confident, since mistakes are penalized 3 points while correct answers receive 1 point."

The OpenAI researchers' mathematical framework shows that under appropriate confidence thresholds, AI systems would naturally express uncertainty rather than guess. So this would lead to fewer hallucinations. The problem is what it would do to user experience.

Consider the implications if ChatGPT started saying "I don't know" to even 30% of queries – a conservative estimate based on the paper's analysis of factual uncertainty in training data. Users accustomed to receiving confident answers to virtually any question would likely abandon such systems rapidly.

I've seen this kind of problem in another area of my life. I'm involved in an air-quality monitoring project in Salt Lake City, Utah.

When the system flags uncertainties around measurements during adverse weather conditions or when equipment is being calibrated, there's less user engagement compared to displays showing confident readings – even when those confident readings prove inaccurate during validation.

frameborder="0″ allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen&gt;

![https://www.youtube.com/watch?v=SvBR0OGT5VI](https://www.youtube.com/watch?v=SvBR0OGT5VI&themeRefresh=1)
## The computational economics problem

It wouldn't be difficult to reduce hallucinations using the paper's insights. Established methods for quantifying uncertainty have [existed](https://en.wikipedia.org/wiki/Bayesian_statistics) for [decades](https://en.wikipedia.org/wiki/Decision_theory).

These could be used to provide trustworthy estimates of uncertainty and guide an AI to make smarter choices.

But even if the problem of users disliking this uncertainty could be overcome, there's a bigger obstacle: computational economics.

Uncertainty-aware language models require significantly more computation than today's approach, as they must evaluate multiple possible responses and estimate confidence levels. For a system processing millions of queries daily, this translates to dramatically higher operational costs.

[More sophisticated approaches](https://openreview.net/forum?id=JAMxRSXLFz) like active learning, where AI systems ask clarifying questions to reduce uncertainty, can improve accuracy but further multiply computational requirements.

Such methods work well in specialized domains like chip design, where wrong answers cost millions of dollars and justify extensive computation. For consumer applications where users expect instant responses, the economics become prohibitive.

The calculus shifts dramatically for AI systems managing critical business operations or economic infrastructure. When AI agents handle supply chain logistics, financial trading or medical diagnostics, the cost of hallucinations far exceeds the expense of getting models to decide whether they're too uncertain.

In these domains, the paper's proposed solutions become economically viable – even necessary. Uncertain AI agents will just have to cost more.

However, consumer applications still dominate AI development priorities. Users want systems that provide confident answers to any question. Evaluation benchmarks reward systems that guess rather than express uncertainty. Computational costs favor fast, overconfident responses over slow, uncertain ones.

Falling energy costs per token and advancing chip architectures may eventually make it more affordable to have AIs decide whether they're certain enough to answer a question. But the relatively high amount of computation required compared to today's guessing would remain, regardless of absolute hardware costs.

In short, the OpenAI paper inadvertently highlights an uncomfortable truth: the business incentives driving consumer AI development remain fundamentally misaligned with reducing hallucinations.

Until these incentives change, hallucinations will persist.![The Conversation](https://counter.theconversation.com/content/265107/count.gif?distributor=republish-lightbox-basic)

_[Wei Xing](https://theconversation.com/profiles/wei-xing-2478822), Assistant Professor, School of Mathematical and Physical Sciences, [University of Sheffield](https://theconversation.com/institutions/university-of-sheffield-1147)_

**This article is republished from [The Conversation](https://theconversation.com) under a Creative Commons license. Read the [original article](https://theconversation.com/why-openais-solution-to-ai-hallucinations-would-kill-chatgpt-tomorrow-265107).**
