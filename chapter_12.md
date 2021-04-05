# Avalanche Consensus Deep Dive

## Deeper Down The Rabbit Hole

Avalanche consensus is a radical breakthrough in distributed systems. It represents as large a leap forward as the classical and Nakamoto consensus protocols that came before it. Now that you have a better understanding of consensus let's take a much deeper look at how Avalanche Consensus works.

### Intuition

First, let's develop some intuition about the protocol. Imagine a room full of people trying to agree on what to get for lunch. Suppose it's a binary choice between pizza and barbecue. Some people might initially prefer pizza while others initially prefer barbecue. Ultimately, though, everyone's goal is to achieve consensus.

Everyone asks a random subset of the people in the room what their lunch preference is. If more than half say pizza, the person thinks, "Ok, looks like things are leaning toward pizza. I prefer pizza now." That is, they adopt the preference of the majority. Similarly, if a majority say barbecue, the person adopts barbecue as their preference.

Everyone repeats this process. Each round, more and more people have the same preference. This is because the more people that prefer an option, the more likely someone is to receive a majority reply and adopt that option as their preference. After enough rounds, they reach consensus and decide on one option, which everyone prefers.

### Snowball

The intuition above outlines the Snowball Algorithm, which is a building block of Avalanche consensus. Let's review the Snowball algorithm.

Parameters

* _n_: number of participants
* _k_ (sample size): between 1 and _n_
* α (quorum size): between 1 and _k_
* β (decision threshold): >= 1

### Algorithm

```text
preference := pizza
consecutiveSuccesses := 0
while not decided:
  ask k random people their preference
  if >= α give the same response:
    preference := response with >= α
    if preference == old preference:
      consecutiveSuccesses++
    else:
      consecutiveSuccesses = 1
  else:
    consecutiveSuccesses = 0
  if consecutiveSuccesses > β:
    decide(preference)
```

### Algorithm Explained

Everyone has an initial preference for pizza or barbecue. Until someone has _decided_, they query _k_ people \(the sample size\) and ask them what they prefer. If α or more people give the same response, that response is adopted as the new preference. α is called the _quorum size_. If the new preference is the same as the old preference, the `consecutiveSuccesses` counter is incremented. If the new preference is different then the old preference, the `consecutiveSucccesses` counter to `1`. If no response gets a quorum \(an α majority of the same response\) then the `consecutiveSuccesses` counter is set to `0`.

Everyone repeats this until they get a quorum for the same response β times in a row. If one person decides pizza, then every other person following the protocol will eventually also decide on pizza.

Random changes in preference, caused by random sampling, cause a network preference for one choice, which begets more network preference for that choice until it becomes irreversible and then the nodes can decide.

