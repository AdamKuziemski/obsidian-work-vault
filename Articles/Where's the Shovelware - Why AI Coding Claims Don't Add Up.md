---
tags:
  - ai
  - topic/programming
author: Judge Mike
source: https://mikelovesrobots.substack.com/p/wheres-the-shovelware-why-ai-coding
read: true
---
I’m furious. I’m really angry. I’m angry in a knocking down sandcastles and punching Daniel LaRusso in the face and talking smack about him to his girl kind of way.

I’m not an angry person generally, but I can’t stand what’s happening to my industry.

I know software development. I’ve been doing it for 25 years, maybe even 28 years[^1] if you count market research tabulation on amber monochrome screens. Yes, I’m old. I’m a middle-aged programming nerd. My entire life and personal identity are wrapped up in this programming thing for better or worse. I thrive off the dopamine hits from shipping cool things.

I was an early adopter of AI coding and a fan until maybe two months ago, when I read the [METR study](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) and suddenly got serious doubts. In that study, the authors discovered that developers were unreliable narrators of their own productivity. They thought AI was making them 20% faster, but it was actually making them 19% slower. This shocked me because I had just told someone the week before that I thought AI was only making me about 25% faster, and I was bummed it wasn’t a higher number. I was only off by 5% from the developer’s own incorrect estimates.

This was unsettling. It was impossible not to question if I too were an unreliable narrator of my own experience. Was I hoodwinked by the screens of code flying by and had no way of quantifying whether all that reading and reviewing of code actually took more time in the first place than just doing the thing myself?

So, I started testing my own productivity using a modified methodology from that study. I’d take a task and I’d estimate how long it would take to code if I were doing it by hand, and then I’d flip a coin, heads I’d use AI, and tails I’d just do it myself. Then I’d record when I started and when I ended. That would give me the delta, and I could use the delta to build AI vs no AI charts, and I’d see some trends. I ran that for six weeks, recording all that data, and do you know what I discovered?

![[53c83842-05e1-4ca1-9021-c292e8f3a502_600x371.png]]

I discovered that the data isn’t statistically significant at any meaningful level. That I would need to record new datapoints for another four months just to prove if AI was speeding me up or slowing me down at all. It’s too neck-and-neck.

That lack of differentiation between the groups is really interesting though. Yes, it’s a limited sample and could be chance, but also so far AI appears to slow me down by a median of 21%, exactly in line with the METR study. I can say definitively that I’m not seeing any massive increase in speed (i.e., 2x) using AI coding tools. If I were, the results would be statistically significant and the study would be over.

That’s really disappointing.

I wish the AI coding dream were true. I wish I could make every dumb coding idea I ever had a reality. I wish I could make a fretboard learning app on Monday, a Korean trainer on Wednesday, and a video game on Saturday. I’d release them all. I’d drown the world in a flood of shovelware like the world had never seen. Well, I would — if it worked.

It turns out, though, and I’ve collected a lot of data on this, it doesn’t just not work for me, it doesn’t work for anyone, and I’m going to prove that.

But first, let’s examine how extreme and widespread these productivity claims are. Cursor’s tagline is “Built to make you extraordinarily productive.” Claude Code’s is “Build Better Software Faster.” GitHub Copilot’s is “Delegate like a boss.” Google says their LLMs make their developers 25% faster. OpenAI makes their own bombastic claims about their coding efficiencies and studies[^2]. And my fellow developers themselves are no better, with 14% claiming they’re seeing a 10x increase in output due to AI.[^3]

> “Delegate like a boss” – Github Copilot

These claims wouldn't matter if the topic weren't so deadly serious. Tech leaders everywhere are buying into the FOMO, convinced their competitors are getting massive gains they're missing out on. This drives them to rebrand as AI-First companies[^4], justify layoffs with newfound productivity narratives, and lowball developer salaries under the assumption that AI has fundamentally changed the value equation.

And yet, despite the most widespread adoption one could imagine[^5], these tools don’t work.

My argument: If so many developers are so extraordinarily productive using these tools, where is the flood of shovelware? We should be seeing apps of all shapes and sizes, video games, new websites, mobile apps, software-as-a-service apps — we should be drowning in choice. We should be in the middle of an indie software revolution. We should be seeing 10,000 Tetris clones on Steam.

Consider this: with all you know about AI-assisted coding and its wide adoption, if I showed you charts and graphs of new software releases across the world, what shape of that graph would you expect? Surely you’d be seeing an exponential growth up-and-to-the-right as adoption took hold and people started producing more?

Now, I’ve spent a lot of money and weeks putting the data for this article together, processing tens of terabytes of data in some cases. So I hope you appreciate how utterly uninspiring and flat these charts are across every major sector of software development.

<img src="https://substackcdn.com/image/fetch/$s_!5F2-!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F79dbad0e-04eb-4fb8-b114-613754379bd2_2428x1288.png"/>
from Statista

<img src="https://substackcdn.com/image/fetch/$s_!MQQk!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6886f9ed-e545-48ba-a6c0-a7b31aa7813b_2454x1372.png"/>
from Statista

<img src="https://substackcdn.com/image/fetch/$s_!xhVj!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd70b517d-c59b-4cb8-8b4f-7e5ee58f0424_632x568.png"/>
from Domain Name Industry Brief by Verisign

<img src="https://substackcdn.com/image/fetch/$s_!No7i!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7adbd0a6-61c0-40a7-8c3a-b436421aa4f0_656x408.png"/>
From SteamDB

