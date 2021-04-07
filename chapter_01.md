# Introduction

## What is Avalanche?

Avalanche is the official name of the Distributed Ledger Technology (DLT) that is being built as an open-source project led by [Ava Labs](https://www.avalabs.org). Avalanche is often referred to as a Layer 1 (L1) network or simply as a 'Blockchain'. The source code is [BSD 3-Clause licensed](ttps://github.com/ava-labs/avalanchego/blob/master/LICENSE) and is available on [GitHub](ttps://github.com/ava-labs/avalanchego) and the community is encouraged to contribute.

Avalanche is also the name of the 'Proof of Stake' (PoS) consensus protocol utilised in the Avalanche network. Avalanche can be utilised as an open-source platform for launching public or private decentralised applications. 

Avalanche is the first 'smart contracts' capable network that can process over 4,500 transactions per second with instant confirmations and high throughput. Ethereum developers can quickly build on Avalanche as [Solidity](https://docs.soliditylang.org) the programming language of the Ethereum Virtual Machine (EVM) works out of the box. 

The key difference between Avalanche and other L1 networks (like Ethereum) is that the consensus protocols Avalanche and Snowman are breakthroughs in computer science.

The Avalanche protocol employs a novel approach to consensus to achieve its strong safety guarantees, quick finality, and high-throughput, all without compromising decentralisation. It brings together the best of Classical and Nakamoto consensus without any of their their drawbacks.

Avalanche is a network of networks consisting of subnets and dynamic sets of validators working together to come into consensus on the state of a set of blockchains. Each subnet contains blockchains which are instances of virtual machines. There is a primary or default subnet which consists of the P, X and C chains.

<small>The table below demonstrates the function of each subnet within the Avalanche primary network.</small>

| Subnet | Purpose |
|:--|:--|
| Platform (P) | The P-Chain is the metadata blockchain on Avalanche and coordinates validators, keeps track of active subnets, and enables the creation of new subnets. The P-Chain implements the Snowman consensus protocol. |
| eXchange (X) | The X-Chain acts as a decentralised platform for creating and trading digital smart assets, a representation of a real-world resource (e.g., equity, bonds) with a set of rules that govern its behaviour, like “can’t be traded until tomorrow” or “can only be sent to US citizens.” |
| Contract (C) | The C-Chain allows for the creation of smart contracts using the C-Chain’s API. The C-Chain is an instance of the Ethereum Virtual Machine powered by Avalanche. |

## What is AVAX?

AVAX is the native asset (token) of the Avalanche network. It is used as a unit of account. AVAX is hard-capped at 720,000,000 and all transaction fees are burned which makes it a deflationary asset. Transaction fees and staking rewards all utilise AVAX. One AVAX is denomination nine, the smallest unit of AVAX is nanoAVAX (nAVAX) at 10<sup>9</sup>AVAX.

<small>The table below demonstrates the divisions of an AVAX within the metric system.</small>

| Name | Symbol | Base 10 | Decimal | English word |
|:--|:--|:--|:--|:--|
| - | - | 10<sup>0</sup> | 1 | one |
| deci- | d-| 10<sup>-1</sup> | 0.1 | tenth |
| centi- | c-| 10<sup>-2</sup> | 0.01 | hundredth |
| milli- |m- | 10<sup>-3</sup> | 0.001 | thousandth |
| micro- | μ-| 10<sup>-6</sup> | 0.000001 | millionth |
| nano- | n-| 10<sup>-9</sup> | 0.000000001 | billionth |

## History of Avalanche

An anonymous team calling themselves Team Rocket released a [white paper](https://ipfs.io/ipfs/QmUy4jh5mGNZvLkjies1RWM4YuvJh5o2FYopNPVYwrRVGV) on IPFS with the title, Snowflake to Avalanche: A Novel Metastable Consensus Protocol Family for Cryptocurrencies.

This white paper was groundbreaking that it instantly got widespread attention and Team Rocket and Prof. Emin Gün Sirer started collaborating to bring the white paper to life in the form of the Avalanche network.
After many months of work, [Avalabs](https://www.avalabs.org) was born.

Avalabs aims to develop and release the first implementation of the protocol written in the [GO](https://golang.org) programming language called [AvalancheGo](https://github.com/ava-labs/avalanchego). The Avalanche network went through many test iterations until it was ready for production usage referred to as Main Network (Mainnet).

This white paper was so groundbreaking that it instantly got widespread attention and Team Rocket and Prof. Emin Gün Sirer started collaborating to bring the white paper to life in the form of the Avalanche network.

<small>The table below demonstrates the timeline of Avalanche development</small>

| Name | Date |
|:--|:--|
| Denali Test Network | June 2020 |
| Everest Test Network | August 2020 |
| Main Network | September 2020 |
| Apricot Phase One | April 2021 |

## Getting Started

As a user getting started with Avalanche is easy and many centralised exchanges support the buying and selling of AVAX with local currency. It is considered safer and best practice to hold your assets in your wallet. A wallet is a piece of software installed in a browser application, web or mobile application.

### Web Wallet

A web wallet is an online service that allows you to interact with your Avalanche wallet to send and receive AVAX and other supported assets on the Avalanche network like non-fungible token (NFT). A web wallet does not require you to install or set up any applications on your device.

### Hardware Wallet

A hardware wallet is a physical device that lets you store, send and receive digital assets. It is considered to be safer than a web wallet and is referred to as "cold storage".

#### Ledger Nano S

There is an app for the [Ledger Nano S](https://github.com/obsidiansystems/ledger-app-avalanche).

1. Launch [Ledger Live](https://www.ledger.com/ledger-live)
2. Open Settings
3. Toggle on `Developer Mode`
4. Launch and approve Manager
5. Search for and install `Avalanche`

<img src="./assets/ledger-app-install.png" width="400px" alt="ledger install app">

### Creating a Web Wallet

[Avalanche Web Wallet](https://wallet.avax.network) is an example of a web wallet.

When you land on the [Avalanche Web Wallet](https://wallet.avax.network) homepage you are given two choices:

1. Access Wallet
2. Create New Wallet

If you are new to the Avalanche ecosystem you will need to **Create New Wallet**.

You will then be asked to: *"Generate a new key phrase to set up your Avalanche Wallet.*". Click on **Generate Key Phrase**.

At this point, you will be shown **24 words** that were *uniquely generated for you*. Write these 24 words down on a piece of paper and ***never lose them***. These words are the keys to the vault where you will store your digital assets. If anybody has access to these 24 words, they have full access to your vault.

Once the 24 words have been written on a piece of paper you will then need to confirm you have done that by checking a confirmation checkbox. The next screen will require you to fill in the blank words necessary to unlock the vault. Once you have successfully filled in the blank words you will gain access to your wallet.

Congratulations. You now have an Avalanche wallet that you can send and receive assets with. Remember you and you alone should have access to the 24 words.

Now, close the browser window and open a fresh window and navigate to the [Avalanche Web Wallet](https://wallet.avax.network) this time select the **Access Wallet** option and then the **Mnemonic Key Phrase** option. Enter your 24 words and you now have access to your wallet once again.

Once you have a single wallet you do not need to create more wallets.

### Obtaining your first AVAX

As you have created a new web your balance will be zero. You will now need to purchase AVAX from an exchange such as [Binance](ttps://www.binance.com) and then fund your wallet you created.

Your wallet has what's called an address. This is a public address you can share with family and friends for them to send you AVAX. Think of this as your bank account number.

This address can look like: X-avax1yr2gwle82z5xgre5lk5mmkg8nh9l2y9gl54ma0

Copy your own wallet address from [Avalanche Web Wallet](https://wallet.avax.network) and use it to send some AVAX from an exchange that supports AVAX.

### Discovering the price of an AVAX

The price of cryptocurrencies often fluctuate and AVAX is no different. The United States Dollar (USD) or Great Britain Pound (GBP) value of your AVAX will be different from time to time.

To see the USD or GBP value of one AVAX you can navigate to the [CoinGecko AVAX page](https://www.coingecko.com/en/coins/avalanche) or [CoinMarketCap AVAX page](https://coinmarketcap.com/currencies/avalanche/) to understand the price at that moment in time. Both of these websites also have native mobile applications which you can download from your devices "App Store".