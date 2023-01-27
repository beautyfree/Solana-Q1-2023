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