<img src="https://substackcdn.com/image/fetch/$s_!eBmO!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F4e23e3e0-d046-4b58-ab6d-bb8e78d38493_664x415.png"/>
I spent $70 on BigQuery processing to make this chart. Via GH Archive

The most interesting thing about these charts is what they’re not showing. They’re not showing a sudden spike or hockey-stick line of growth. They’re flat at best. There’s no shovelware surge. There’s no sudden indie boom occurring post-2022/2023. You could not tell looking at these charts when AI-assisted coding became widely adopted. The core premise is flawed. Nobody is shipping more than before.

The impact on human lives is incredible. People are being fired because they’re not adopting these tools fast enough[^6]. People are sitting in jobs they don’t like because they’re afraid if they go somewhere else it’ll be worse. People are spending all this time trying to get good at prompting and feeling bad because they’re failing.

This whole thing is bullshit.

So if you're a developer feeling pressured to adopt these tools — by your manager, your peers, or the general industry hysteria — trust your gut. If these tools feel clunky, if they're slowing you down, if you're confused how other people can be so productive, you're not broken. The data backs up what you're experiencing. You're not falling behind by sticking with what you know works. If you’re feeling brave, show your manager these charts and ask them what they think about it.

If you take away anything from this it should be that (A) developers aren't shipping anything more than they were before (that’s the only metric that matters), and (B) if someone — whether it's your CEO, your tech lead, or some Reddit dork — claims they're now a 10xer because of AI, that’s almost assuredly untrue, demand they show receipts or shut the fuck up.

Now, I know the internet. I know what many of you chumps are going to say before you even say it, so let’s just get into it:

1. *“Well, if you just learned how to prompt properly, then you would be a 10x engineer like me.”*
   Look at the data. There are no new 10xers. If there were — if the 14% of self-proclaimed AI 10xers were actually 10xers — that would more than double the worldwide output of new software. That didn’t happen. And as for you, personally, show me the 30 apps you created this year. I’m not entertaining this without receipts.
   
2. *“Well, it’s a new technology and so much is invested, and it takes time…”*
   Yes, billions of dollars have been invested in these tools. Billions of dollars will continue to be invested in these tools. The problem is that they’re being sold and decisions are being made about them — which affect real people’s lives — as if they work today. Don’t parrot that nonsense to me that it’s a work in progress. It’s September 2025, and we’ve had these tools for years now, and they still suck. Someday, maybe they won’t suck, but we'd better see objective proof of them having an impact on actually shipping things on the large.
   
3. *“Well, maybe it kind of sucks now, but if you don’t adopt it early, you’ll be left behind.”*
   There are no indicators that prompting is hard to learn. Github Copilot themselves say that [initially, users only accept 29% of prompted coding suggestions](https://github.blog/news-insights/research/the-economic-impact-of-the-ai-powered-developer-lifecycle-and-lessons-from-github-copilot/) (which itself is a wild claim to inefficiency, why would you publicize that?), but with six months of experience, users naturally get better at prompting and that grows to a whopping 34% acceptance rate. Apparently, 6 months of experience only makes you 5% better at prompting.
   
4. *“Well, maybe quality is going up and things aren’t necessarily shipping faster…”*
   That doesn’t make any sense. We all know that the industry has taken a step back in terms of code quality by at least a decade. Hardly anyone tests anymore. The last time I heard the phrase “continuous improvement” or “test-driven development” was before COVID. You know as well as I do that if there’s a tool that can make people 10x coders, we’d be drowning in shovelware.
   
5. *“Well, it’s all website-driven, and people don’t really care about domain names these days; it’s all subdomains on sites like Vercel.”*
   Shut up. People love their ego domains.
   
6. *“Well, .ai domain names are up 47% this year…”*
   Yeah, that’s cause all the startups pivoted to AI. It’s the only way to get money out of investor FOMO. Has the overall amount of domain names gone up at an unprecedented rate, though? No, it hasn’t. Look at the new domains chart.
   
7. *“Well, if you were a real engineer, you’d know that most of software development is not writing code.”*
   That’s only true when you’re in a large corporation. When you’re by yourself, when you’re the stakeholder as well as the developer, you’re not in meetings. You're telling me that people aren’t shipping anything solo anymore? That people aren’t shipping new GitHub projects that scratch a personal itch? How does software creation not involve code?

Thanks for reading! Subscribe for free to receive new posts and support my work.

[^1]: 28 years of experience is approximately 55,000 hours. I thought that was a fun metric.

[^2]: Sam Altman says programmers are currently "10 times more productive" with AI coding. And that the world wants"100 times maybe a thousand times more software." (I agree there.) Altman also predicts programmers will "make three times as much" in the future. From https://www.finalroundai.com/blog/sam-altman-says-world-wants-1000x-more-software

[^3]: 78% of developers using AI report productivity gains, with 17% of those (13-14% of total developers surveyed) claiming a “10×” increase in output from AI - http://qodo.ai/reports/state-of-ai-code-quality

[^4]: Funny to me how none of these “AI First” coding shops reportedly provide any training on how to become a 10xer with AI coding. “Experiment and figure it out yourself” is the common advice. Meanwhile, the official prompting guides are apparently not worth paying attention to because they don’t work. You see the dissonance, right?

[^5]: 60% of developers report using AI coding tools daily, and 82% use AI coding tools at least weekly. - http://qodo.ai/reports/state-of-ai-code-quality

[^6]: The Coinbase CEO fired engineers last week who would not use Cursor or Copilot - https://www.finalroundai.com/blog/coinbase-ceo-fired-engineers-for-not-using-ai-tools
