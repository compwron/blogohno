epic
- flow / user journey snippet
	- story == card
		- - subtask / acceptance criteria? 

top: clone urbit
chatapp is the ui

epics:
1. accurately implement nock
2. ui: conform to urbit-worker interface / porcelin API-alike. (communication protocol btw manager and worker. unix pipe, nouns go in, nouns go out. manager does the rest. IFTT-ish)
- find specs on interface / write tests that the current one passes...
https://web.archive.org/web/20200403203553/https://deno.land/
3. persist state (of nock having saved acked packets)
4. writeahead log (rebuild a "noun" (node) from persisted state owned by that noun)




