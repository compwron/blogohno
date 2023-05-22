---
layout: post
title:  "Saga"
date:   2023-05-21 15:31:25 -0700
categories: jekyll update
permalink: saga
---

epic
- flow / user journey snippet
	- story == card
		- - subtask / acceptance criteria? 

top: clone urbit
chatapp is the ui

epics:
1. accurately implement nock
- story1: build obvious version of unify-as-isEqual - take two arguments and compare them without leaking them. No perfenhancements, no memory freeing.
- story2: atoms
1.5. namely it passes all the standard library tests in finite time
1.100 any performance enhancements at all to nock (equality frees memory)
- for the function misleadingly named unify
2. ui: conform to urbit-worker interface / porcelin API-alike. (communication protocol btw manager and worker. unix pipe, nouns go in, nouns go out. manager does the rest. IFTT-ish)
- find specs on interface / write tests that the current one passes...
https://web.archive.org/web/20200403203553/https://deno.land/
3. persist state (of nock having saved acked packets)
4. writeahead log (rebuild a "noun" (node) from persisted state owned by that noun)

ideal
- upstream adoption
- prove nock is actually determinstic (aot theoretically determinstic - qa canonical interpreter by making different mistakes)
- have the canonical interpreter and this interpreter to talk to each other and handoff to each other during execution by seriealizing the stack, send it to the other one, execute more instructions, send it back... as a way of generaitng more test cases effec3tivey.. (as a way of finding either of them introduce transient errors in execution that other mistakes cancel out) is this happening? It would be fun to say that you've proved that it's not! 
"Has this code that I havent written yet going to have any bugs? well..."

