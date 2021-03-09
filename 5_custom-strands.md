# Custom Strands

So far we've mostly been looking at ready-made `strandio` functions. Here we'll look at writing our own.

Here's the simple thread from part 1, except the guts of it has been moved into its own arm `++ custom` in a core: 

```
/-  spider 
=,  strand=strand:spider 
=>
|%
++  custom
  |=  arg=vase
  =/  m  (strand ,vase)
  ^-  form:m
  (pure:m arg)
--
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m 
(custom arg)
```

You can also use `;<` `bind` pipelines and so forth. You don't have to use `pure` though, this is also a valid thread:

```
/-  spider 
=,  strand=strand:spider 
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m 
|=  strand-input:strand
[~ %done arg]
```

As previously discussed, a `strand` is a function from `strand-input:strand` to `output:strand`. So if we add a gate that takes `strand-input:strand` as its sample and compose a valid output then it works fine.

We can then add extra conditions:

```
/-  spider 
=,  strand=strand:spider 
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m 
|=  strand-input:strand
=/  umsg  !<  (unit @tas)  arg
?~  umsg
[~ %fail %no-arg ~]
=/  msg=@tas  u.umsg
?.  =(msg %foo)
[~ %fail %not-foo ~]
[~ %done arg]
```

So:

```
> -mythread
thread failed: %no-arg
> -mythread %bar
thread failed: %not-foo
> -mythread %foo
[~ %foo]
```

This is most useful when you give `strand-input` a face like `|=  tin=strand-input:strand` and then test `in.tin` for whatever pokes or signs you're expecting.

# [Previous](4_strand-output.md)

