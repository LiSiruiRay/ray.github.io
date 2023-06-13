---
title: Self-built Annotation :Format and Essence
date: 2023-02-04 00:41:32
tags: [Java, Java Annotation, Programming, Tech, Tutorial]
categories:
- Tech
- Java
- Java Language
- Annotation Tutorial
---

This is the video version of the tutorial:

<div style="position: relative; width: 100%; padding-bottom: 70%;">
  <iframe src="https://www.youtube.com/embed/kdzch2RvTOg" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

# Self built Java Annotation: Format & Essence

After learning about annotations built by others, we might wonder if we can also built some annotations by our own. After all, the beauty of learning programming is to build your own world.

However, we have no idea how to program an annotation ourselves. So what do we do? We simulate. So first let’s see how is the built-in annotation is programed.

# Format of annotation

We can jump into the source code clicking the `@Override`while holding `⌘command`. Here is the source code for `@Override`

## `@Override`

```java
/*
 ......
 * @jls 9.6.4.4 @Override
 * @since 1.5
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

Here we found that there are two part of the code. A chunk of code like this:

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
```

And another chunk of code like this:

```java
public @interface Override {
}
```

## `@Deprecated`

Now let’s see the source code for `@Deprecated`:

```java
/**
.......
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, MODULE, PARAMETER, TYPE})
public @interface Deprecated {
    /**
    ....
     */
    String since() default "";
    /**
     ....
     */
    boolean forRemoval() default false;

```

**It seems like that the definition of annotations is divided into two part: upper part with a bunch of other annotations; and a lower part with `public @interface ....`**

# To define an annotation:

We are correct, there are two parts. The annotation above `public @interface ....` is called “Meta annotation”, which means the annotation for annotation. Does that sound familiar? Yeah, we saw it in the last video in the [Java Annotation Wikipedia Page](https://en.wikipedia.org/wiki/Java_annotation), which is the thing that we would ignore for “now”.

{% asset_img Wikipedia_JAnnotation_MetaAnno.png Wikipedia page for meta annotation %}

# Create our own annotations:

Now we do not care about the meta annotation, will still delay that for now.

Yet for the following part, we always see something like `public @interface ....`, that is essentially how we build an annotation. Alright, since we have revealed the mysterious vail on the top of the annotation, let’s build one ourselves.

```java
public @interface RayAnno {}
```

Let’s try it out. 

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
@RayAnno
public class RayAnnoTest {
		@RayAnno
    int age;

		@RayAnno
    String name;

		@RayAnno
    public void testMethod1() {}

		@RayAnno
    public String testMethod2(int a, int b) {
        return "";
    }

		@RayAnno
    public void testMethod3() {}
}
```

We found that we could add my own annotation to wherever we want.

# What is behind annotation 

Now we’ve known how the define an annotation, though don’t know what that can be used for. But we are human. WE ARE CURIOUS. We want to know how this is working, just like we want to know others’ secrets.

We can do this by decompile. The java file will eventually be compiled to a [Byte-code](https://en.wikipedia.org/wiki/Bytecode) file, and we can decompile it to see what is really behind the scene.

So how do we do it?

We have the code:

```java
public @interface RayAnno {}
```

and a file `RayAnno.java`

We do the following to compile `.java` file, we will get a `RayAnno.class`file, which is the  [Byte-code](https://en.wikipedia.org/wiki/Bytecode) file aforementioned.

```bash
% javac RayAnno.java
```

Then we do the following commend to disassemble the `RayAnno.class`

```bash
% javac RayAnno.class
```

We get 

```bash
Compiled from "RayAnno.java"
public interface RayAnno extends java.lang.annotation.Annotation {
}
```

which means that a “new” `.java` file was generated, which includes:

```java
public interface RayAnno extends java.lang.annotation.Annotation {
}
```

How amazing!

Thus, we found the essence of the annotation. Which is **`public interface RayAnno extends java.lang.annotation.Annotation {}`**.

# `public interface RayAnno extends java.lang.annotation.Annotation {}`

This line of code reveals that we are actually creating a new interface when we create an annotation. Furthermore, this interface extends `Annotation` interface under package `java.lang.annotation`.

## Let’s take a trip into this intriguing `Annotation` interface

Here is the [API documentation](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/Annotation.html). We found the following:

{% asset_img API_Docu_Annotation.png Java Annotation Description %}

This just means that this interface is the root interface for all annotations. 

We also see some built in methods, which we will talk about later on.

{% asset_img API_JavaDoc_Anno_Method.png Java Annotation Methods %}

Now we have known that an annotation is simply an interface. Thus, it makes sense to induce that what can be defined in interfaces should be able to be defined in the annotation. And now we are going to talk about some characteristic about annotations.

# Annotation’s content

So what do we talk about in the interface? Methods. (Attributes are kinda useless in this case) Let’s take a look about the methods in annotations.

We can code some abstract method in annotations as:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public @interface RayAnno {
    public String show();
}
```

We can add an `abstract` of course:

```java
/**
 * @author : Ray Li
 * @created : 13/31/3033, 25:61
 * @description :
 */
public @interface RayAnno {
    public abstract String show();
}
```

We call those methods in annotation “attributes” of annotation. There are some special features about these attributes, which we will talk about soon.
