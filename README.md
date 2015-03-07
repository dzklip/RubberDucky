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
## The Programer's Axiom of Choice
* given multiple equally good ways to solve a problem, just pick one and get on with it.

The Customer wants many Servers to "replicate the state" of one Server — we let the Customer choose what that means.

We Choose to satisfy the Customer by delivering exactly the same Requests in exactly the same order to each and every Server — Guaranteed.

Let us assume we start with a bunch of Servers that are all in an initial state and considered "replicated" or "synchronized" by the Customer.

As soon as Rubber Ducky delivers one Request to one Server we are committed: that is the Request that must be processed next by each and every Server. However the first Server is free to process a second Request before any other Server has processed the first Request. We only guarantee eventual Replication.

## First Invariant: If Server S-5 processes Request N after Request M, then every Server that has processed Request M may only process Request N next.

We see that there must be a implicit or explicit ordered list of Requests which have been processed by any Server. We choose to make an explicit list which we call the Ledger. We can see that if two or more Servers have each processed all the listed Requests then it does not matter which of them processes the next Request. However multiple Requests may be waiting to be processed and it is not acceptable for each of two or more Servers to process different Requests next. We Choose to append to the Ledger an ordered list of not-yet-processed Requests. We see that if two different Servers each received a different Request and appended it to the Ledger, then the ordered list of not-yet-processed Requests could be different. Therefore We Choose to require that only one Server shall append Requests to the Ledger.

We Choose to designate a "Distinguished Leader" which is the first to receive any Client Request, and which appends that Request to the Ledger. The Distinguished Leader is then responsible for propagating changes to the Ledger to other Servers. If any other Server receives a Request, it must refer that Request to the Distinguished Leader.

We can see that the Distinguished Leader is not free to process the new Request, because if the Distinguished Leader falls ill then the other Servers may not even know that the new Request exists, and would therefore choose to process some other Request.

Almost the entire point of Rubber Ducky is to satisy its guarantees even when a Server becomes "partitioned" or otherwise unavailable. We Choose to define "unavailable" to mean "cannot engage in two-way communication with at least half of the Servers."


Therefore if the Distinguished Leader becomes unavailable it is neccesary to distinguish a new Leader. However when a new Leader is Distinguished it is possible 
