# Core concepts of Solana program development

## Runtime 

1. The runtime environment allows the owner program to `debit the account or change its data`. It defines additional rules that determine whether the client can modify the accounts.

👉 The System program `allows users to transfer lamports` by recognizing transaction signatures. If the client has signed the transaction with the keypair's private key, it knows the client `has authorized the token transfer`.

In other words, the entire set of accounts belonging to a given program can be considered a repository of keys and values 👌 The key is `the account's address`, and the value is arbitrary binary data that depends on the program.

After the runtime environment executes each transaction's instructions, it uses the account metadata to ensure that the access policy has not been violated. If the program violates the procedure, the runtime environment `discards all account changes made by all instructions` in the transaction and marks the transaction as failed.

2. As part of the runtime, we need to look at policies, limitations, and sysvar accounts:

2.1 `Policy` 
    Only the owner of the account may change owner

    Only the owner of the account may change account data

    An account not assigned to the program can not have its balance decrease 

    Executable is one-way (false -> true), and only the account owner may set it 

    The balance of read-only and executable accounts may not change

    No one can make modifications to the rent_epoch associated with this account

    Only the system program can change the data size and only if the system program owns the accoutt

2.2 `limitations`

No access to: `rand`, `std::fs`, `std::net`, `std::future`, `std::process`, `std::sync`, `std::task`, `std::thread`, `std::time`

limited access to: `std::hash`, `std::os`

The Bincode trait requires a huge computational cost in cycles and depth of call. `Avoid it.`

`Avoid formatting strings` because they are so expensive for computer calculations.

`No support` for macro “println!”, “print!” the Solana logging helpers should be used instead (we will look at logging in steps 8-9 of the current course).

The runtime limits the number of instructions a program can execute while processing one instruction. 📌 See the computation budget for more information.

2.3 Sysvar 

Solana has specific accounts called sysvar, which `provide various data about the state of the program cluster.`

👉 You can use them to determine the current slot, epoch, the number of slots in a given epoch, commissions for a given slot, block hashes, lease rate, and more.

The sysvar accounts are located at known addresses published along with the account diagrams in `the solana_program crate.`

💻 At runtime, we retrieve the sysvar using `the get() function:`

    ```rust
    let clock = Clock::get()
    ```
Another option is to `pass the sysvar program as an account`. We include it in the instructions as one of the accounts. Access to the sysvar account is` always read-only.`

    ```rust
    let clock_sysvar_info = next_account_info(account_info_iter)?;
    let clock = Clock::from_account_info(&clock_sysvar_info)?;
    ```

    You can find all sysvar accounts and their details here.
    https://docs.anza.xyz/runtime/sysvars

    See this example for hands-on interaction with sysvar accounts.
    https://github.com/solana-labs/solana-program-library/tree/master/examples/rust/sysvar

## Programs 

1. Solana Programs, often called "smart contracts" on other blockchains, are the executable code that interprets the instructions sent inside each transaction on the blockchain.

👉 They can be deployed directly into the network's core as `Native Programs` or published by anyone as `On Chain Programs.`
https://solana.com/docs/core/programs#native-programs
https://solana.com/docs/core/programs#on-chain-programs

Programs are `the core building blocks of the network `and handle everything from sending tokens between wallets, accepting votes of DAOs, and tracking ownership of NFTs.

Both types of programs run on top of the `Sealevel runtime`, which is Solana's parallel processing model that helps to enable the high transaction speeds of the blockchain.
https://medium.com/solana-labs/sealevel-parallel-processing-thousands-of-smart-contracts-d814b378192

2. Key points: 

    Programs are essentially particular types of `Accounts` marked as "executable".

    Programs can `own other Accounts;`

    Programs can only `change the data or debit accounts` they own;

    Any program can `read or credit` another account;

    Programs are considered `stateless` since the primary data stored in a program account is the compiled BPF code;

    Programs can be `upgraded` by their owner (see more below).

👉 These user-written programs are deployed directly to the blockchain for anyone to interact with and execute. Hence, the name `"on-chain"!`

In effect, "on-chain programs" are any programs that are not baked directly into the Solana cluster's core code (like the native programs discussed below).

