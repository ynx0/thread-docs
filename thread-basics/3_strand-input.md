# Strand Input

The input to a `strand` is defined in `/lib/strand/hoon` as:

```
+$  strand-input  [=bowl in=(unit input)]
```

## bowl

`bowl` is the following:

```
+$  bowl
  $:  our=ship
      src=ship
      tid=tid
      mom=(unit tid)
      wex=boat:gall
      sup=bitt:gall
      eny=@uvJ
      now=@da
      byk=beak
  ==
```

- `our` - our ship
- `src` - ship where input is coming from
- `tid` - ID of this thread
- `mom` - parent thread if this is a child thread
- `wex` - outgoing subscriptions
- `sup` - incoming subscriptions
- `eny` - entropy
- `now` - current datetime
- `byk` - `[p=ship q=desk r=case]` path prefix

There are a number of functions in `strandio` to access the `bowl` contents like `get-bowl`, `get-beak`, `get-time`, `get-our` and `get-entropy`.

You can also write a function with a gate whose sample is `strand-input:strand` and access the bowl that way like:

```
/-  spider
/+  strandio
=,  strand=strand:spider 
=>
|%
++  bowl-stuff
  =/  m  (strand ,[boat:gall bitt:gall])
  ^-  form:m
  |=  tin=strand-input:strand
  `[%done [wex.bowl.tin sup.bowl.tin]]
--
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m
;<  res=[boat:gall bitt:gall]  bind:m  bowl-stuff
(pure:m !>(res))
```

## input

`input` is defined in libstrand as:

```
+$  input
  $%  [%poke =cage]
      [%sign =wire =sign-arvo]
      [%agent =wire =sign:agent:gall]
      [%watch =path]
  ==
```

- `%poke` incoming poke
- `%sign` incoming sign from arvo
- `%agent` incoming sign from a gall agent
- `%watch` incoming subscription

Various functions in `strandio` will check `input` and conditionally do things based on its contents. For example, `sleep` sets a `behn` timer and then calls `take-wake` to wait for a `%wake` sign from behn:

```
++  take-wake
  |=  until=(unit @da)
  =/  m  (strand ,~)
  ^-  form:m
  |=  tin=strand-input:strand
  ?+  in.tin  `[%skip ~]
      ~  `[%wait ~]
      [~ %sign [%wait @ ~] %behn %wake *]
    ?.  |(?=(~ until) =(`u.until (slaw %da i.t.wire.u.in.tin)))
      `[%skip ~]
    ?~  error.sign-arvo.u.in.tin
      `[%done ~]
    `[%fail %timer-error u.error.sign-arvo.u.in.tin]
  ==
```

You can of course also write your own function to handle inputs.

# [Previous](2_micgal-and-bind.md) | [Next](4_strand-output.md)
## [Return Home](../index.md)
