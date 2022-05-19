## What if your whole codebase were a whole bunch of slack messages?

## Why?

Because the dream of chatops is not just dead but has arisen from the deep with new friends

## What are we giving up on? / Recreating?

Git. Possibly also SQL.

## What would that even mean?


# Data(base) migrations within the nightmare

Adding a column should not need to be a migration, and adding a column is implicit when you refer to it. Deleing a column should not happen. Columns can be garbage collected if you can prove that no depolyed code is relying on this column. Columns only relied upon by code run run in the last 30 days can be removed. (But at no point are we undeploying any code, so garbage collection will never occur.)

For these reasons, this system does not support `select *` and making a column non-nullable is an actual constriaint on everyone else using the database. In other words, "renaming should be YOUR" problem i.e. names are permenant at a certain level- future code must be backwards compatible with respect to renaming.

Indexes should not live in the model i.e. should not live next to the fields, or in the code next to the fields- instead indices will be created the first time a query is performed using that field in the query parameters. Querying by a field will idempotently create or reuse an index, and then have an index retention policy where unused indices are quietly dropped (logging optional / see V2). However, the system will preemptively create an index whenever anything foreign keys into you

In addition- why not use a columnar store and then take no performance hit from these changes? (If you have no performance, taking a hit is meaningless!)


## Tools that need to be built to built in order to do this:

(Assuming that we're not rewriting slack and have a aslack instance that is handling auth and handing bot recieveng authorized and trusted messages of what user did what 
can't have auth groups in slack but can have who gets added to which slack groups

1. slackbot to detect code chunks posted in slack
1. server for code chunks to be sent to by slackbot + engine to stitch them together into a codebase
1. second slack instance for devops for the first slack instance?
1. server endpoint to serve the assembled codebase for viewing by an editor like VSCode
1. some way of running tests in CI i.e. subscribe to code changes somehow
1. deploying the code - deploy each version to its own mini instance (version built via CI into a docker container or similar)
1. system of record for which version is "prod" at any time ?


## Authentication and Authorization

In this system, the ultimate source of authority is the chat (slack) administrator(s). Administrators cannot be kept out of private channels. 

As for an ultimate external source of truth, that is - what webhook does slackbot post messages to? I.e. where does the code go - to where is the released version of the software... released? And again that can be configured by someone with permissions assigned by the slack admin. This could be a single row single column database of which version of the thing are we using.... 

### About "deploying" to "prod": 

CNAME record pointing at vercel or similar, using the concept of build code, push docker container, genrates url... lazily when hits it spins up a docker container - maybe via lambda plus DNS shenanigans / nginx plugin, "someone is going to wite some lua about this" Each URL will be a guid for global uniqueness

Prod data will have to be durable but for all other deploys, provision a minimal database with an upper limit of size, and swap in the prod database when a release is "promoted"


### How to use the working system

To put code into the "repository", make a chunk of code and post it in slack. Every slack channel is a microservice, and each code block is a file. To replace the contents of a file, start a thread against the original slack comment, and post a code block- this will replace the original. The slackbot should post a diff from the old code to the new code if you command it to- something like `bot diff` in your code message. A reply is a PR? TODO

To keep your codebase safe (since it is less likely that every developer will have an up to date copy maybe?), automate your slack backups - dump all the messages to json regularly. Maybe backed up to something like a database... (but the main code-complilation server will always have at the latest version, but it won't have history- the source of truth for code history is the messages in slack!)


Optimistically while there is not an enormous amount of code in the system, performance for assembling the codebase will be negligable or at least solvable

What about external dependencies? Well, we're not going to be able to vendor them- and posting a `yarn.lock` could be a little difficult, but `package.json` or similar will at least be reasonable

Code blocks gets approved for "merge" into main by a certain number of thumbs up... 

code block is a file
refactorig is fucked
is that different than usual

One of the common lints is- "don't make your files too long" right? So if the code block is longer than the slack message then... disallow it? xD
But also allow code as attachments for when lint is inevitably in need of circumvention
Alternatelyliit then ok for attachments
at a minimum need to attack CSVs into ... 
images on website that are not base64, svg only lol
brute force a dalle prompt that generated the hing reliably

important modern worksflow feature sthat will be missing:

you will want to export to git, git hulk import
lets you do PRs on github repo... delaring defeat, ways to not suck (was tha ton the roadmap???)
bug burndown chart of number of tig ocmman sstillin the sustem

vscode pull code from endpoint

if writing this in apl, huirtoy is simpler because line by line its character by characer every version of line that has existed, possible to visually scan thru, the problem with APL is () 
literate problem dicouerce, what characer to huser here, accessible when you click thru the line of code, "get what people were feeling man" already inherently columnar, tho getting... talking to a db might... 
can al talk to databases? it has some... it doesnt love sql pjilosphically btut here are some libraries for that...

the obvious alertante meme andser is build in prolog or datalog

what are some complicated db migrations? how can we plan for them? 


how come up with? chatops? node script was upstream of this, just ssh in... slack bots.. a different type of terminal... natural language emulation sofr sending reminders instead of sheell thermianl... slackbot let you ssh into prod and do things... let the world have a record of your sins, if you have  devops channel ... d you ned oher mahanism for code storage or just move intot his sytem... 