And even though Solana Labs maintains a small subset of these on-chain programs (collectively known as the Solana Program Library), anyone can create or publish one. On-chain programs can also `be updated directly on the blockchain` by the respective program's Account owner.

https://spl.solana.com/

## Native Programs 

☝ Native programs are programs built directly into the core of the Solana blockchain.

Like other "on-chain" programs in Solana, native programs can be called by any other program/user. However, they can only `be upgraded as part of the core blockchain and cluster updates`. These native program upgrades are controlled via the releases to the `different clusters.`

 Examples of native programs include

`System Program`: Create new accounts, transfer tokens, and more

`BPF Loader Program`: Deploys, upgrades, and executes programs on the chain
    
`Vote program`: Create and manage accounts that track validator voting state and rewards.

1. Executable 

When a Solana program is deployed onto the network, it is marked as` "executable" `by the BPF Loader Program. This allows the Solana runtime to `efficiently and properly execute the compiled program code.`

2. Upgradeable 

🤘 Unlike other blockchains, developers `can upgrade Solana programs` after they are deployed to the network.

## Solana Program Library

The Solana Program Library (SPL) is a `collection of on-chain programs targeting `the `Sealevel parallel runtime`. These programs are tested against Solana's implementation of Sealevel, solana-runtime, and deployed to its mainnet. 

1. Token Program 

    This program defines a standard implementation for `Fungible and Non-Fungible tokens`. With the Token Program, you can:
        `Create` your fungible token
        `View` all Tokens that you own
        `Wrapping` SOL in a Token
        `Transferring` tokens to another user
        `Create a non-fungible` token, etc.

2. SPL Governance

    SPL Governance is a program the chief purpose is to `provide core building blocks` and `primitives to create Decentralized Autonomous Organizations (DAOs)` on the Solana blockchain.

    The program is DAO type, and asset types agnostic and can be used to build any kind of DAOs which can own and manage any type of asset.
        For example, it can be used as an authority provider for mints, token accounts, and other access control where we may want a voting population to collectively vote on the disbursement of funds. It can also control upgrades of itself and other programs through democratic means.

    The program can be used in the simplest form for `Multisig control` over a shared wallet (treasury account) or as a `Multisig upgrade authority` for Solana programs.

    The plugins are ordinary Solana programs and can be written `using any supporting technology like Anchor framework.`
    https://github.com/solana-labs/solana-program-library/tree/master/governance

3. Token Swap Program

    A Uniswap-like exchange for the Token program on the Solana blockchain, implementing multiple automated market maker (AMM) curves.

    ☝ The Token Swap Program allows `simple trading of token pairs without a centralized limit order book.` The program uses a mathematical formula called `"curve"` to calculate the price of all trades.
        Curves aim to mimic normal market dynamics: for example, as traders buy a lot of one token type, the value of the other token type goes up.

    Depositors in the pool provide liquidity for the token pair. That liquidity enables trade execution at the spot price. In exchange for their liquidity, depositors receive pool tokens, representing their fractional ownership in the pool. A program withholds a portion of the input token during each trade as a fee. That fee increases the value of pool tokens stored in the pool.

    ☝ Uniswap and Balancer heavily inspired this program. More information is available in their excellent documentation and whitepapers.

4. Stake Pool

    A program for `pooling together SOL` to be staked by `an off-chain agent running a Delegation Bot` that redistributes the stakes across the network and tries to maximize censorship resistance and rewards.

    👉 Stake pools are an `alternative method of earning staking rewards`. This on-chain program pools together SOL to be staked by a staker, allowing SOL holders to stake and earn rewards without managing stakes.

    In its current iteration, the stake pool accepts active stakes or SOL, so deposits may come from either an active stake or SOL wallet. Withdrawals can return a fully active stake account from one of the stake pool's accounts or SOL from the reserve.

    ☝ This means that stake pool managers and stakers must be comfortable with creating and delegating stakes, which are more advanced operations than sending and receiving SPL tokens and SOL.

## Account 

If the program needs to `store the state between transactions`, it does so using accounts. An account includes metadata that tells the runtime, who is allowed to access the data, and how.

