# Offline scavenge Instructions

WARNING: do not run on the database of a running node

command line help: Run offlinescavenger --help

## Configuration

All the options are the same as the regular database options, with the addition of

- --scavenge-threads :  The number of threads to use for scavenging.
- --start-scavenge-from-chunk :  The chunk number to start the scavenge at.

Configuration can be done using file, environment variables and command line in the same way as EventStoreDB

## Running the tool

Do NOT run on the database of a running node

Do run on a copy of the database (including index) to minimise node downtime.

- Run ./offlinescavenger --whatif to check the configuration.
- Run ./offlinescavenger

While its running, look to see
* if the chunks produced are much reduced in size.
* if the rate of progress is acceptable

After it is finished,  run sanity check that the number of chunks produced is as expected.
Compare overall size of the database before/after.

## Checking the offline scavenge

We now have a scavenged database.
Take a _copy_ of the produced file to start a new ES node, verify that the results are as expected .
Perform any necessary checks to ensure the database contains the data needed and is suitable for production.

## Upgrading the production cluster
Copy the _original_ scavenged database (not the copy used for testing as it may contains additional data).

Select a follower node,

- copy the scavenged database in a seperate directory
- stop the node
- swap directories
- start the node
- wait for the node to join the cluster and catch up.
- roll out to other nodes

## Considerations & Rollback

Consider comparing the performance of the scavenged node against the other nodes.

Consider waiting for some time before scavenging other nodes

Take a backup of the database before restoring the scavenged database
or keep at least one node un-scavenged for a certain period of time.
