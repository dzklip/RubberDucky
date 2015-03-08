# RubberDucky
It's much better than a Raft because it is cute — and easy to understand

Rubber Ducky is a system that reliably replicates activity across a large number of servers despite the possibility of server failure. It enhances a Client-Server application by enabling the replication of state across all servers.

It is important to understand that Rubber Ducky (and Raft, etc.) enables multiple "replicated" or
"synchronized" Servers. Rubber Ducky does not enable a "distributed" multi-Server system. A Rubber Ducky system should have approximately the same throughput as the single-Server system it replaces, though it will certainly have greater latency. What Rubber Ducky can add is reliability and enhanced persistence.

It is also important to understand that as we will describe it, Rubber Ducky (and Raft, etc.) do require that most (more than half) of the Servers remain available or else no requests will be processed. Rubber Ducky does guarantee that if most of the servers are unavailable then no Requests will be processed.

## Rubber Ducky :: Raft :: Paxos

Rubber Ducky is a refactoring of the Raft system in
order to make it simple, understandable, and useful.
Therefore Rubber Ducky is a paper which describes a
protocol and a mechanism for handling communication within
that protocol. Rubber Ducky does not pretend to be
the system the Author would use to solve the same
problem: that system is called Sargasso Mat. Rubber
Ducky is rather a system which is obviously the same
as Raft, and obviously much better. Its simplicity
and clarity make it easier to understand its
limitations, and hence the limitations of Raft and
probably Paxos.

Paxos is a system similar to Raft which is ably denigrated in the Raft paper. Reducing their analysis to two salient points:
  * "Multi-Paxos" is not actually described in Lamport's Paper, so implementors are left to their own devices.
  * While the Paxos algorithm may be provably correct, any real-world implementation of Paxos must compromise the purity of the described protocol in so many ways that the proofs cannot speak to the correctness of the most faithful implementation.

Reducing my analysis of the limitations of the Raft Paper to two salient points:
  * The paper reads like a live-blog of extended design meetings. They describe a mechanism, then a couple meetings later they discover an issue and improvise a hack to work around that issue. Then hacks to work around the hacks.
  * The point of Rubber Ducky is to do what the Raft authors failed to do after all that hackery: Revisit and re-adjust goals, expectations, and definitions so that a simple protocol succeeds without hackery.

## Client :: Request :: Server :: Response

In a Client-Server system Clients make Requests of a
single Server, which returns a Response. A competent
Rubber Ducky implementation intercepts and
manipulates the delivery of Requests to multiple
Servers in such a manner as to ensure the reliable
replication of Activity. Rubber Ducky may be added to
an existing Client-Server system, or a Rubber Ducky
implementation may be co-developed with a new or
modified Client-Server system.

Most Client-Server systems which can transmit their
Requests over a network are compatible with Rubber
Ducky, but not all. Servers coupled with Rubber Ducky
must always produce the same Responses when given the
same sequence of Requests, not allowing any input
(such as clocks, weather, etc.) to affect their
Responses unless that input passes as a Request through
Rubber Ducky.

## Customer :: Implementor :: Author

There are three players in the Rubber Ducky
ecosystem. Each player makes certain guarantees. The
Author of Rubber Ducky guarantees that if the
Customer and Implementor hold up their parts of the
Contract, then Rubber Ducky will hold up its part.
The Implementor guarantees to faithfully implement
Rubber Ducky — protocol and mechanism —
without alteration or exception.

The Customer owns the Client-Server portion and is
thus the beneficiary of the reliably replicated
Activity which Rubber Ducky and the Implementor
Guarantee. The Customer must express their Requests
as arrays of bytes which Rubber Ducky refers to as
Quacks. These Quacks will be duplicated and
transmitted to multiple different Servers. The
Customer guarantees that a Quack duplicated and
delivered to another Server will still be the same
Request. The Customer is responsible for the
definition of "replicated" or
"synchronized" Servers. The Customer
guarantees that two Servers they considers
"synchronized" or "replicated", each
being given the same Request — whether
concurrently or at vastly different times — will,
after each has processed that Quack, again be
"synchronized" or "replicated". This
is subtly important.

In real world systems Servers have "state"
such as log messages and time stamps which will not
be replicated reliably by Rubber Ducky. In general
this should be okay, even desirable. The Customer
must not consider such timestamps when evaluating
whether a pair of Servers are "synchronized"
or "replicated". An example might be that
when a Server accepts a Request it records a
time-stamp. Such time-stamps will be different for
each Server. The Customer is free to disregard such
timestamps when evaluating whether two servers are
truly synchronized. Alternatively the Customer might
feel comfortable replacing that timestamp with one
included in the Request when the Request is issued.
Whatever is in the Request will be reliably
replicated by Rubber Ducky.

