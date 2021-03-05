# Validating and Consensus

## Currency creation

## Consensus

### What is Consensus?

Consensus is the means by which a series of independent voters (often called “validators”) come to an agreement on a decision. At a glance, the process sounds straightforward: figure out if a piece of data is consistent with the rules of their system and whether the rest of the network agrees with their assessment.

Consensus protocols (like Avalanche) facilitate agreement between all of the validators, ensuring that the network has a synchronized view on the data. Robust consensus protocols work even if some validators in the network are faulty or malicious.

At the end of this process, all nodes will share the same data, referred to as “state”, that was agreed on in this decision-making process. If a conflicting transaction goes through on any node and it’s not in full agreement with the rest of the network, this is called a “safety violation.” This means that at least one machine is inconsistent with the other machines in the network. The purpose of consensus protocols is to minimize the chance safety violations on the network, preferably to the point where it’s incredibly unlikely.

Simple enough concept, right? Yet the topic of _consensus protocols_ is one of the hardest areas in computer science.

### History of Consensus

Over the 45-year history of distributed systems, there have been only three approaches to the consensus problem: Classical, Nakamoto, and Avalanche. Let’s discuss these three approaches, to illustrate what sets them apart from each other and determine their respective strengths and weaknesses.

#### Classical Protocols

Classical consensus protocols, such as Practical Byzantine Fault Tolerance (PBFT) and HotStuff, are based on all-to-all voting. This means that a validator needs to hear from a vast set of nodes that comprise the network to come to a decision. In addition, they are “probability of 1”, or P=1, protocols, which means that the network agrees on a decision with absolute certainty. Transactions finalize immediately after a node has received responses from the requisite fraction of the nodes that comprise the system.

Classical consensus has two major problems in open, Internet-like settings. The first problem is that these protocols are fragile: their correctness depends heavily on everyone in the system knowing and agreeing on the identities of the validators that comprise the system at any one point. As a result, any errors in maintaining membership in the system, or any difference in the views of the network can lead to safety violations. Further, an attacker only needs control of 33% of the network in order to launch a double-spend attack that is guaranteed to succeed.

Second, these systems, while fast, do not scale well in the number of participants. The most scalable Classical protocol, HotStuff (incidentally designed by Ted Yin, who is now working on the Avalanche protocol) used by Facebook’s Libra, only supports approximately 100 validators before the performance begins to suffer.

These two flaws make Classical consensus an unviable candidate for open and permissionless networks where nodes may join and leave at-will and nodes may behave adversarially to the network’s intent.

#### Nakamoto Consensus

For over a decade, practical Byzantine fault-tolerant systems were a novelty because Classical consensus couldn’t scale to handle global needs. Then, out of nowhere, Satoshi Nakamoto came along and dropped the Bitcoin paper and showed the world it was possible to create an adversary-resistant consensus protocol that worked at a global scale. Nakamoto accomplished this by redefining the consensus problem and making the correctness definition probabilistic.

With Nakamoto-based protocols, instead of waiting for absolute certainty across all nodes in the network, Nakamoto trades off an indistinguishable difference in probability for greater scalability. Where classical protocols must reach consensus with a probability of 1 (P=1), Nakamoto reaches consensus with a probability of 1 minus some teeny tiny chance of error (P=1 — ε). In Nakamoto, this error value is made smaller and smaller over time as more blocks are produced. The more blocks, the chances of a reorganization drop exponentially.

Nakamoto is undeniably a breakthrough as a robust, global protocol, but it does have drawbacks. It’s slow, consumes a lot of energy, and takes a very long time to feel confident in a transaction’s finality. While those are acceptable downsides for an asset that moves infrequently or acts as a reserve, they are too much of a burden for use cases like peer-to-peer payments and decentralized finance.

#### Avalanche

In the wake of Nakamoto’s work, the world still wanted a protocol with all of the benefits of Nakamoto consensus (robustness, scale, decentralization) and all the benefits of Classical consensus (speed, quick finality, and energy efficiency). Crypto’s own version of the proverbial, “have your cake and eat it too.”

Then, in May of 2018, a paper was distributed by a pseudonymous group named Team Rocket that proposed a third class of consensus protocol they dubbed “Avalanche.” With all the benefits of Classical consensus and the massive decentralization of Nakamoto consensus, it proved that Classical protocols can be generalized to behave probabilistically and gain massive performance improvements as a result.

<img src="./assets/ConsensusParadigmComparison.png" alt="Consensus Paradigm Comparison" width="700px">

#### What is Avalanche Consensus?

Avalanche consensus, like Nakamoto consensus, is a probabilistic protocol. Just as Nakamoto traded off a small chance in probability for performance, Avalanche embraces probability to make the chance of error just as microscopic (and better yet, like all parts of Avalanche, configurable by validators on custom subnets).

Avalanche can make the error so small that it is even less likely that a safety violation will occur on an Avalanche node than the odds of finding a SHA-256 hash collision. To put this in greater perspective, it’s dozens of orders of magnitude more likely that a life-ending asteroid will collide with the Earth in the next hundred years than a SHA-256 collision is detected in the next thousand years by a network computing 1 quintillion hashes a second. That’s really safe!

Avalanche also finalizes all transactions immediately, without waiting for confirmations. Avalanche is able to do so because it’s a generalization over Classical consensus, so it inherits this very desirable property. In fact, Avalanche sees transactions finalize in less than a second on average. This is blazingly fast compared to existing decentralized networks.

As a generalized Classical consensus with probabilistic models factored in, Avalanche also has the property of being both CPU-bound and high-throughput. It doesn’t require specialized (read: expensive) hardware to reach high-performance numbers in excess of 4,500 transactions per second. That means the computer you already have (or even the one collecting dust in your closet) is powerful enough to run Avalanche. The combination makes Avalanche nodes both extremely green and economical.

Better yet, like Nakamoto consensus, there’s no known cap on how many individuals can participate in the network at its deepest levels — the same can’t be said for Classical protocols whose performance degrades exponentially with greater participation (more in the advanced note below). For all the talk about making these networks accessible to the masses, Avalanche is peerless in this regard.

Avalanche does not depend on Proof of Work like Nakamoto consensus. In protocols like Bitcoin, Proof of Work is necessary for both organization and security against dishonest actors. Avalanche could use Proof of Work but chose Proof of Stake to force users to have some amount of tokens before being allowed to vote on transactions.

Finally, unlike Bitcoin and other systems based on Nakamoto’s work that requires perpetual action, Avalanche nodes only work when there is work to be done. There’s no mining or polling to get new blocks. Transactions are broadcast to the wider network, who then hears them and begins voting. If there are no transactions to vote on, the nodes in the network don’t do anything except listen until new transactions are heard. Simply put, Avalanche works smarter, not harder.

_Advanced note: The most important property of Avalanche, which really sets it far apart from existing Classical protocols, is that like Nakamoto consensus, it can operate with no known upper-bound of participants in the network. Decisions in Avalanche can be made in O(1) (constant number) of messages per node. Compare this with Classical protocols which take O(n²) messages to reach consensus and the network scaling issues that faced Classical go away in Avalanche._

<img src="./assets/HowAvalancheConsensusWorks.png" alt="How Avalanche Consensus Works" width="700px">
