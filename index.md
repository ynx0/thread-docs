# Thread Docs

## Thread basics

These docs walk through the fundamental things you need to know to write threads. They're focused on basic thread composition so don't touch on interacting with threads from gall agents and such. The included examples can all just be run from the dojo.

1. [Thread Fundamentals](thread-basics/1_thread-fundamentals.md)
   - Basic information and overview of threads, strands, `form` & `pure`. 
2. [Micgal and Bind](thread-basics/2_micgal-and-bind.md)
   - Covers using micgal and `bind` to chain strands.
3. [Strand Input](thread-basics/3_strand-input.md)
   - What strands receive as input
4. [Strand Output](thread-basics/4_strand-output.md)
   - What strands produce
5. [Custom Strands](thread-basics/5_custom-strands.md)
   - Notes on writing your own strands rather than just using ready-made ones.

## Gall

## How-tos

- [Grab some JSON from a URL](examples/get-json.md)
   - Here's an example of chaining a couple of external http requests for JSON.

- [Start a child thread](examples/start-thread-from-thread.md)
   - Starting and managing child threads.

- [Main Loop](examples/main-loop.md)
   - How to use `main-loop` in `strandio` to try an input against multiple functions.

## [Reference](reference.md)
Basic reference information. For usage of particular `strandio` functions just refer directly to `/lib/strandio/hoon` since they're largely self-explanatory.