As another example, if the Customer is attempting to enhance a caching system with timed expiration then
obviously at a "vastly later time" some entries may have expired. The Customer is free to consider two Servers with differentially expired entries to be nonetheless "replicated". Alternatively the Customer could have a special "Clock Client" which sends "Time Update" Requests at regular intervals, and Servers could rely only on those "Time Update" Requests to process expirations.

As we see the Customer is entirely responsible for the meaning of "replicated" or "synchronized" state between two Servers. All Rubber Ducky guarantees is that every Server within Rubber Ducky will receive exactly the same Quacks in exactly the same order. This is what we mean by "replicated Activity".

## Rubber Ducky —vs— Raft & Paxos

Rubber Ducky enhances — adds value — to a Customer's Client-Server system. It is not a work of art to be admired for its own intrinsic beauty.

The Raft paper for instance makes no effort to separate the concerns of its replication from the concerns of the "state machines" which appear somehow related to be the target of their activity. There are bizarre forays into state-machine land such as the paragraphs about using unix "fork" to replicate the state machine's state but it is clear that the state machine within the Raft paper has no purpose except to be the state machine in the Raft paper. This is art for art's sake.

## Rubber Ducky, Raft, and Paxos —vs— Sargasso Mat

Rubber Ducky, Raft, and Paxos all center their activity on a "Distinguished Leader" who has many responsibilities. This creates a bottleneck to performance, and the replacement of a failing Leader is (at least for Raft and Paxos) a slow, complex, and fraught undertaking.

Rubber Ducky and Raft (at least) are Star networks — the Leader must communicate with all Followers. This means that the forward progress of the system is limited not by the input speed of the Leader, but by its output speed divided by half the number of followers. To be quite honest, other limitations of the protocols make it unlikely that even that limit will be reached.

Sargasso Mat is a far faster system and such limits are unacceptable.

## Queen Duck :: Ducklings

The Distinguished leader in Rubber Duckie is named "Queen Duck". Obviously she is followed by a small flotilla of Ducklings. Because sometimes the Queen is deposed and because her successor always takes the same name, a specific Queen Duck is always referred to by her number: "Queen Duck 8", "Queen Duck 9", etc.

# SUDDEN LURCH SIDEWAYS INTO DERIVATION FROM PRODUCT REQUIREMENTS

## Terminology: just so we don't confused
* The Customer owns Clients which make Requests which are represented as Messages (bytes) and Servers which process those Requests and perhaps deliver Responses.
* The Implementor owns Nodes which communicate with each other using RPCs. An RPC always requires a response.
* Nodes accept Requests and eventually deliver them to Servers, after communicating with each other using RPCs.

## The Programer's Axiom of Choice
* given multiple equally good ways to solve a problem, just pick one and get on with it.

The Customer wants many Servers to "replicate the state" of one Server — we let the Customer choose what that means.

We Choose to satisfy the Customer by delivering exactly the same Requests in exactly the same order to each and every Server — Guaranteed.

Let us assume we start with a bunch of Servers that are all in an initial state and considered "replicated" or "synchronized" by the Customer.

As soon as Rubber Ducky delivers the first Request to the first Server we are committed: that is the Request that must be processed first by each and every Server. However the first Server is free to process a second Request before any other Server has processed the first Request. We only guarantee eventual Replication.

### Invariant:
* If Server S processes Request N after Request M, then every Server that has processed Request M may only process Request N next.

We see that there must be an implicit or explicit ordered list of Requests which have been processed by any Server. We choose to make an explicit list which we call the Ledger. We can see that if two or more Servers have each processed all the listed Requests then it does not matter which of them processes the next Request. However multiple Requests may be waiting to be processed and it is not acceptable for each of two or more Servers to process different Requests next. We Choose to append to the Ledger an ordered list of not-yet-processed Requests. We see that if two different Nodes each received a different Request and appended it to the Ledger, then the ordered list of not-yet-processed Requests could be different. Therefore We Choose to require that only one Node shall append Requests to the Ledger.

We Choose to designate a "Distinguished Leader" which is the first to receive any Client Request, and which appends that Request to the Ledger. The Distinguished Leader is then responsible for propagating changes to the Ledger to other Nodes. If any other Node receives a Request, it must refer that Request to the Distinguished Leader.

We can see that the Distinguished Leader is not yet free to *process* the new Request, because if the Distinguished Leader becomes unavailable then the other Nodes may not even know that the new Request exists, and could therefore choose to process some other Request next.

Almost the entire point of Rubber Ducky is to satisfy its guarantees even when a Node becomes "partitioned" or otherwise unavailable. We Choose to define "unavailable" to mean "cannot engage in two-way communication with at least half of the other Nodes."

