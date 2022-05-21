epic
- flow / user journey snippet
	- story == card
		- - subtask / acceptance criteria? 

top: clone urbit
chatapp is the ui

epics:
1. accurately implement nock
1.5. namely it passes all the standard library tests in finite time
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



tags:
0 - atom
10 - backreferernce
11 - cell

atom:
0. 01 0
1. 01 1
2. 0010 10
3. 0010 11
4. 0011 100
5. 0011 101
6. 0011 110
7. 0011 111
8. 000100 1000
9. 000101 1001
...

cell: concat
11 010 011 [0 1]
11 010 11 010 11 010 11 010 [0 0 0 0]

backreference: atom for index
 2-v           "2"
11 11 011 011 1001010   [[1 1] [1 1]]


[0 0]


[[1 2] [3 4] [1 2]]
