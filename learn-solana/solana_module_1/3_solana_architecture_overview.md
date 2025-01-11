# Solana architecture overview

## Solana and consensus

🤓 Solana's unique architecture solves decentralization, scalability, and security problems. The main features of the architecture are:

`the presence of on-chain time verification`: thanks to the verifiable delay function (VDF), a function whose value calculation requires a fixed amount of time, many times less than the verification of that value;

`streaming transactions` that do not require waiting for consensus and security issues;

`minimizing the amount of data and streaming it` using UDP; 

`separates data and code,` which allows the program to run faster

`unique consensus model.`

Solana's `PoS-based` consensus mechanism is known as `Tower BFT (tBFT)`. This model uses `Proof of History (PoH)` as a consensus `clock` to reduce communication overhead and latency. For this cost reduction, encode the passage of time in SHA-256, sequential-hashing verifiable delay function (VDF). This hybrid Solana consensus model pushes the boundaries of validation time.


📌 We recommend reading this article if you want to learn more about PoH.
https://medium.com/@anatolyyakovenko/proof-of-history-a-decentralized-clock-for-blockchain-9d245bd5abb3

## Solana and validators

Validators form the backbone of the Solana network. ☝ They process transactions and participate in consensus. 

`Cluster`: a set of independent computers (validators or nodes) working together (and sometimes against each other) to validate to serve transactions is called a cluster 

`Ledger`: A validator is a full member of the Solana network cluster that produces new blocks. The validator validates transactions added to the ledger.


`Validator`: The validator contains two pipeline processes, one used in ledger mode and one used in validator mode.

`Leader`: the role of the validator, which adds entries to the ledger. The validators select leader according to a specific algorithm. The time during a leader accepts transactions in the network and creates a block is called a slot

`Block`: continous set of leager entries. A leader produces no more than one block per slot 

`Epoch`: a time counted in slots. An epoch is 432,000 slots 