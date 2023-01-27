https://paulx.dev/blog/2021/01/14/programming-on-solana-an-introduction/

### Recaps

- On Solana, smart contracts are called programs

- each program is processed by its BPF Loader and has an entrypoint whose structure depends on which BPF Loader is used
- accounts are used to store state
- accounts are owned by programs
- only the account owner may debit an account and adjust its data
- all accounts to be written to or read must be passed into the entrypoint

- developers should use the data field to save data inside accounts
- the token program owns token accounts which - inside their data field - hold [relevant information](https://github.com/solana-labs/solana-program-library/blob/master/token/program/src/state.rs#L86)
- the token program also owns token mint accounts with [relevant data](https://github.com/solana-labs/solana-program-library/blob/master/token/program/src/state.rs#L86)
- each token account holds a reference to their token mint account, thereby stating which token mint they belong to
- the token program allows the authority of a token account to transfer its ownership to another address
- All internal Solana internal account information are saved into [fields on the account](https://docs.rs/solana-program/1.5.0/solana_program/account_info/struct.AccountInfo.html#fields) but never into the data field which is solely meant for user space information

- Solana has sysvars that are parameters of the Solana cluster you are on. These sysvars can be accessed through accounts and store parameters such as what the current fee or rent is. As of solana-program version 1.6.5, [sysvars can also be accessed without being passed into the entrypoint as an account](https://github.com/solana-labs/solana/blob/a1a0d6f84b30ecea1fd1b699aa3cd6ab2741db77/programs/bpf/rust/sysvar/src/lib.rs)(this tutorial will continue to use the old way for now, but you shouldn't!).

- Rent is deducted from an account's balance according to their space requirements regularly. An account can, however, be made rent-exempt if its balance is higher than some threshold that depends on the space it's consuming

- Program Derived Addresses do not lie on the `ed25519` curve and therefore have no private key associated with them.
- When including a `signed` account in a program call, in all CPIs including that account made by that program inside the current instruction, the account will also be `signed`, i.e. the signature is extended to the CPIs.

- There can be several instructions (ix) inside one transaction (tx) in Solana. These instructions are executed out synchronously and the tx as a whole is executed atomically. These instructions can call different programs.
- The system program is responsible for allocating account space and assigning (internal - not user space) account ownership
- Instructions may depend on previous instructions inside the same transaction
- Commitment settings give downstream developers ways to query the network which differ in finality likelihood

- when a program calls `invoke_signed`, the runtime uses the given seeds and the program id of the calling program to recreate the PDA and if it matches one of the given accounts inside `invoke_signed`'s arguments, that account's `signed` property will be set to true
- If an account has no balance left, it will be purged from memory by the runtime after the transaction (you can see this when going navigating to an account that has been closed in the explorer)
- "closing" instructions must set the data field properly, even if the intent is to have the account be purged from memory after the transaction
#