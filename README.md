# Open-Guild-Technical-Activity-draft

# Tutorial: Working with PSP22 and PSP34 using Inkathon and OpenBrush

This tutorial will guide you through implementing and working with the PSP22 (a token standard similar to ERC20) and PSP34 (similar to ERC721) using the Inkathon framework and OpenBrush library. These tools are part of the Polkadot ecosystem for smart contract development using the Ink! language.

## Prerequisites

Before we start, ensure you have the following:

1. **Rust**: Install Rust and set up your environment.
   - Install Rust: [Rust Installation Guide](https://www.rust-lang.org/tools/install)
   - Add the `wasm32-unknown-unknown` target: 
     ```bash
     rustup target add wasm32-unknown-unknown
     ```

2. **Ink! CLI**: Ink! is a smart contract language for Substrate. Install it using the following command:
   ```bash
   cargo install --force --locked cargo-contract
   ```

3. **Node.js and NPM**: Inkathon is a TypeScript library, so you’ll need Node.js and NPM.
   - Install Node.js and NPM: [Node.js Download](https://nodejs.org/)

4. **Inkathon and OpenBrush**: Install these using the commands below:
   - Inkathon:
     ```bash
     npm install --save inkathon
     ```
   - OpenBrush: 
     ```bash
     cargo install cargo-contract
     ```

## Step 1: Set Up a New Ink! Project

Create a new Ink! project for your PSP22 or PSP34 contract.

```bash
cargo contract new my_psp_contract
cd my_psp_contract
```

This will create a basic Ink! project structure.

## Step 2: Add OpenBrush Dependency

Open the `Cargo.toml` file in your project and add OpenBrush as a dependency.

```toml
[dependencies]
ink = "4.0.0"
openbrush = { git = "https://github.com/727-Ventures/openbrush-contracts.git", tag = "v2.0.0" }
```

## Step 3: Implement PSP22 or PSP34

Now, let's implement a basic PSP22 token.

### PSP22 Token Implementation:

Create a new file `lib.rs` under the `src` directory and paste the following code:

```rust
use ink_lang as ink;
use openbrush::contracts::psp22::*;
use openbrush::traits::Storage;

#[ink::contract]
mod my_psp22 {
    use super::*;
    use openbrush::contracts::psp22::extensions::mintable::*;

    #[ink(storage)]
    #[derive(Default, Storage)]
    pub struct MyPSP22 {
        #[storage_field]
        psp22: psp22::Data,
    }

    impl PSP22 for MyPSP22 {}
    impl PSP22Mintable for MyPSP22 {}

    impl MyPSP22 {
        #[ink(constructor)]
        pub fn new(initial_supply: Balance) -> Self {
            let mut instance = Self::default();
            instance._mint_to(Self::env().caller(), initial_supply).expect("Should mint");
            instance
        }
    }
}
```

This code creates a basic PSP22 token with minting functionality.

### PSP34 Token Implementation:

For PSP34, create a new file `lib.rs` under the `src` directory and paste the following code:

```rust
use ink_lang as ink;
use openbrush::contracts::psp34::*;
use openbrush::traits::Storage;

#[ink::contract]
mod my_psp34 {
    use super::*;
    use openbrush::contracts::psp34::extensions::enumerable::*;

    #[ink(storage)]
    #[derive(Default, Storage)]
    pub struct MyPSP34 {
        #[storage_field]
        psp34: psp34::Data,
    }

    impl PSP34 for MyPSP34 {}
    impl PSP34Enumerable for MyPSP34 {}

    impl MyPSP34 {
        #[ink(constructor)]
        pub fn new() -> Self {
            Self::default()
        }

        #[ink(message)]
        pub fn mint(&mut self, to: AccountId, id: Id) -> Result<(), PSP34Error> {
            self._mint_to(to, id)
        }
    }
}
```

This code creates a basic PSP34 token with enumerable functionality.

## Step 4: Compile and Deploy

Compile your contract using the following command:

```bash
cargo +nightly contract build
```

This will generate a `.wasm` file that you can deploy on a Substrate-based chain.

## Step 5: Deploy and Interact using Inkathon

Inkathon provides tools to interact with your smart contract from a TypeScript environment.

### Create a new Inkathon project:

```bash
npx create-inkathon-app my_inkathon_app
cd my_inkathon_app
npm install
```

### Deploy the Contract:

Use the Inkathon framework to deploy your contract:

```typescript
import { deployContract } from '@scio-labs/inkathon';

async function deployPSP22Contract() {
    const contractAddress = await deployContract({
        codeHash: '<your_contract_code_hash>',
        constructor: '<constructor>',
        gasLimit: '<gas_limit>',
        value: '<value>',
    });
    console.log('Contract deployed at:', contractAddress);
}

deployPSP22Contract();
```

Replace `<your_contract_code_hash>`, `<constructor>`, `<gas_limit>`, and `<value>` with the appropriate values from your contract.

### Interact with the Contract:

Use Inkathon's functions to call your contract methods, such as minting tokens, transferring, etc.

```typescript
import { callContractMethod } from '@scio-labs/inkathon';

async function mintPSP22Token(contractAddress: string, to: string, amount: string) {
    const result = await callContractMethod({
        address: contractAddress,
        method: 'mint',
        args: [to, amount],
        gasLimit: '<gas_limit>',
        value: '<value>',
    });
    console.log('Minting result:', result);
}

mintPSP22Token('<contract_address>', '<recipient_address>', '1000');
```

## Conclusion

You’ve now set up a basic environment to work with PSP22 and PSP34 contracts using Inkathon and OpenBrush. You can expand these contracts by implementing additional features or extending the functionality according to your needs.

Feel free to explore further by diving into the [Ink! documentation](https://use.ink/) and the [OpenBrush GitHub repository](https://github.com/727-Ventures/openbrush-contracts) for more advanced use cases and examples.
