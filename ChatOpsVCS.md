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


## Tools needed 

Tools that need to be built to built in order to do this:

(Assuming that we're not rewriting slack and have a aslack instance that is handling auth and handing bot recieveng authorized and trusted messages of what user did what 
cant have auth groups in slack but can have who gets added to which slack groups


1. slackbot to detect code chunks
1. server for code chunks to be sent to by slackbot + engine to stitch them together into a codebase
1. second slack instance for devops for the first slack instance
"assuming we had a working system, what would the working sstem itself look like?"


ultimate osurece o authority is slack admin, cant be kept out of private channel
ultimate external source of truth, what webhook does slackbot post messages to from slckas end. 
the released version ofthe software... 
single row single oclumn database of whaich version ofthe thing are we using.... 

cname, vercel, nextup, several ppl have built i... concept of build code, push docke conrainer, genrates urlll.. laxily when hits ndoins spins upo docker container

lambda plus dns shenanigans /nmginx plugin
"someone is going to wite some lua about this"


http request -> read host header, colla host header, results in docker conainer spnu up... 
inthe infinite hotel of doors, standard delarive indrastucts... 

autoscaling required

guids
"I come from urbit, fan of perma names, put onto devops the compexity of that being true or affordable, which feels to me like its doable"



db group stable, 1mb limit per deploy, poke somethign to exceed


how do you do any kind of observability fo this system
scaling is auto, throughput is... 

so this is intended to be a rep[lacement for git]


the core of it is is... 
to put code into repo, make a chunj of code
microservice is channel
code block is a file
code block replying to a message contianing a code block is "delete that file and" (reply is "link to previous message" in slack)
a thread is a branch
or instead of replying, a message with the same filename, make the bot post a diff in teh message about... the url for... 
slack rate limits? not multiple bot messages than human messages? notifying of... 

slack backups to json regularly

code is nominally stored in slack, backed up to table... 

something grabs all the code from slack and 
grab most recent message in thread isntead of recalculating, cache, ? 
rom message deiverd column, first in cain of replues, first in column, what when is the mewssage posted... idex on tuple of that to get per message most recent. 
the more code you have, the longer it will take to reply
optimistic, less than 100mb code in system, perf is ok or at least solvable... 

more than 1100 mb of external depenxvies
git hustiry
git hisotry per line give up on
code gets approved by a certain number of thumbs up... 

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






