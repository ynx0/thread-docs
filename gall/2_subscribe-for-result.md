# Subscribe for result

```
/+  default-agent, dbug
=*  card  card:agent:gall
%-  agent:dbug
^-  agent:gall
|_  =bowl:gall
+*  this      .
    def   ~(. (default-agent this %|) bowl)
::
++  on-init  on-init:def
++  on-save  on-save:def
++  on-load  on-load:def
++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  ?+    mark  (on-poke:def mark vase)
      %noun
    ?+    q.vase  (on-poke:def mark vase)
        (pair term term)
      =/  tid  `@ta`(cat 3 'thread_' (scot %uv (sham eny.bowl)))
      =/  ta-now  `@ta`(scot %da now.bowl)
      =/  start-args  [~ `tid p.q.vase !>(q.q.vase)]
      :_  this
      :~
        [%pass /thread/[ta-now] %agent [our.bowl %spider] %watch /thread-result/[tid]]
        [%pass /thread/[ta-now] %agent [our.bowl %spider] %poke %spider-start !>(start-args)]
      ==
    ==
  ==
++  on-watch  on-watch:def
++  on-leave  on-leave:def
++  on-peek   on-peek:def
++  on-agent
   |=  [=wire =sign:agent:gall]
   ^-  (quip card _this)
   ?+    -.wire  (on-agent:def wire sign)
       %thread
     ?+    -.sign  (on-agent:def wire sign)
         %poke-ack
       ?~  p.sign
         %-  (slog leaf+"Thread started successfully" ~)
         `this
       %-  (slog leaf+"Thread failed to start" u.p.sign)
       `this
     ::
         %fact
       ?+    p.cage.sign  (on-agent:def wire sign)
           %thread-fail
         =/  err  !<  (pair term tang)  q.cage.sign
         %-  (slog leaf+"Thread failed: {(trip p.err)}" q.err)
         `this
           %thread-done
         =/  res  (trip !<(term q.cage.sign))
         %-  (slog leaf+"Result: {res}" ~)
         `this
       ==
     ==
   ==
++  on-arvo   on-arvo:def
++  on-fail   on-fail:def
--
```

```
/-  spider 
=,  strand=strand:spider 
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m
|=  strand-input:strand
?+    q.arg  [~ %fail %not-foo ~]
    %foo
  [~ %done arg]
==
```

# [Previous](1_start-thread.md) | [Next](3_subscribe-for-facts.md)
## [Return Home](../index.md)