The account includes metadata for the `lifetime of the file`. That lifetime is expressed by a number of fractional native tokens called` lamports.`

👉 Accounts are held in validator memory, and `they pay "rent" `to stay there. Each validator periodically scans all accounts and collects the rent. Any account that drops to zero lamports is purged. Accounts can also be marked `rent-exempt` if they contain a sufficient number of lamports.

A Solana client uses an address to look up an account. ☝ `The address is a 256-bit public key.`

    Key points: Signers, Read-only, Executable, Creating, Ownership and Assignment to Programs, Rent and Rent exemption

1. Program Derived Addresses (PDAs)

👉 Program Derived Addresses (PDAs) are home to accounts that are designed to be controlled by a specific program.

With PDAs, programs can programmatically `sign for certain addresses without needing a private key`. PDAs serve as the foundation for `Cross-Program Invocation`, which allows Solana apps to be composable with one another.

PDAs are `an essential building block` for developing programs on Solana. With PDAs, programs can sign for accounts while guaranteeing that no external user can also generate a valid signature for the same account. In addition to signing for accounts, certain programs can also modify accounts held on their PDAs.

2. Associated Token Accounts (ATAs)

👉 A user may arbitrarily own many token accounts belonging to the same mint, which makes it difficult for other users to know which account they should send tokens to and introduces friction into many other aspects of token management. This program introduces a way to deterministically derive a token account key from a user's main System account address and a token mint address, allowing the user to create a main token account for each token they own. We call these accounts `Associated Token Accounts.`

😎 In addition, it allows a user to `send tokens `to another user even if the beneficiary `does not yet have a token account for that mint.`

Unlike a system transfer, for a token transfer to succeed, the recipient must already have a token account with the compatible mint, and somebody needs to fund that token account.

If the recipient must fund it first, it makes things like airdrop campaigns difficult and just generally increases the friction of token transfers.

📌 See the SPL Token program for more information about tokens in general.
https://spl.solana.com/token
📌 Creating, interacting, and using examples you can see here.
https://solanacookbook.com/references/token.html#how-to-create-a-token-account

## Transactions 

Program execution begins with a transaction being submitted to the cluster. The Solana runtime will execute a program to process each of the instructions contained in the transaction in order and atomically.

1. Instructions 

Each `instruction` specifies a single program, a subset of the transaction's accounts that should be passed to the program, and a data byte array that is passed to the program. The program `interprets the data array and operates on the accounts` specified by the instructions.

👉 The program can return successfully or with an error code. An error return causes the entire transaction to fail immediately.

2. Transaction Fees

The small fees paid to process `instructions` on the Solana blockchain are known as `"transaction fees"`.

✌ As each transaction (which contains one or more instructions) is sent through the network, it gets processed by the current leader validation client. Once confirmed as a global state transaction, this transaction fee is paid to the network to help support the economic design of the Solana blockchain.

✔ Why pay transaction fees?
Transaction fees offer many benefits in the Solana economic design described below:

Many current blockchain economies (e.g., Bitcoin, Ethereum) rely on protocol-based rewards to support the economy in the short term. And when the protocol-derived rewards expire, predict that the revenue generated through transaction fees will support the economy in the long term.
In an attempt to create a sustainable economy on Solana through protocol-based rewards and transaction fees:
    a fixed portion (initially 50%) of each transaction fee is burned (aka destroyed),
    with the remaining fee going to the current leader processing the transaction.

✔ Calculating transaction fees 
    Transactions fees are calculated based on two main parts:
        a statically set base fee per signature;
        the computational resources used during the transaction, measured in "compute units".

✔ Prioritization fee
    Recently, Solana has introduced an optional fee called the "prioritization fee". This additional fee can be paid to help boost how a transaction is prioritized against others, resulting in faster transaction execution times.

    👉 The prioritization fee is calculated by multiplying the requested maximum compute units by the compute-unit price (specified in increments of 0.000001 lamports per compute unit) rounded up to the nearest lamport.

3. Compute Budget

To prevent abuse of computational resources, `each transaction is allocated a computing budget`. The budget specifies the maximum number of computing units a transaction can consume, the costs associated with different types of operations the transaction may perform, and the operational bounds to which the transaction must adhere.

