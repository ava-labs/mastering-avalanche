# How Avalanche works
With Avalanche, we’ve innovated at every level of blockchain networks. Foundationally, with a breakthrough in consensus protocols, and then continuing, layer-by-layer, to evolve areas like network and virtual machine models that hadn’t been explored enough.
Unlike other networks that force terms and conditions of network participation uniformly across the system, Avalanche empowers individuals and enterprises to easily create powerful, reliable, and secure applications and custom blockchain networks with complex rulesets or build on existing private or public networks that fit their use case.
Take, for example, a regulated financial institution wanting to issue digital assets. In a system where the institution cannot control which nodes validate their network activity, it’s not possible for them to do so compliantly. In Avalanche, the same institution can achieve complete control over the virtual machine running their use case and the conditions of running a node on their custom sub-network.
Avalanche employs a multi-chain framework with three blockchains that divide critical functions–and even employ different data structures–to give developers maximum flexibility and control over their applications.

First, there is the Exchange Chain (X-Chain). The X-Chain facilitates the creation and exchange of assets between individuals peer-to-peer, including Avalanche’s native token, AVAX. As opposed to a traditional blockchain where transactions are organized chronologically or by block height, Avalanche’s X-Chain is a directed acyclic graph (DAG).
DAGs link individual transactions to other transactions, rather than waiting for blocks of transactions to coalesce and be validated together. By optimizing for high volumes of transactions, DAGs have a significant advantage in scalability.
Second, there is the Contract Chain (C-Chain). The C-Chain is Avalanche’s default smart contract blockchain and a super-fast implementation of the Ethereum Virtual Machine. It is fully compatible with Solidity smart contracts and Ethereum tooling out of the box, so Ethereum developers can seamlessly port applications into the Avalanche ecosystem.
Whereas the X-Chain is a DAG, the C-Chain uses a modified version of Avalanche consensus that enables the traditional blockchain ordering required for smart contracts.
Finally, there is the Platform Chain (P-Chain). The P-Chain is responsible for staking, coordinating validators across networks, and creating custom subnets. Every Avalanche validator participates in staking on the P-Chain to help secure the core network, but these validators can then form dynamic or private sets of validators to operate subnets.
On these subnets, the validators have complete control over the data, economic model, virtual machine, and more. Meaning, a set of validators could port over virtual machines from any other blockchain network to effectively replace the underlying consensus mechanism, and optimize those networks’ performance and smart contract logic.

## Transactions
First, let's develop some intuition about the protocol. Imagine a room full of people trying to agree on what to get for lunch. Suppose it's a binary choice between pizza and barbecue. Some people might initially prefer pizza while others initially prefer barbecue. Ultimately, though, everyone's goal is to achieve consensus.
Everyone asks a random subset of the people in the room what their lunch preference is. If more than half say pizza, the person thinks, "Ok, looks like things are leaning toward pizza. I prefer pizza now." That is, they adopt the preference of the majority. Similarly, if a majority say barbecue, the person adopts barbecue as their preference.
Everyone repeats this process. Each round, more and more people have the same preference. This is because the more people that prefer an option, the more likely someone is to receive a majority reply and adopt that option as their preference. After enough rounds, they reach consensus and decide on one option, which everyone prefers.

It is almost like that in Avalanche: Validator takes a transaction request from an user, checks for is it a legal request. Then selects K people randomly from network and asks them "is it legal?". And repeat α (round size) times selecting validators and asking them their decisions. For every round, every validator that takes that transaction checks decisions: If β (majority) in the same decision, they will say "okay, 1 times we are (mostly) agree on what should we do". If they not, "we are not in the same decision, we have to go where we started". Lets see in an algorithm:

```
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
Everyone has an initial preference for pizza or barbecue. Until someone has decided, they query k people (the sample size) and ask them what they prefer. If α or more people give the same response, that response is adopted as the new preference. α is called the quorum size. If the new preference is the same as the old preference, the consecutiveSuccesses counter is incremented. If the new preference is different then the old preference, the consecutiveSucccesses counter to 1. If no response gets a quorum (an α majority of the same response) then the consecutiveSuccesses counter is set to 0.
Everyone repeats this until they get a quorum for the same response β times in a row. If one person decides pizza, then every other person following the protocol will eventually also decide on pizza.
Random changes in preference, caused by random sampling, cause a network preference for one choice, which begets more network preference for that choice until it becomes irreversible and then the nodes can decide.

In our example, there is a binary choice between pizza or barbecue, but Snowball can be adapted to achieve consensus on decisions with many possible choices.
The liveness and safety thresholds are parameterizable. As the quorum size, α, increases, the safety threshold increases, and the liveness threshold decreases. This means the network can tolerate more byzantine (deliberately incorrect, malicious) nodes and remain safe, meaning all nodes will eventually agree whether something is accepted or rejected. The liveness threshold is the number of malicious participants that can be tolerated before the protocol is unable to make progress.
These values, which are constants, are quite small on the Avalanche Network. The sample size, k, is 20. So when a node asks a group of nodes their opinion, it only queries 20 nodes out of the whole network. The quorum size, α, is 14. So if 14 or more nodes give the same response, that response is adopted as the querying node's preference. The decision threshold, β, is 20. A node decides on choice after receiving 20 consecutive quorum (α majority) responses.
Snowball is very scalable as the number of nodes on the network, n, increases. Regardless of the number of participants in the network, the number of consensus messages sent remains the same because in a given query, a node only queries 20 nodes, even if there are thousands of nodes in the network.

## Blocks/vertices
The implementation of the Avalanche consensus protocol by Ava Labs (namely in AvalancheGo) has some optimizations for latency and throughput. The most important optimization is the use of vertices. A vertex is like a block in a linear blockchain. It contains the hashes of its parents, and it contains a list of transactions. Vertices allow transactions to be batched and voted on in groups rather than one by one. The DAG is composed of vertices, and the protocol works very similar to how it's described above.
If a node receives a vote for a vertex, it counts as a vote for all the transactions in a vertex, and votes are applied transitively upward. A vertex is accepted when all the transactions which are in it are accepted. If a vertex contains a rejected transaction then it is rejected and all of its descendants are rejected. If a vertex is rejected, any valid transactions are re-issued into a new vertex which is not the child of a rejected vertex. New vertices are appended to preferred vertices.

## Validating

## The blockchain
