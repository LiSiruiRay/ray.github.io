---
title: What is Java Annotation?-intro
date: 2023-02-02 02:45:14
tags: [Java, Java Annotation, Programming, Tech, Tutorial]
categories:
- Tech
- Java
- Java Language
- Annotation Tutorial

excerpt: This is a blog about what annotation in java is.
---
# Annotation-intro

This is the video version:

<div style="position: relative; width: 100%; padding-bottom: 70%;">
  <iframe src="https://www.youtube.com/embed/XEE_BeI-U_I" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>


# Introduction
## Where have we seen annotation

- When learning inheritance: `@Override`
    - extend a super class
    - implements an interface
- When we use Unit test: `@Test`
    - To run something “without” a main method
- When learning Java Spring (totally fine if you haven’t touched anything about spring)
    
    : `@Configuration`,  `@Component`, `@Repository`, `@Autowire`...
    
    - We can use annotation to create beans
    - We can use annotation to set restriction for parameters

# What is annotation

## In short, it is a kind of “not part of the program”

Where have we see something that feels like not part of the program? Yes, comments! Annotation is just like comments. But instead of for human, annotation is more like a comment for program, specifically for java compiler.

## Why do we wanna learn annotation?

Admittedly, you can write Java programs and use annotation without learning the underlaying principle. However, if you are not satisfied with just using the frameworks developed by others, if you wanna do frameworks by yourself, or if you want to have a deeper understanding of what is inside of Java language design, then it is indispensable to learn annotation and finish this series.

Annotation is counted as an advanced knowledge of Java programming. You are stepping out of beginners and intermediate, if you are watching this video!

## Say in the end

- We will jump into the source code of annotation and have a touch on what exactly annotation is. (spoil alert! it is simply an interface)
- I will introduce several frequently used built-in annotation

# Definitions:

## --To describe programs, but for programs

## --Format: `@XXX`

# Functions:

## 1. To format java documentation (API doc)

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public class Student {
    /**
     *
     * @param name of the person you wanna greet
     * @param sentence of the greeting
     * @return full greeting sentence
     */
    public String greet(String name, String sentence) {
        return sentence + ", " + name;
    }
}
```
- A java document is something you see when you search up thing like "how to slipt String in Java". Here are some examples.
  - [Class String](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html)
  - [Class Collections](https://docs.oracle.com/javase/7/docs/api/java/util/Collections.html)

Then, type `javadoc` in the command line, and you will found the files pop out.

Click the `index.html`, you will find the java doc.

## 2. Analyze the code

## 3. Compiling check
