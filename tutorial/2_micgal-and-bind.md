# Micgal and Bind

Having looked at `form` and `pure`, we'll now look at the last `strand` arm `bind`. Bind is typically used in combination with the micgal rune (`;<`).

## Micgal

Micgal takes four arguments like `spec hoon hoon hoon`. Given `;<  a  b  c  d`, it composes them like `((b ,a) c |=(a d))`. So, for example, these two expressions are equivalent:

```
;<  ~  bind:m  (sleep:strandio ~s2)
(pure:m !>(~))
```

and
```
((bind:m ,~) (sleep:strandio ~s2) |=(~ (pure:m !>(~))))
```

Micgal exists simply for readability. The above isn't too bad, but consider this:

```
;<  a  b  c
;<  d  e  f
;<  g  h  i
j
```
...as opposed to this monstrosity: `((b ,a) c |=(a ((e ,d) f |=(d ((h ,g) i |=(g j))))))`

## bind

Bind must be specialised like `(bind:m ,<type>)` and it takes two arguments. The first argument is a gate that returns a `form` which produces `<type>`. The second argument is a gate whose sample is `<type>` and which returns a `form`.

Bind calls the first gate then, if it succeeded, calls the second gate with the result of the first as its sample. If the first gate failed, it will instead just return an error message and not bother calling the next gate. 

Since the second gate may itself contain another `;<  <type>  bind:m  ...`, you can see how this allows you to glue together an arbitrarily large pipeline, where subsequent gates depend on the previous ones.

## strandio

`/lib/strandio/hoon` contains a large collection of useful, ready-made functions for use in threads. For example:

- `sleep` waits for the specified time.
- `get-time` gets the current time.
- `poke` pokes an agent.
- `watch` subscribes to an agent.
- `fetch-json` produces the JSON at a particular URL.
- `retry` tries a strand repeatedly with exponential backoff until it succeeds.
- `start-thread` starts another thread.
- `send-raw-card` sends any card.

...and many more.

## Putting it together

Here's a simple thread with a couple of `strandio` functions:

```
/-  spider
/+  strandio
=,  strand=strand:spider 
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m
;<  s=ship  bind:m  get-our:strandio
;<  t=@da   bind:m  get-time:strandio
;<  ~       bind:m  (sleep:strandio ~s1)
(pure:m !>([s t]))
```

Save it as `/ted/mythread.hoon`, `|commit` it and run it with `-mythread`. You should see something like:

```
> -mythread
[~zod ~2021.3.6..14.15.39..45d3]
```

## Analysis

To use `strandio` functions we've imported the library with `/+  strandio`.

`strand-input` includes a bowl with things like `our` and `now` so we can get them with `strandio` functions like `get-our` and `get-time`.

`sleep` just sets a timer and waits for the specified amount of time.

Note how we've set the face and return type of the functions like `s=ship`, `t=@da` and `~`. You can see how `(pure:m !>([s t]))` at the end of the pipeline has access to the values returned by the previous functions.

# [Previous](1_thread-fundamentals.md) | [Next](3_success-and-failure.md)
