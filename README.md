# RubberDucky
It's much better than a Raft because it is cute and easy to understand

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
Responses which does not pass as a Request through
Rubber Ducky.

It is important to understand that Rubber Ducky (and Raft, etc.)
enables multiple "replicated" or
"synchronized" Servers. Rubber Ducky does not
enable a distributed multi-Server system. A Rubber
Ducky system should have approximately the same
throughput as the single-Server system it replaces, though it will certainly have greater latency.
What Rubber Ducky can add is reliability and enhanced
persistence.

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

The Distinguished leader in Rubber Duckie is named "Queen Duck". Obviously she is followed by a small flotilla of Ducklings. Because sometimes the Queen is deposed and her successor takes the same name, a specific Queen Duck is always referred to by her number: "Queen Duck 8", "Queen Duck 9", etc.
