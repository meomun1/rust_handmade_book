# What is Solana?

## Intro 

😎 Solana is an energy-efficient Tier 1 blockchain with high transaction processing speeds and the ability to scale stably. It launched in 2017 with the then-innovative `Proof-of-History (PoH) consensus mechanism`, which provides one of the highest transaction processing speeds per second (more than 700,000 transactions per second ✌). The following year, the development team released a `testnet`, and they released the `mainnet` in 2020.

## Architecture Overview 

Solana labs created the blockchain architecture to facilitate the development of decentralized, scalable applications `(DApps)` and `smart contracts`. With this in mind, it satisfies all the characteristics of the famous trilemma blockchain: `it's scalable, secure, and decentralized ✓`

Data transformation in the Solana blockchain is performed based on `the SHA256 hash function`. It obtains a unique combination of characters by using the input data. The network creates the next hash of data based on the received transaction information. 

The way described above creates a continuous, long chain of hashed transactions, which is relatively easy to understand and verify.
👌 This data algorithm is what makes transaction processing so complex.

## Ethereum and Solana 

Ethereum is currently the most prominent blockchain. It hosts more decentralized applications than any other blockchain ecosystem. However, due to Ethereum's low bandwidth processing capacity of about 15 transactions per second, the vast enthusiasm around the project makes gas prices exorbitant.

👉 In contrast, the Solana blockchain combines several new protocols, solving the trilemma of scalability, security, and decentralization. It takes `0.4 seconds to process a block`, and as hardware capacity increases, so does the speed of the entire network.

This difference in speed is due to the `difference in the consensus algorithm`.

## A little bit about the fundamental differences between the consensuses

`PoW` consensus blockchains do a lot of energy-consuming work. Due to the unstable climate situation, many consider the PoW consensus unsuitable. Low-energy efficiency was one of the main reasons `Ethereum` switched from `Proof-of-Work` consensus to `Proof-of-Stake (PoS)`.

👉 `PoS` replaces miners with validators, who bid on matching tokens and thus participate in verifying blocks in the network. They do not need to compete, as miners do, to be the first to solve the puzzle. Instead, users are randomly selected based on their stakes. The higher the rate, the better the chance of being chosen by the validator. When a validator is appointed, they must propose a block. If other users verify that block, the validator receives a reward consisting of the transaction fee for that block. `PoS is slightly less secure than PoW` because people, not mathematical decisions, determine its security. There is also the possibility that with PoS, a group of validators will take control.

Still, in reality, these validators can be blocked or excluded from the network, so the more significant the blockchain, the less likely this is. Also, due to the lack of computation, `PoS is less energy intensive`.

☝ Solana combines `Proof-of-Stake in its Proof-History (PoH)` consensus to provide a unique consensus algorithm. `Each transaction or data fragment is assigned a unique timestamp, representing state and data with cryptographically secure hashes`. Timestamps guarantee the order of events and precisely determine the time of data creation. Although PoH is critical to the consensus model, it is not the primary consensus protocol. Instead, Solana uses a practical Byzantine fault tolerance mechanism (pBFT) called `Tower Consensus`, intertwined with the PoS mechanism. We do not need to dive into it now; this information is enough to get acquainted with it 😉

The combined efforts of `PoS validators` using the `PoH protocol` and optimizing consensus make Solana one of the world's fastest, safest, and most decentralized blockchains. `The Tower Consensus protocol` reduces latency using the PoH protocol as a global time source.