{% hint style="info" %}
For a great visualization, check out [this demo](https://tedyin.com/archive/snow-bft-demo/#/snow) from Ava Labs' Co-Founder Ted Yin.
{% endhint %}

In our example, there is a binary choice between pizza or barbecue, but Snowball can be adapted to achieve consensus on decisions with many possible choices.

The liveness and safety thresholds are parameterizable. As the quorum size, α, increases, the safety threshold increases, and the liveness threshold decreases. This means the network can tolerate more byzantine \(deliberately incorrect, malicious\) nodes and remain safe, meaning all nodes will eventually agree whether something is accepted or rejected. The liveness threshold is the number of malicious participants that can be tolerated before the protocol is unable to make progress.

These values, which are constants, are quite small on the Avalanche Network. The sample size, _k_, is `20`. So when a node asks a group of nodes their opinion, it only queries `20` nodes out of the whole network. The quorum size, α, is `14`. So if `14` or more nodes give the same response, that response is adopted as the querying node's preference. The decision threshold, β, is `20`. A node decides on choice after receiving `20` consecutive quorum \(α majority\) responses.

Snowball is very scalable as the number of nodes on the network, _n_, increases. Regardless of the number of participants in the network, the number of consensus messages sent remains the same because in a given query, a node only queries `20` nodes, even if there are thousands of nodes in the network.

## DAGs (Directed Acyclic Graphs)

Now let's introduce a data structure called a DAG or Directed Acyclic Graph. A DAG gives a **partial ordering** of decisions. For example, check out the DAG in this diagram:

<p style="text-align:center;"><img src="./assets/basic-dag.png" alt="Basic DAG" width="400px"></p>

**a** is before **b**. **b** is before **d**. **d** is before **e**. Transitively, we can say that **a** comes before **e**. However, since this is a partial ordering: for some elements, ordering is not defined. For example, both **b** and **c** are after **a** but there is no notion of whether **b** is before or after **c**.

Two additional DAG related concepts are **ancestors** and **descendants**. Ancestors are any nodes in the DAG which you can draw a line up to. For example, the ancestors of **d** are **a**, **b**, and **c**. The ancestors of **e** are **a**, **b**, **c**, and **d**. Descendants are the opposite of ancestors. The descendants of **a** are **b**, **c**, **d**, and **e**. The descendants of **b** are **d** and **e**.

Both Bitcoin and Ethereum, for example, have a linear chain where every block has one parent and one child. Avalanche uses a DAG to store data rather than a linear chain. Each element of the DAG may have multiple parents. The parent-child relationship in the DAG does not imply an application-level dependency.

In a consensus protocol, the name of the game is to prevent the inclusion of **conflicting transactions** into the DAG. Conflicts are application-defined. Different applications will have different notions about what it means for two transactions to conflict. For example, in a P2P payment system, transactions that consume the same UTXO \([Unspent Transaction Output](https://en.wikipedia.org/wiki/Unspent_transaction_output)\) would conflict. In Avalanche every transaction belongs to a **conflict set** which consists of conflicting transactions. Only one transaction in a conflict set can be included in the DAG. Each node **prefers** one transaction in a conflict set.

## Working Example

Suppose we have an Avalanche network running with the following parameters. The sample size, _k_, is `4`. The quorum size, α, is `3`. The number of consecutive success, β, is `4`.

<p style="text-align:center;"><img src="./assets/example-1.png" alt="Working example 1" width="400px"></p>

A node finds out about a new transaction **Y**. It queries the network based on the above parameters. It queries _k_ \(`4`\) validators and asks, "Do you prefer this transaction?" It gets back responses—three of them say **yes** and one of them says **no**. The quorum size, α, is `3` so there is an α majority \(quorum\) of yes responses. Now we the node updates its DAG.

<p style="text-align:center;"><img src="./assets/example-2.png" alt="Working example 2" width="400px"></p>

If a node gets an α majority response for a transaction then you give that transaction a **chit**, which is a boolean that says, "When I queried the network about this transaction, an α majority said that they preferred it." In our example, transaction Y gets a chit.

There is also a notion of **confidence**, which is the sum of a vertex's chit plus the sum of its descendants' chits. For example, transaction **V** has a chit. It also has three descendants which have a chit so its confidence is increased from `3` to `4`. Similarly, transactions **W** and **X** both have a chit and they both have a descendant with a chit, so they each have confidence `2`. Transaction Y has confidence `1`.

**Consecutive successes** are the same as in Snowball. It's the number of times that a transaction, or a descendant of the transaction, received a successful α majority query response. Previously, transaction V had `3` consecutive successes, itself and its two children, and now it has `4` consecutive successes with transaction Y. Similarly for transactions W and X.

<p style="text-align:center;"><img src="./assets/example-3.png" alt="Working example 3" width="400px"></p>

In this example we the acceptance threshold, β, is `4`. Transaction V has `4` consecutive success so it's **accepted**. This node is sure that every other correct node will eventually accept this transaction.

<p style="text-align:center;"><img src="./assets/example-4.png" alt="Working example 4" width="400px"></p>

Now suppose the node learns about transaction **Y'** which conflicts with transaction Y. It follows the same steps as before and subsamples _k_ \(`4`\) validators and asks if they prefer transaction Y'. In this case, two of them say that they prefer Y' and two of them say that they do not prefer Y'. This time there is no α majority response, and the DAG is updated accordingly.

<p style="text-align:center;"><img src="./assets/example-5.png" alt="Working example 5" width="400px"></p>

Transactions Y and Y' are in a conflict set; only one of them can ultimately get accepted. Transaction Y' doesn't get a chit because it didn't get an α majority response. It has confidence `0` because it doesn't have a chit and it doesn't have any descendants with a chit. It has `0` consecutive successes because the previous query didn't get an α majority response. W's consecutive success counter goes from `2` to `0`. Its confidence is still `2`.

When a node is asked whether it prefers a given transaction, it replies yes if that transaction has the highest confidence of any transaction in the transaction's conflict set. In this example, transaction Y has confidence `1` and transaction Y' has confidence `0` so the node prefer transaction Y to transaction Y'.

<p style="text-align:center;"><img src="./assets/example-6.png" alt="Working example 6" width="400px"></p>

Now the node learns about a new transaction, **Z**, and it does the same thing as before. It queries _k_ nodes, gets back an α majority response, and updates the DAG.

<p style="text-align:center;"><img src="./assets/example-7.png" alt="Working example 7" width="400px"></p>

Transaction Z gets a chit. It also has a confidence of `1` and `1` consecutive success. The processing ancestors are updated, too. No transactions have `4` consecutive successes so no ancestors are accepted.

## Vertices

Everything discussed to this point is how Avalanche is described in [the Avalanche whitepaper](https://assets-global.website-files.com/5d80307810123f5ffbb34d6e/6009805681b416f34dcae012_Avalanche%20Consensus%20Whitepaper.pdf). The implementation of the Avalanche consensus protocol by Ava Labs \(namely in AvalancheGo\) has some optimizations for latency and throughput. The most important optimization is the use of **vertices**. A vertex is like a block in a linear blockchain. It contains the hashes of its parents, and it contains a list of transactions. Vertices allow transactions to be batched and voted on in groups rather than one by one. The DAG is composed of vertices, and the protocol works very similar to how it's described above.

If a node receives a vote for a vertex, it counts as a vote for all the transactions in a vertex, and votes are applied transitively upward. A vertex is accepted when all the transactions which are in it are accepted. If a vertex contains a rejected transaction then it is rejected and all of its descendants are rejected. If a vertex is rejected, any valid transactions are re-issued into a new vertex which is not the child of a rejected vertex. New vertices are appended to preferred vertices.

## Finality

Avalanche consensus is probabilistically safe up to a safety threshold. That is, the probability that a correct node accepts a transaction that another correct node rejects can be made arbitrarily low by adjusting system parameters. In Nakamoto consensus protocol \(as used in Bitcoin and Ethereum, for example\), a block may be included in the chain but then be removed and not end up in the canonical chain. This means waiting an hour for transaction settlement. In Avalanche, acceptance/rejection are **final and irreversible** and take a few seconds.

## Optimizations

It's not efficient for nodes to just ask, "Do you prefer this vertex?" when they query validators. In Ava Labs' implementation, during a query a node asks, "Given that this vertex exists, which vertices do you prefer?" Instead of getting back a binary yes/no, the node receives the other node's preferred vertex set.

Nodes don't only query upon hearing of a new transaction. They repeatedly query until there are no virtuous vertices processing. A virtuous vertex is one that has no conflicts.

Nodes don't need to wait until they get all _k_ query responses before registering the outcome of a poll. If no transaction can get an α majority then there's no need to wait for the rest of the responses.

## Validators

If it were free to become a validator on the Avalanche network, that would be problematic because a malicious actor could start many, many nodes which would get queried very frequently. The malicious actor could make the node act badly and cause a safety or liveness failure. The validators, the nodes which are queried as part of consensus, have influence over the network. They have to pay for that influence with real-world value in order to prevent this kind of ballot stuffing. This idea of using real-world value to buy influence over the network is called Proof of Stake.

To become a validator, a node must **bond** \(stake\) something valuable \(**AVAX**\). The more AVAX a node bonds, the more often that node is queried by other nodes. When a node samples the network it's not uniformly random. Rather, it's weighted by stake amount. Nodes are incentivized to be validators because they get a reward if, while they validate, they're sufficiently correct and responsive.

Avalanche doesn't have slashing. If a node doesn't behave well while validating, such as giving incorrect responses or perhaps not responding at all, its stake is still returned in whole, but with no reward. As long as a sufficient portion of the bonded AVAX is held by correct nodes, then the network is safe, and is live for virtuous transactions.

## Big Ideas

Two big ideas in Avalanche are **subsampling** and **transitive voting**. Subsampling has low message overhead. It doesn't matter if there are twenty validators or two thousand validators; the number of consensus messages a node sends during a query remains constant.

<p style="text-align:center;"><img src="./assets/example-8.png" alt="Working example 8" width="400px"></p>

Transitive voting, where a vote for a vertex is a vote for all it's ancestors, helps with transaction throughput. Each vote is actually many votes in one. For example, in the above diagram, if a node gets a vote for vertex **D**, that implies a vote for all it's ancestors; a vote for **D** is also a vote for **A**, **B**, and **C**.

## Loose Ends

Transactions are created by users which call an API on the [AvalancheGo](https://github.com/ava-labs/avalanchego) full node or create them using a library such as [AvalancheJS](https://github.com/ava-labs/avalanchejs). Vertices are created when nodes batch incoming transactions together or when accepted transactions from a rejected vertex get reissued and added to the DAG. A vertex's parents are chosen from the virtuous frontier, which are the nodes at the tip of the DAG with no conflicts. It's important to build on virtuous vertices because if we built on non-virtuous vertices there would be a higher chance that the node would get rejected which means there's a higher chance it's ancestors get rejected and we would make less progress.

## Additional Observations

Conflicting transactions are not guaranteed to be live. That's not really a problem because if you don't want your transaction to not be live then don't issue a conflicting transaction.

Avalanche works for linear chains too. The protocol is largely the same as above, but each vertex has only have one parent. This gives a total ordering of vertices. This is useful for certain applications where one needs to know if a transaction came before another transaction, such as with smart contracts. Snowman is the name of Ava Labs' implementation of the Avalanche consensus protocol for linear chains.

If there are no undecided transactions, the Avalanche consensus protocol _quiesces_. That is, it does nothing if there is no work to be done. Avalanche is more sustainable than Proof-of-work where nodes need to constantly do work.

Avalanche has no leader. Any node can propose a transaction and any node that has staked AVAX can vote on every transaction, which makes the network more robust and decentralized.

## Why Is This Important?

Avalanche is a general consensus engine. It doesn't matter what type of application is put on top of it. The protocol allows the decoupling of the application layer from the consensus layer. If you're building a Dapp on Avalanche then you just need to define a few things, like how conflicts are defined and what is in a transaction. You don't need to worry about how nodes come to an agreement. The consensus protocol is a black box that put something into it and it comes back as accepted or rejected.

Avalanche can be used for all kinds of applications, not just P2P payment networks. Avalanche's Primary Network has an instance of the Ethereum Virtual Machine, which is backward compatible with existing Ethereum Dapps and dev tooling. The Ethereum consensus protocol has been replaced with Avalanche consensus to enable lower block latency and higher throughput.

Avalanche is very performant. It can process thousands of transactions per second with one to two second acceptance latency.