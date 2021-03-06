# Thread Fundamentals

## Introduction

A thread is like a transient gall agent. Unlike an agent, it can end and it can fail. The primary uses for threads are:

1. Complex IO, like making a bunch of external API calls where each call depends on the last. Including this in your agent significantly increases its complexity and the risk of a mishandled intermediary state corrupting permanent state. If you spin the IO out into a thread, your agent only has to make one call to the thread and receive one response.
2. Testing - threads are very useful for writing complex tests for your agents.

Threads are managed by the gall agent called `spider`.

## Thread location

Threads live in the `ted` directory of your desk:

```
%
├──app
├──gen
├──lib
├──mar
├──sur
├──sys
└──ted <-
   ├──foo
   │  └──bar.hoon
   └──baz.hoon
```

From the dojo, `ted/baz.hoon` can be run with `-baz`, and `ted/foo/bar.hoon` with `-foo-bar`.

**NOTE:** When the dojo sees the `-` prefix it automatically handles creating a thread ID, composing the argument, poking the `spider` gall agent and subscribing for the result. Running a thread from another context (eg. a gall agent) requires doing these things explicitly and is outside the scope of this particular tutorial.

## Libraries and Structures

There are three files that matter:

- `/sur/spider/hoon` - this contains a few simple structures used by spider. It's not terribly useful except it imports libstrand, so you'll typically import `spider` to get `strand`.

- `/lib/strand/hoon` - this contains all the main functions and structures for strands (a thread is a specific case of a strand), and you'll refer to this fairly frequently.

- `/lib/strandio/hoon` - this contains a large collection of ready-made functions for use in threads. You'll likely use many of these when you write threads, so it's very useful.

## Thread definition

`/sur/spider/hoon` defines a thread as:

`+$  thread  $-(vase _*form:(strand ,vase))`

That is, a gate which takes a `vase` and returns the `form` of a `strand` that produces a `vase`. Internally it will compose one or more `strand`s and is itself a `strand`. At this stage this won't be too meaningful, so let's look at its parts in detail.

## Strands

A strand is a function of `strand-input:strand -> output:strand`. You can see the details of `strand-input` [here](https://github.com/urbit/urbit/blob/master/pkg/arvo/lib/strand.hoon#L2-L21) and `output:strand` [here](https://github.com/urbit/urbit/blob/master/pkg/arvo/lib/strand.hoon#L23-L48). The input and output is handled automatically by spider so practically you don't need to worry about the details but it's useful to have a quick look.

A strand is a core that has three arms:
- `form`
- `pure`
- `bind`

A strand must be specialised to produce a particular type like `(strand ,<type>)`. As previously mentioned, a `thread` produces a `vase` so is specialised like `(strand ,vase)`. Within your thread you'll likely compose multiple strands which produce different types like `(strand ,@ud)`, `(strand ,[path cage])`, etc, but in the end it will always come back to a `(strand ,vase)`.

Strands are conventionally given the face `m` like:

```
=/  m  (strand ,vase)
...
```

<small>**NOTE:** a comma prefix as in `,vase` is the irregular form of `^:` which is a gate that returns the sample value if it's of the correct type, but crashes otherwise.</small> 

## Form and Pure

### `form`

The `form` arm is the mold of the strand, suitable `^-`. The two other arms produce `form`s so you'll cast everything to this like:

```
=/  m  (strand ,@ud)
^-  form:m
...
```

### `pure`

Pure produces a strand that does nothing except return a value. So, `(pure:(strand ,@tas) %foo)` is a strand that produces `%foo` without doing any IO.

We'll cover `bind` later.

## A trivial thread

```
/-  spider 
=,  strand=strand:spider 
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m 
(pure:m arg)
```

The above code is a simple thread that just echos its input, and it's a good boilerplate to start from.

Save the above code as a file in `ted/mythread.hoon` and `|commit` it. Run it with `-mythread 'foo'`, you should see the following:


```
> -mythread 'foo'
[~ 'foo']
```

**NOTE:** The dojo wraps arguments in a unit so that's why it's `[~ 'foo']` rather than just `foo`.

## Analysis

We'll go through it line-by line.

<h3>

```
/-  spider 
=,  strand=strand:spider 
```
</h3>

First we import `/sur/spider/hoon` which includes `/lib/strand/hoon` and give give the latter the face `strand` for convenience.

<h3>

```
^-  thread:spider
```
</h3>

We make it a thread by casting it to `thread:spider`

<h3>

```
|=  arg=vase
```
</h3>

We create a gate that takes a vase, the first part of the previously mentioned thread definition.

<h3>

```
=/  m  (strand ,vase)
```
</h3>

Inside the gate we create our `strand` specialised to produce a `vase` and give it the canonical face `m`.

<h3>

```
^-  form:m 
```
</h3>

We cast the output to `form` - the mold of the strand we created.

<h3>

```
(pure:m arg)
```
</h3>

Finally we call `pure` with the gate input `arg` as its argument. Since `arg` is a `vase` it will return the `form` of a `strand` which produces a `vase`. Thus we've created a thread in accordance with its type definition.

Next we'll look at the third arm of a strand: `bind`.

# [Previous](#) | [Next](2_micgal-and-bind.md)

