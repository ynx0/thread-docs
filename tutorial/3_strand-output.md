# Strand Output

A strand produces a `[(list card) <response>]`. The first part is a list of cards to be sent off immediately, and `<response>` is one of:

- `[%wait ~]`
- `[%skip ~]`
- `[%cont self=(strand-form-raw a)]`
- `[%fail err=(pair term tang)]`
- `[%done value=a]`

Whose meanings are:

- **wait:** don't move on, stay here.  The next sign should come back to this same callback.
- **skip:** didn't expect this input; drop it down to be handled elsewhere
- **cont:** continue computation with new callback.
- **fail:** abort computation; don't send effects
- **done:** finish computation; send effects

So if you feed `2 2` into the following strand:

```
  |=  [a=@ud b=@ud]
  =/  m  (strand ,@ud) 
  ^-  form:m
  (pure:m (add a b))
```

It won't just produce `4`, but rather `[~ %done 4]`.

Note that spider doesn't actually return these codes to thread subscribers, they're only used internally to manage the flow of the thread.

```
/-  spider
/+  strandio
=,  strand=strand:spider
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m
=/  ures  !<  (unit path)  arg
?~  ures
  (strand-fail:strandio %no-arg ~)
;<  our=ship  bind:m  get-our:strandio
;<  now=@da   bind:m  get-time:strandio
;<  hmm=?     bind:m  (check-for-file:strandio [our %home [%da now]] `path`u.ures)
?.  hmm
(strand-fail:strandio %no-file ~)
(pure:m !>('exists'))
```
