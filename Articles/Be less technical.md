---
tags:
  - topic/programming/philosophy
source: https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/
author: Shivan Someone
read: true
---
_Disclaimer: This is by no means an attempt to tell you to not be technical at all, but rather a series of thoughts about situations under which thinking in terms of implementation can remove more value than it adds. I am also not an expert in product or engineering - just someone who is trying to write down my thoughts :)_

**Be less technical**. That’s it.

You’re an engineer. You solve **problems*** by designing solutions to them that run on _really_ advanced calculators. You do this for a reason. Perhaps that reason is that you are overwhelmingly concerned with the speed and logistics of grocery delivery, or maybe you’d just like to buy some groceries for yourself at the end of the month. Whatever the reason, you solve problems, and you’re likely damn good at it (the solving of problems that is, not delivering groceries).

But you need to **Be less technical** when describing and thinking about how to solve those far-reaching problems and the atomic** ones that occur along the way.

That may sound blasphemous, but please bear with me for a second (or a few minutes). I am not saying that you should reject everything you’ve learned and cultivated over the years, but rather - you should learn how to communicate those things to someone who hasn’t had that same experience of learning and cultivation as you.

### Language is the interface for describing problems [#](https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/#language-is-the-interface-for-describing-problems)

Over time, our species has developed and refined thousands of languages. Structured forms of communication that are, for the most part, highly interoperable. That is to say, we have the means to translate between the most popular languages with relative ease.

This has enabled us to think about humanity, its ongoing survival, and its conflicts across geographies and cultures.

We have developed structured, formal rules for **transferring information** between each other to ensure that **we can make informed decisions**.

By that definition, the way we communicate with each other (by speaking, writing, illustrating, etc) - becomes a way for us to describe the problems we experience, and by extension - reason about solving those problems.

### Natural language and software engineering [#](https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/#natural-language-and-software-engineering)

Software engineering, in my opinion, has developed its own formal language for communication. It is so pervasive and commonplace in conversation on the day to day, that I would go as far as to say that it is in a way a steady-state natural language that lives outside of English (and perhaps has analogues in other spoken languages that I am not aware of).

As an engineer who operates in this environment*** - you would have mastered this language quite quickly - without realizing that it can be incredibly exclusionary.

This language is **technical.** (Which can be a term used in other professional disciplines to refer to language constructs specifically adapted to their discipline).

The problem when describing and thinking and speaking about problems in technical language, is that it automatically excludes people who do not operate in that immediate environment.

This might raise a flag and question for some people.

> _“But surely, by working in tech, you should know these things?”_

At face value, this may be a solid assumption and question. But this ignores the fact that not everyone that works in “tech” is working near engineering. That is, they are not tasked with producing solutions **in code**. They may work in a customer-facing role, or perhaps are exceptional at designing user interfaces. They have a _different technical language_.

This is when it becomes especially important to **be less technical.**

