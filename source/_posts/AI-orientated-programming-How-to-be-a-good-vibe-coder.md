---
title: 'AI orientated programming: How to be a good vibe coder'
date: 2025-10-13 04:15:23
math: true
excerpt: What is a good vibe coder? What's a developer's stand under AI era?
tags: [AI orientated coding, Vibe Coding, Cursor]
categories:
  - Project Logs
  - Start Up Logs
  - Lessons Learned
---

What exactly does LLMs change? Under this new era, where should a developer go?
---
# First touch on the concept: AI oriented programming

I have been using AI for programming for a while in different hackathons and result-orientated projects (unlike class project where you develop to learn, you are developing a product, and the final result is the most important).

Yet, it was when I was interning at Tiktok, right after I joined the Aurary team, when I learned that there is a lot to ponder about on AI programming.

# AI programming is not simply letting AI writing your code
After joining Aurary, I met Sony, and he talked me through the team's AI usage. It's several simple princeple yet it increased the stability, effeciency, and scalability of the project greatly. 

I extract three main points from our conversation:

- **Write docs** and let AI trace and update them accordingly: you can let LLM write the doc of course. But what important is that you are the one reviewing it and you have to make sure that the LLM is writing what you are looking for. What are your metrics? To what extense does it count as successful? What are the standard?
- Always let LLM write **unit tests**: LLMs can easily write thousands of lines of code after several round of conversation. That is one indirect reason why people wants it -- how fast it can develop stuff. But with greater power comes the greater danger. AI can write tons of for/if/while blocks embedded in one another, making the code unreadable. In this case, it is important to let the LLM to write the unit test, as a way for you to see the interface, the result, and as a tutorial to teach you how to use what AI built.
- Be clear on **what you should write** and what is good for AI: This is a rather vague point. But it is increasingly important for an AI orientated culture like us. You have to clear out your mind and know exactly what you wanted. You have to know exactly how you want the code to perform. You have to manual deal with some important steps: PRD writing, unit test, project stacks, etc.

The points aforementioend are the philosophies I figured during the talking with Sony. Before learning about this, I used to put a chunk of what I wanted and let AI directly jump into the coding process. The problem that after first 5 prompts the code become super bulky and become the so-called the “shit mountain code”. You can't understand the code and you can't change anyline. You won't know why it works and why it's not.

# View from my tech lead

After I started vibe coding, I realize that LLMs now are like short-memoried, dumb junior developers who has no background of what you wanted though with some locally excellent skills (know most of the library, remember/know most of the algorithms, never tired of writing comments, etc). Thus from this perspective it is giving programmers a chance to be a tech lead. 

I asked my tech lead at TikTok what are some qualities of a tech lead. A tech lead have to manage a team where there is very likely to be tech stacks that he/she does not understand. What do they do during this time? How are they management to lead the whole team to progress?

I ask this question because I also don't know every tech stacks my LLM is using. I am wondering under this scenario what I should do to manage my AI-developers and guid the overall process.

My tech lead's answer wasn't surprising: he told me that tech leads grew from a junior developer. The reason why a tech lead become a tech lead is that he is the lead of the tech. Which means that he must of alrady exceedingly good at tech stacks. He (tech lead) himself must has wrote most of the programs/tech stacks before becoming a tech lead. And what does that endow them? They can quickly understand what you are talking about even they don't know the detailed technology you are using. After probably at most 3 rounds of conversation he knows what you are talking about and have a view on the project. That is what makes a tech lead a tech lead.

So, even under the AI era, everyone can be a tech lead. We still should be modest and grow to be a tech lead.

# If LLM is gonna replace programmers, why can't everyone develop an app now?

I have a friend who's doing market at Tiktok, called Z. She is an AI edge app heavy user. She always tries the newsst technologies. I once asked her if she think that with all the tools (v0.dev, lovable, curosr, codex, etc), can a non-techniquel person like her develop a product solely? She immediately said no. She said that now the AIs might be able to create a demo page, a MVP, or a static website showing some information or portfolio. But to do a whole project and be able to communicate and upgrade? It's still not enough.