🤓 As the transaction is processed, compute units are consumed by its instruction's programs performing operations such as executing BPF instructions, calling syscalls, etc... 

When the transaction destroys its entire budget or exceeds a bound, such as attempting a call stack that is too deep, `the runtime halts the transaction processing and returns an error.`

☝ For cross-program invocations, the instructions invoked inherit the budget of their parent. If an invoked instruction consumes the transaction's remaining budget or exceeds a bound, the entire invocation chain and the top-level transaction processing are halted.

📌 The current compute budget can be found in the Solana Program Runtime.
https://github.com/solana-labs/solana/blob/090e11210aa7222d8295610a6ccac4acda711bb9/program-runtime/src/compute_budget.rs#L26-L87

4. Blockhash

A transaction includes a recent `blockhash` to prevent duplication and to give transactions lifetimes. Any transaction that is completely identical to a previous one is rejected, so adding a newer blockhash `allows multiple transactions to repeat the same action`. Transactions also have `lifetimes defined by the blockhash`, as any transaction whose blockhash is too old will be rejected.

5. Configuring State Commitment
    // update later

6. Data Types 

    ➜ Pubkey is the address of a Solana account. Some account addresses are ed25519 public keys, with corresponding secret keys managed off-chain.

    ➜ Hash is a cryptographic hash – used to identify blocks uniquely and for general-purpose hashing.

    ➜ AccountInfo is a description of a single Solana account.

    ➜ Instruction is a directive that tells the runtime to execute a program, passing it into a set of accounts and program-specific data.

    ➜ ProgramError and ProgramResult are the error types all programs must return, reported to the runtime as a u64.

    ➜ Sol is the Solana native token type, with conversions to and from lamports, the smallest fractional unit of SOL, in the native_token module.

    ➜ In solana_sdk and solana_program other famous data structures are well described.

👓 Let's look at a code example.

Programs on Solana have a single entry point. The parameters of the entry point are strictly defined:

    ```rust 
    entrypoint!(process_instruction);
    fn process_instruction(
        program_id : &Pubkey,
        accounts: &[AccountInfo],
        instruction_data: &[u8],
    ) -> ProgramResult {
        Processor::process(program_id,accounts,instruction_data)
    }
    ```

The `program_id` is `the public key of the currently executing program.`

The  `accounts`  are an ordered slice of the `AccountInfo` referenced by the instruction. The place in the array of each of them matters because you are always working with serialized data. ☝ `If the order of the accounts is wrong, the program will not work as expected.`

👉 The members of the AccountInfo structure are read-only except for `lamports` and data. 

The  `instruction_data` is the `general-purpose byte array`. As mentioned earlier, a byte array of data about what operations the program should perform and any additional information those operations may require.

The data the program interacts with is in a serialized state, `forcing us to deserialize it for interaction`. We will look at how to do this in lesson 10.

## Cross-Program Invocations (CPIS)

👌 The Solana runtime allows programs to call each other via a mechanism called `cross-program invocation`. Calling between programs is achieved by one program invoking an instruction of the other. The invoking program is halted until the invoked program finishes processing the instruction.

`✔ Instructions that require privileges`

The runtime uses the privileges granted to the caller program to determine `what privileges can be extended to the callee`. Privileges in this context refer to signers and writable accounts.

    For example, suppose the instruction the caller is processing contains a signer or writable account. In that case, the caller can invoke an instruction that also contains that signer and/or writable account.

👉 This privilege extension relies on the fact that programs are immutable, except during the particular case of program upgrades.

`✔ Program signed accounts`

Programs can issue instructions containing signed accounts not signed in the original transaction by using Program derived addresses.

`✔ Call Depth`
Cross-program invocations allow programs to invoke other programs directly, but the depth is `currently constrained to 4.`

`✔ Reentrancy`
Reentrancy is `currently limited to direct self-recursion`, capped at a fixed depth. This restriction prevents situations where a program might invoke another from an intermediary state without the knowledge that it might later be called back into. Direct recursion gives the program `complete control of its state` when it gets called back.