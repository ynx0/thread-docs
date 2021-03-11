# main-loop

`main-loop` is a useful function included in `strandio` that creates a loop and, importantly, lets you try the same input against multiple functions until one succeeds.

`main-loop` works with another function called `handle`. Handle converts a `%skip` into a `[fail %ignore ~]`. When `main-loop` sees a `[fail %ignore ~]` it tries the next function in its list with the same input.

## Example

Here are two files: `tester.hoon` and `tested.hoon`. Save them both to `/ted`, `|commit %home` and run `-tester`. You should see:

```
> -tester
baz
~
```

### tester.hoon
```
/-  spider
/+  *strandio
=,  strand=strand:spider
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m
;<  tid=tid:spider   bind:m  (start-thread %tested)
;<  our=ship         bind:m  get-our
;<  ~                bind:m  %-  poke
                             :-  [our %spider]
                             [%spider-input !>([tid `cage`[%baz !>("baz")]])]
(pure:m !>(~))
```
### tested.hoon

```
/-  spider
/+  *strandio
=,  strand=strand:spider
=>
|%
++  looper
  =/  m  (strand ,~)
  ^-  form:m
  %-  (main-loop ,~)
  :~  |=  ~
      ^-  form:m
      ;<  =vase  bind:m  ((handle ,vase) (take-poke %foo))
      =/  msg=tape  !<(tape vase)
      %-  (slog leaf+"{msg}" ~)
      (pure:m ~)
  ::
      |=  ~
      ^-  form:m
      ;<  =vase  bind:m  ((handle ,vase) (take-poke %bar))
      =/  msg=tape  !<(tape vase)
      %-  (slog leaf+"{msg}" ~)
      (pure:m ~)
  ::
      |=  ~
      ^-  form:m
      ;<  =vase  bind:m  ((handle ,vase) (take-poke %baz))
      =/  msg=tape  !<(tape vase)
      %-  (slog leaf+"{msg}" ~)
      (pure:m ~)
  ==
--
^-  thread:spider 
|=  arg=vase
=/  m  (strand ,vase) 
^-  form:m
;<  ~  bind:m  looper
(pure:m !>(~))
```

The first thread (tester.hoon) just starts the second thread and pokes it with a vase containing `"baz"` and the mark `%baz`.

The second thread (tested.hoon) has a `main-loop` with a list of three `take-poke` strands. As you can see it's the third one expecting a mark of `%baz` but yet it still successfully prints the message. This is because it tried the previous two which each saw the wrong mark and said `%skip`.

Notice we've wrapped the `take-poke`s in `handle` to convert the `%skip`s into `[%fail %ignore ~]`s that `main-loop` can handle.

## [Return Home](../index.md)
