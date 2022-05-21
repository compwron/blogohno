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


// L: story1: build obvious version of unify - take two arguments and compare them without leaking them. 
// deep unification- two things, tails match, rewrite tail to point to the other, can't drop the whole thing because someone else is hanging onto the whole thing, but parts of them need to be deduplicated.
// TODO: what dedupe guarantees does the unify operator actually provide? 
// TODO: separate ewpic for accurate vs performant nock interpreter. 
// L: unify story2: what to do about hash memoization (in urbit, check for equlity is amortized fast,
// q1: want to do hashing? -> story: create contrived test case that without hashing is too slow. Is it a test case we are about?
// q2: the name unify - the pair of test cases for that- create either two copies of a large byte buffer and unify them and assert
// that the total memory in use is less than 2 of that byte buffer... or create 2 copies of a deeply nested tree structure and assert that the pointer you get back out is... aaa, nvm
//  a way this can go wrong- it frees something that something else is pointing into because you messed up reference counts- this should be a test for unify.
// 3rd consideration- false positives, false negatives? subcomponent- pointer to compiled code is different from just raw code? Or ...
//  data structure optimization should not-
// need optimized equality algos for things represented in clever ways (that aren't built yet) - i.e. bit streams -
// - that are represented as sequence of bytes with a message on the pointer saying- only go one at a time - want to add that. 32 bitwise comaprison for byte streams, not byte by byte plz.
// leave a t
// TODO detect when things are oif the same optimized representation type and detect when it has an equality special case
// TODO add bit streams - and then need equality for them