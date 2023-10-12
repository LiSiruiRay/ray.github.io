---
title: Java Annotation-built in annotation
date: 2023-02-02 17:20:59
tags: [Java, Java Annotation, Programming, Tech, Tutorial]
categories:
  - Tech
  - Java
  - Java Language
  - Annotation Tutorial
---

Here is the video version:

<div style="position: relative; width: 100%; padding-bottom: 70%;">
  <iframe src="https://www.youtube.com/embed/DPzwfhlGf-c" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

# What are some built in annotations

We can check some definitions from the [Wikipedia page about Java annotation](https://en.wikipedia.org/wiki/Java_annotation)

We found that they introduced three built in annotation here

{% asset_img wiki_javaAnno.png Wikipidia page about java annotation %}

## `@Override`: check if the method is correctly overriding another one

## `@Deprecated`: to note that the method is out-dated (might have a problem or too slow)

## `@SuppressWarnings`: to wipe out warnings

now let's jump into some code and see how these three annotations are working in real-world cases

# `@Override`

As we introduce before in the [Annotation-intro](https://blog.slray.com/2023/02/02/What-is-Java-Annotation-intro/), `@Override` annotation is for checking whether a method is correctly overriding this class’ super class, or interface.

Let's go through some example codes:

We have an `Animal` class:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :An animal class for annotation demo
 */
public class Animal {
    String name;
    int age;
    public void attack(String name) {
        System.out.println("attacking->" + name);
    }
}
```

And a `cat`class that extend `Animal`class:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public class Cat extends Animal{
		//If we change the return type, input parameter type or amount
		//then the program will post an erro
    @Override
    public void attack(String name) {
        System.out.println("Miao attack! ->" + name);
    }
}
```

# `@Deprecated`

`@Deprecated` annotation will mark current method as out-dated.

Let’s look at an example.

We have a `SumCalculator`class in which we are planning to do sum calculation. At first we have the method as such:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public class SumCalculator {
    public int sum(int a, int b) {return a+b;}
}
```

but later on we found that the `sum()`method can only take two parameters, but we wanna change it so that it could take whatever numbers people wanna input. Moreover, we also wanna inform people that the `sum()`method is out-dated. Instead, people should use `sumNumbers()`. We can do such thing:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public class SumCalculator {
    @Deprecated
    public int sum(int a, int b) {return a+b;}
    public int sumNumbers(int[] nums) {
        int result = 0;
        for (int num : nums) {
            result += num;
        }

        return result;
    }
}
```

Notice here we can add a `@Deprecated` on the top of `sum()` method. Thus when we call it, we will have following effect to remind users that they are calling deprecated methods.

{% asset_img sum_depre.png Deprecated sum reminding %}

## Some source code with `@Deprecated`:

Where do we see a lot of deprecated method? When we are coding involving `Date` class.

{% asset_img builtin_depre.png Built in built in deprecated methods %}

# `@SuppressWarnings`:

This annotation is used to suppress compiler’s warning.

When we write code like this, we can see on the top right corner where is a yellow triangle with an exclamation mark with number 4. We can also see that on the right side, there are 4 bars.

{% asset_img warnings.png All the warnings %}

Each bar represent some warning. Such as “Method ‘test()’ is never used”.

{% asset_img warning_big.png detailed warnings %}

But we can suppress the warning by doing this:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public class WarningDemo {
    @SuppressWarnings("all")
    public static void test(){}
}
```

Notice we added `@SuppressWarnings("all")` on the top of the method whose warning you want to suppress.

After doing this, you will find that the yellow bar disappeared.

- Parameters in annotation:

You might have noticed that there was an “all” parameter inside of the annotation. This means we want to suppress all the warning. We will look as this later.

Normally, we would add `@SuppressWarnings("all")` on the top of the class, so that we can suppress all the warnings.