_Software is not the only discipline that has an exclusionary technical language. Mathematics, which is the lingua franca of physics (among other “hard” sciences), is typically exclusionary at the highest levels (take for instance the [Navier-Stokes equations](https://web.archive.org/web/20220716105226/https://en.wikipedia.org/wiki/Navier%E2%80%93Stokes_equations) - a set of partial differential equations that was presented to me as a first-year engineering student that scared me into computer science). That has not however inhibited some of the world's greatest minds from attempting to make physics accessible (see: [A Brief History of Time](https://web.archive.org/web/20220716105226/https://www.goodreads.com/book/show/3869.A_Brief_History_of_Time))_

### Language inherent constraints [#](https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/#language-inherent-constraints)

Another point to be aware of is that languages and the barriers between them can introduce constraints on how many “bytes” of information we can transfer between each other.

Consider the asymmetrical information relationship between the German phrase:

> “Schadenfreude”

And its “equivalent” English translation:

> “...means joy over some harm or misfortune suffered by another” - [Merriam Webster](https://web.archive.org/web/20220716105226/https://www.merriam-webster.com/dictionary/schadenfreude)

Though not a perfectly applicable example, we can see that the English language, due to its rules and formalization, does not have a _commonly_ used English equivalent. This is an **inherent constraint** in the English language as it pertains to its translation to German.

_Edit (17-04-2022): As clarified by a [poster](https://web.archive.org/web/20220716105226/https://news.ycombinator.com/reply?id=31059099&goto=item%3Fid%3D31046268%2331059099) on HackerNews, “Schadenfreude” does actually constitute an English word, seeing as it is a loanword (and I have linked to an English dictionary to reference it). A [single-word English equivalent](https://web.archive.org/web/20220716105226/https://en.wikipedia.org/wiki/Schadenfreude) may also exist ("Epicaricacy"), though I am not sure of its prevalence and acceptance. I do still feel like this a good example of an asymmetrical translation, since the meaning/translation is not immediately apparent._

Similarly, regular English, may not have a single word or phrase that encapsulates the intricacies involved in implementing, for example, a full-text search engine.

This is a **constraint** that we have to be aware exists when talking about problems and the solutions. Discussions of _ElasticSearch, Lucene and inverted indices_ may not transfer as much useful information as we think to our audience and peers.

A word in regular English, e.g. “Search” - may not have a well-defined technical translation outside the implementation. This restricts our ability to think about the problem because suddenly, we are focused on _implementing a search engine_ without fully understanding what someone might have meant by “search”. (Of course, this is a reductive view and assumes that engineers are focused solely on implementation detail 😉).

It is our job to make sure we communicate and problem solve using a shared set of rules.

### Why should I be less technical? Surely others can learn to be technical? [#](https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/#why-should-i-be-less-technical-surely-others-can-learn-to-be-technical)

This doesn’t warrant a paragraph or long explanation. The simple answer is that you have already got the **interoperability layer** between technical language and “regular English” in your head. As a consequence of understanding the technical details, you are best equipped to be the **translation layer.**

**Be less technical. Be an interface for the technical details. Translate.**

### When can I be technical? [#](https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/#when-can-i-be-technical)

All that said, there are times to be technical, or else the language wouldn’t exist. I think the nuance is that technical communication needs to happen at the right level of granularity. I would err on the side of caution of being deeply technical when the audience and your peers are for the most part, not engineers.

I would be more technical in discussions and situations that are centered on scaling systems and improving their speed, but even in these cases, I would look first for the “regular English” motivations for scale and speed. Perhaps other solutions exist outside engineering?

A contrived example of focusing less on technical details and paying more attention to regular English communication:

> A: Our users are complaining our system is slow!  
> B: Ah okay, maybe we need to optimize how fast our pages load up, it's probably an API problem - engineering can fix that!  
> C: Hold on, what part of our system is slow? Is it the app?  
> A: Oh I don’t know - it was one of our users that uses our app on his phone a lot.  
> C: Hmm, perhaps he has a slow data connection is slow? Can we look at his network speed on a dashboard or something?

### Alright, how can I be less technical? [#](https://web.archive.org/web/20220716105226/https://www.sequential.dev/posts/be-less-technical/#alright-how-can-i-be-less-technical)

The simplest advice I have is to write more regular English. As engineers we are:

- Writing code for other engineer-humans to read, and for machines to parse
- Writing documentation for that code, in the hopes that other engineer-humans will be able to use our code

But we are hardly writing for non-engineer humans.

It is my honest belief that to be highly effective at problem-solving for people, we have to be world-class communicators. Writing is a good start.

The second piece of advice would be to pay attention to highly successful consumer technology and open source projects. Focus on the decisions they’ve made that drastically changed their product, why those decisions came to be, and the technical constraints and impact those decisions had (and the way engineers had to translate those decisions into code, and back).

As an example:

Why did Twitter increase the word count on tweets several years back? And what was the implementation detail that backed that up? What technical constraints might they have faced? And how do you hypothesize their engineering team communicated those constraints?

To be quite honest, being less technical is something I am still learning how to do, so I do not have all the answers, and I doubt I ever will - but I think that if we as engineers can describe problems without leaning excessively on technical details, we’re all going to build better solutions.

If you enjoyed reading this, give me a follow on [Twitter](https://web.archive.org/web/20220716105226/https://twitter.com/shivmo18)!

---

*Most times these problems are experienced by people, or maybe they’re experienced by machines, but in some way - you are transiently solving for problems experienced by people

**In this case, “atomic” refers to a set of problems that cumulatively may provide a solution to a larger problem. e.g. tracking drivers for a delivery platform (which in itself may contain smaller problems)

***And if you were lucky enough to study an engineering discipline, were educated in this environment