Therefore if the Distinguished Leader becomes unavailable it is neccesary to distinguish a new Leader. However when a new Leader is Distinguished it is possible that the old leader may as yet be unaware that it has been deposed. This means that both the Deposed Distinguished Leader and the New Distinguished Leader may be adding different Requests to their copies of the Ledger at the same time. Worse, it is possible that a few Nodes (but obviously fewer than half) are still following the Deposed Distinguished Leader and accepting its updates to the Ledger. This violates our rule that only one Node may make updates to the Ledger.

## To reiterate:
* Only a Distinguished Leader may append Requests to the Ledger.
* But a deposed Distinguished Leader might append invalid entries to a deposed Ledger.
* Therefore the presence of a Request in the Ledger is not enough to allow it to be processed.
* A Deposed Leader may rejoin the group and follow the New Leader while having invalid entries in its Ledger.
* Followers of a Deposed Leader may also have invalid entries in their Ledgers.
* The Distinguished Leader must be in communication with at least half of the other Nodes.

And we make an observation: If two Sets each contain a majority (*more* than half) of the Nodes, then there will be at least one Node that is in both Sets. Since a new Leader cannot be Distinguished unless it is in communication with at least half of the other Nodes, it follows that together they form a group containing a majority of the Nodes. Therefore any fact known to a majority of the Nodes will be known by at least one Node in communication with the Distinguished Leader (or known to the Distinguished Leader itself, of course). We choose not to let Nodes forget any important facts. These specifically include valid entries in the Ledger.

### Requirement:
* The Implementor must guarantee that certain facts (such as valid entries in the Ledger) are persistent even if the Node holding them crashes or power-cycles or whatever and recovers.

### Invariant:
* any Ledger Entries that are some point in time known to a majority of the Nodes will thereafter *always* be known to some Node within the majority of available Nodes which includes the Distinguished Leader.

### Requirement:
* There *MUST* be a known number of Nodes and the complete list of Nodes must be known to every Node.

Or it is impossible to known if we have a majority.

## About Processing Requests:
* Any valid Requests in the Ledger which are *KNOWN* to be *known* to a majority of the Nodes may safely be given to Servers to be processed in the specified order. Once they are known by a majority, we can guarantee that they will always be known by some Node in any future majority.

We Choose (or have already chosen by implication): valid Ledger entries always flow from the Distinguished Leader to followers and never in the other direction.

### Invariant:
* The New Distinguished Leader in any majority *SHALL BE* the Node with the Most Complete Ledger.

But we have not precisely defined "Most Complete Ledger" yet, since we know there are circumstances in which Ledger entries may be invalid: a Deposed Distinguished Leader and its followers may not yet realize that the Distinguished Leader has been deposed. We do know that such invalid entries will always be the most recent entries made by the previous leader. In fact, if we require such invalid entries to be purged before new entries are added, then they will always be the most recent entries of the most recent previous leader.

Because there is only one valid Distinguished Leader at any time, they form an ordered list and therefore it is possible to number the Distinguished Leaders. It is also obviously possible to number the Requests they have added into the Ledger. This means that a description of what should be in the Ledger can be reduced to a set of pairs of numbers: Distinguished Leader number and the number of that Distinguished Leader's last valid entry.

### Definition:
* The Ledger shall be a numbered list of Distinguished Leaders, and for each Leader a numbered list of their valid entries.

### Subtlety alert
* Do remember that valid entries includes all processed entries possibly followed by some unprocessed entries. The definition of valid is not "has been processed" or "can now be processed" even though it is true that every entry which has been processed must also be valid. The definition of "valid entry in the Ledger" is exactly "known to the current Distinguished Leader". This should be unsurprising since obviously no known-to-be-invalid entries were ever made in the Ledger.

### Requirement:
* before a follower accepts any new entry from a Distinguished Leader, it shall have purged all invalid entries from the most recent Distinguished Leader from which it had accepted entries AND it shall have accepted all previous valid entries which it had not yet accepted.

This means that any follower holding entries any from some Distinguished Leader must have all and only valid entries for all previous leaders.

### Invariant:
* The "Most Complete Ledger" is always the ledger with entries from the most recent Distinguished Leader and for that leader the one with the most entries.

It follows that the current Distinguished Leader always has the Most Complete Ledger.

What does it mean to be "in communication" with another Node?

### Definition:
* a Node is "unresponsive" when it fails to respond to an RPC from another Node within a time period considered Reasonable and Customary.

The Implementor defines "Reasonable and Customary" or allows the Customer to configure appropriate values. These values have no effect upon the guarantees made by Rubber Ducky but rather affect system behavior in the real world and should be tuned so as to provide an appropriate level of responsiveness to serious network partitions without causing thrashing due to minor communication delays.

