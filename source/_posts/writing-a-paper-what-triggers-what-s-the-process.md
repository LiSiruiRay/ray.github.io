---
title: 'writing a paper: what triggers? what''s the process?'
date: 2025-10-11 02:49:30
math: true
excerpt: Why do I want to do a research paper? Where is the problem from? How is the preparation process?
tags: [Research, Algorithms, Clustering Algorithms, LLM, AI]
categories:
  - Research Logs
  - Progress Logs
  - Computer Science
---

# What is happenning?

While I was developing the market analysis part of [EQUO](https://tryequo.com/), I met a problem that the current existing clustering algorithm does work good enough. I tried k-means and hierarchical clustering, with OpenAI's embedding model [TODO: check model]. The problem is that, always, the unrelated news are clustered as one event.

[TODO: add pictures]

I met the following problem:

- **Algorithm Effect**: As mentioend above, the cluster is not as good as what I wanted
- **Debugging Difficulties**: The passage -> vector process is blackboxed, this step we don't have control; even with control, a high demension vector is hard to understand
- **Hard to Visualize & Improve**: It is very hard to visualize and evaulate the final result. I tried to do a [PCA](https://en.wikipedia.org/wiki/Principal_component_analysis) on the vectors but the problem/concern is that the high demension passage vector will lose too much information to demensionized to a visualizible vector. Thus, it is harder to see if the clustering is good.

This problem also happened while I was doing the capybara project.

All the above prompt me to explore more clustering algorithm tailored for NLP purposes. I started making this exploration a formal research.

# Why a formal research

The reason is that I have been exposed to the pre-formal research progress too much. I have been involving in a research at NYU Abu Dhabi on the topic of "transformer based models on non-stationary time series data". I have been exposed to ODE researches. I have done some reading on formal verifications with my professor. But I have always reached only up to the point of finishing reading the materails, and never get involved into writing a formal publicable paper. 

Further than that, I have been interested in Quantitive Research positions since freshman year but I haven't really started fighting for that yet. This would be a perfect time for me to try if the researching life fits me.

Other than the more "real world reason", another motivation --or belief-- I am having is 1) I don't believe in linear learning. I believe that whatever you want to learn; you are interested; you want to pursue; you should start **right now**. There is no such thing that I need to prepare for this and that and there is no such thing as "I am not good enough yet". So if I am interested in researching, I am just gonna start working on it. No more hesitation. 2) I don't believe in the conventional/hierachy structure of the academic industry. I have a sense of different levels of respects for scholars. Professors are having the absolute right in charge of the advisee, the impilicite discremination against lower ranking schools or GPAs. I am just gonna be the one who research on whatever I am interested in. Even without being in a system.

# How am I preparing

GPT has started the revolution of powering everyone. Less and less resources are exclusive for high-class people. I am planning to use ChatGPT to guid most of my reserach process. Here is the [conversation](https://chatgpt.com/share/68ea04b9-1a0c-8013-9fe0-58c6f95be5b5)