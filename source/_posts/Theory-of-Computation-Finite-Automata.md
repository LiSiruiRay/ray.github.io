---
title: 'Theory of Computation: Finite Automata'
date: 2025-01-30 09:34:49
tags: [Math, Proof Theory, Computer Science, Theory of Computation, Finite Automata, Regular Expression]
categories:
  - Daily Logs
  - Computer Science
  - Theory of Computation
---
# Regular Expression
I was wondering if the operation of regular expression (Union, Concatenation, Star) is complete.

This question needs further prompt: In what sense do I mean complete? 

Am I asking:
 - Does every string is representable with the regular operation?
 - Does the three operation formed the universe set of operation? (Whatever the universe set of operation means)
 - Does the three operation adding more information to each other? (By that I could mean that "why can't we define the operation by something else; by less operations?")
 - ...

I eventually chose the last question listed above. The answer for now is easy: this set of operation corresponds to what a finite automata can do. By that, I meant that a finite automata can recognize every regular expression.