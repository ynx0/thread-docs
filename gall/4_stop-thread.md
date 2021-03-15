# Stop a thread

Here we've added one last card to `on-poke` to stop the thread and a little extra to `on-agent` to print things for demonstrative purposes.

#### thread-starter.hoon

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
        [%pass /thread/updates/[ta-now] %agent [our.bowl %spider] %watch /thread/[tid]/updates]
        [%pass /thread-stop/[ta-now] %agent [our.bowl %spider] %poke %spider-stop !>([tid %.y])]
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
         ?:  =(q.cage.sign *vase)
           %-  (slog leaf+"Thread cancelled nicely" ~)
         `this
         =/  res  (trip !<(term q.cage.sign))
         %-  (slog leaf+"Result: {res}" ~)
         `this
           %update
         =/  msg  !<  tape  q.cage.sign
         %-  (slog leaf+msg ~)
         `this
       ==
     ==
       %thread-stop
     ?+    -.sign  (on-agent:def wire sign)
         %poke-ack
       ?~  p.sign
         %-  (slog leaf+"Thread cancelled successfully" ~)
         `this
       %-  (slog leaf+"Thread failed to stop" u.p.sign)
       `this
     ==
   ==
++  on-arvo   on-arvo:def
++  on-fail   on-fail:def
--
```

We've also added a `sleep` to the thread to keep it running for demonstration.

#### test-thread.hoon

```
/-  spider 
/+  *strandio
=,  strand=strand:spider 
^-  thread:spider 
|=  arg=vase 
=/  m  (strand ,vase) 
^-  form:m
;<  =path   bind:m  take-watch
;<  ~       bind:m  (send-raw-card [%give %fact ~[path] %update !>("message 1")])
;<  ~       bind:m  %-  send-raw-cards
                    :~  [%give %fact ~[path] %update !>("message 2")]
                        [%give %fact ~[path] %update !>("message 3")]
                        [%give %fact ~[path] %update !>("message 4")]
                    ==
;<  ~       bind:m  (send-raw-card [%give %kick ~[path] ~])
;<  ~       bind:m  (sleep ~m1)
|=  strand-input:strand
?+    q.arg  [~ %fail %not-foo ~]
    %foo
  [~ %done arg]
==
```

Save these, `|commit`, and run with `:thread-starter [%test-thread %foo]`. You should see:

```
Thread started successfully
message 1
message 2
message 3
message 4
Thread cancelled successfully
Thread cancelled nicely
```

Now, try changing the vase in our new card from `!>([tid %.y])` to `!>([tid %.n])`. Save, `|commit`, and run again. You should see:

```
Thread started successfully
message 1
message 2
message 3
message 4
Thread cancelled successfully
Thread failed: cancelled
```

## Analysis

# [Previous](3_subscribe-for-facts.md)
## [Return Home](../index.md)
