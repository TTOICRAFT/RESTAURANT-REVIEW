# 🍽️ Restaurant Review Solana Smart Contract

## Overview

This Solana smart contract allows users to **create** and **update** reviews for restaurants on-chain. Each review includes:

* `title` – The name of the restaurant or review subject.
* `description` – A detailed review description.
* `rating` – A numeric score from 1 to 10.
* `location` – The physical location of the restaurant.

All data is stored using Solana’s **Program Derived Addresses (PDAs)** and **Borsh serialization**, ensuring reviews are safely and efficiently stored on-chain.

---

## 📦 Features

* **Add Review**: Create a new restaurant review on-chain.
* **Update Review**: Modify an existing review.
* **Validation**: Ensures ratings are between 1 and 10.
* **PDA Verification**: Reviews are stored under unique PDAs derived from the reviewer and title, preventing tampering.

---

## 📁 Project Structure

```bash
.
├── src/
│   ├── instruction.rs       # Defines ReviewInstruction and payload handling
│   ├── state.rs             # Defines AccountState and custom errors
│   └── lib.rs               # Entrypoint logic for handling Solana program instructions
├── Cargo.toml
└── README.md
```

---

## 🚀 Deployment Guide

### Prerequisites

Ensure the following are installed:

* [Rust + Cargo](https://www.rust-lang.org/tools/install)
* [Solana CLI](https://docs.solana.com/cli/install-solana-cli-tools)
* [Anchor](https://book.anchor-lang.com/chapter_2/anchor_init.html) *(optional but helpful for testing)*

---

### 🔧 Build the Program

```bash
cargo build-bpf
```

This compiles your program to BPF format, which Solana uses for on-chain programs.

---

### 📡 Deploy to Localnet/Testnet

```bash
solana-test-validator
```

Open a new terminal:

```bash
solana config set --url localhost

solana airdrop 2

solana program deploy ./target/deploy/restaurant_review.so
```

Copy the **program ID** printed in the console. You’ll use it to interact with the contract.

---

## 🧪 Interacting With the Contract

### 1. **Add a Review**

Send an instruction with:

* Variant: `0`
* Payload (Borsh): `title`, `rating`, `description`, `location`

The PDA will be derived from your wallet and the review title.

### 2. **Update a Review**

Send an instruction with:

* Variant: `1`
* Payload (Borsh): `title`, `rating`, `description`, `location`

Must be sent by the original author (initializer). The same PDA derivation logic is used.

> You can use custom scripts or clients with `@solana/web3.js`, `borsh`, or `anchor-client` to build and send these instructions.

---

## 🔐 PDA and Account Layout

The PDA is derived as:

```rust
Pubkey::find_program_address(
    &[initializer.key.as_ref(), title.as_bytes()],
    &program_id
);
```

Each `AccountState` contains:

```rust
pub struct AccountState {
    pub is_initialized: bool,
    pub rating: u8,
    pub description: String,
    pub title: String,
    pub location: String,
}
```

---

## ⚠️ Error Handling

Custom errors are implemented via `thiserror` and converted to `ProgramError`. Possible errors include:

* `UninitializedAccount`
* `InvalidPDA`
* `InvalidRating`

---

## 💡 Ideas for Future Enhancements

* **Delete Review** functionality.
* **Review history or versioning**.
* **Like/dislike voting system**.
* **Indexing reviews by location/title via off-chain program**.

---

## 🧾 License

This project is MIT licensed. Feel free to contribute and enhance the contract!

---

Developed by TTOICRAFT.
