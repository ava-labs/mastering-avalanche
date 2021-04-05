# Avalanche Consensus

## What is Avalanche Consensus?

Avalanche consensus, like Nakamoto consensus, is a probabilistic protocol. Just as Nakamoto traded off a small chance in probability for performance, Avalanche embraces probability to make the chance of error just as microscopic (and better yet, like all parts of Avalanche, configurable by validators on custom subnets).

Avalanche can make the error so small that it is even less likely that a safety violation will occur on an Avalanche node than the odds of finding a SHA-256 hash collision. To put this in greater perspective, it’s dozens of orders of magnitude more likely that a life-ending asteroid will collide with the Earth in the next hundred years than a SHA-256 collision is detected in the next thousand years by a network computing 1 quintillion hashes a second. That’s really safe!

Avalanche also finalizes all transactions immediately, without waiting for confirmations. Avalanche is able to do so because it’s a generalization over Classical consensus, so it inherits this very desirable property. In fact, Avalanche sees transactions finalize in less than a second on average. This is blazingly fast compared to existing decentralized networks.

As a generalized Classical consensus with probabilistic models factored in, Avalanche also has the property of being both CPU-bound and high-throughput. It doesn’t require specialized (read: expensive) hardware to reach high-performance numbers in excess of 4,500 transactions per second. That means the computer you already have (or even the one collecting dust in your closet) is powerful enough to run Avalanche. The combination makes Avalanche nodes both extremely green and economical.

Better yet, like Nakamoto consensus, there’s no known cap on how many individuals can participate in the network at its deepest levels — the same can’t be said for Classical protocols whose performance degrades exponentially with greater participation (more in the advanced note below). For all the talk about making these networks accessible to the masses, Avalanche is peerless in this regard.

Avalanche does not depend on Proof of Work like Nakamoto consensus. In protocols like Bitcoin, Proof of Work is necessary for both organization and security against dishonest actors. Avalanche could use Proof of Work but chose Proof of Stake to force users to have some amount of tokens before being allowed to vote on transactions.

Finally, unlike Bitcoin and other systems based on Nakamoto’s work that requires perpetual action, Avalanche nodes only work when there is work to be done. There’s no mining or polling to get new blocks. Transactions are broadcast to the wider network, who then hears them and begins voting. If there are no transactions to vote on, the nodes in the network don’t do anything except listen until new transactions are heard. Simply put, Avalanche works smarter, not harder.

_Advanced note: The most important property of Avalanche, which really sets it far apart from existing Classical protocols, is that like Nakamoto consensus, it can operate with no known upper-bound of participants in the network. Decisions in Avalanche can be made in O(1) (constant number) of messages per node. Compare this with Classical protocols which take O(n²) messages to reach consensus and the network scaling issues that faced Classical go away in Avalanche._

## How does Avalanche Consensus work?

To start, let’s talk about what a validator does in Avalanche consensus. Avalanche is a voting protocol. Validators listen for transactions. When they hear a transaction, they vote on whether a transaction is to be “Accepted”, and if it is not, it is marked “Rejected”. Transactions that appear virtuous will be voted on as “Accepted” by a validator. If it is conflicting, the transaction will be voted on as “Rejected”. That’s the sum of it. A validator sees a decision, makes an initial decision, and then collaborates with the rest of the network to determine whether the network agrees with this decision. This is the same expectation in Classical voting protocols, but in Avalanche this happens on much higher validator counts.

This voting process in Avalanche is what makes it so unique. Each validator is a completely independent decision-maker. There is no leader election. However, by protocol, each node uses the same process to determine whether a decision is virtuous and how likely they are in consensus with the rest of the network. Once they see a high probability of agreement across the network, the node locks in their vote and accepts a transaction as final.

The process used to determine whether a transaction is preferred and that the rest of the network agrees on this decision is referred to as “repeated random subsampling”.

At a high-level, this means a validator randomly selects other validators to ask them what they like. It does this over and over again on new randomly selected nodes until it builds up enough data to determine that its probability of being correct is so high you may as well consider it impossible that it’s wrong.

In more detail, it works as follows:

1. A validator is presented with a set of transactions that have been issued and asked to decide which transactions to “Accept.”
2. The node client presents the virtual machines (“VMs”) with their transactions, and the VMs will provide information to help the client determine which transactions are acceptable.
3. The validator selects a subset of these transactions that do not conflict, marks them as preferred, and attempts to accept them over the network.
4. Any nodes that query this validator receives its latest preferred choice for this decision.
5. This validator selects K nodes from the entire validator list (probability of selection is weighted by stake amount) to query for their preferred decision.
6. Each queried validator responds with their preferred decisions, the validator’s votes are updated accordingly, and confidence is built.
7. Meanwhile, other nodes are also selecting random validators from the validator set to be queried for their preferred response and updating their answers accordingly.
8. This continues for at least M rounds or until a sufficient confidence threshold is reached, whatever comes last, with K validators selected randomly each round (without replacement).
9. Once a confidence threshold is reached, the decision on the transaction is locked in as final.
10. If “Accepted”, the transaction is sent to the VM to be handled; if “Rejected”, the transaction is removed from consensus.

<p style="text-align:center;"><img src="./assets/consensus-flow.png" alt="How Avalanche Consensus Works" width="700px"></p>

The Team Rocket whitepaper showed that with correct parameterization, a network will come to the same decision using this process to a parameterizable probability.

## Probabilistic Finality

There’s a need by scientists to have certainty in their models. It’s very nice and neat to be able to say, “this is absolutely true beyond a shadow of a doubt,” when describing a system or process. Classical protocols strive toward this idyllic model, but in the real world, nothing is certain.

With 100 machines in a Classical network, there’s a chance that 33% of them could all go offline at once or that someone socially takes over 33% of them and tries to impose their will on the network. There’s also a non-zero probability that all the oxygen in a room will jump to the other side through random collisions and cause anyone misfortunate enough to be on the wrong side to suffocate in a pool of nitrogen. It’s possible, but the probabilities are so low as to not be a concern for anyone.

Nakamoto continues to demonstrate to the world that probabilistic finality is acceptable, as long as the chance of a safety failure is so astronomically remote that if one occurs it would be more fascinating than the network having a little hiccup for a bit. The world finds that as an acceptable service level. Heck, that’s way better than five nines (99.999%) that you get with carrier-grade SLAs.

Avalanche, by accepting this same minor margin of error, is accepting the chance of error once in 20,000 years in the model with the right parameters. It’s more likely that aging internet infrastructure causes massive network outages. It was this key insight that helped Team Rocket to pave the way for a new consensus protocol which scales tremendously better than existing Classical networks.