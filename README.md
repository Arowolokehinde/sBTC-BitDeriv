
# sBTC-BitDeriv - Decentralized P2P BTC Options Contract

## Overview

This Clarity smart contract implements a decentralized peer-to-peer options trading protocol on the Stacks blockchain, with internal BTC tracking. It allows users to:

* Deposit and withdraw BTC (simulated via internal balance mapping)
* Create **CALL** or **PUT** options with a BTC notional value
* Buy and exercise options before expiry
* Use a BTC/USD oracle for off-chain price data input

The contract tracks user BTC balances, manages option lifecycles, and ensures secure settlement based on strike prices.

---

## 📦 Features

* ✅ Internal BTC wallet tracking for each user
* ✅ Support for CALL and PUT options
* ✅ Option creation, purchase, and exercise flow
* ✅ Role-based BTC price oracle updates
* ✅ User option history tracking
* ✅ Error handling for edge cases (e.g., expired options, insufficient balance)

---

## 🔐 Data Structures

### `BTCBalances`

Tracks each user's internal BTC balance.

```clarity
{ holder: principal } => { balance: uint }
```

### `Options`

Stores all created options with metadata.

```clarity
{ 
  option-id: uint 
} => {
  creator: principal,
  buyer: (optional principal),
  option-type: uint, ;; 1 = CALL, 2 = PUT
  strike-price: uint,
  premium: uint,
  expiry: uint,
  btc-amount: uint,
  is-active: bool,
  is-executed: bool,
  creation-height: uint
}
```

### `UserOptions`

Stores created and purchased option IDs per user.

```clarity
{ user: principal } => { created: (list 20 uint), purchased: (list 20 uint) }
```

---

## ⚙️ Public Functions

### `deposit-btc (amount uint)`

Deposits BTC into the user's internal balance.

### `withdraw-btc (amount uint)`

Withdraws BTC from the user's internal balance.

### `create-option (option-type uint, strike-price uint, premium uint, expiry uint, btc-amount uint)`

Creates a new CALL or PUT option. CALL creators must have sufficient BTC in balance.

### `exercise-option (option-id uint)`

Exercises an option if valid and not expired.

### `set-btc-price (price uint)`

Updates the oracle BTC price. **Restricted to contract owner.**

---

## 🧠 Read-Only Functions

* `get-option (option-id uint)` — fetches option metadata
* `get-btc-balance (holder principal)` — gets user's BTC balance
* `get-user-options (user principal)` — returns created and purchased options
* `get-btc-price` — gets current oracle price

---

## 🔐 Authorization

* `CONTRACT-OWNER` is defined as the deploying `tx-sender`.
* Only the contract owner can update the BTC oracle price.

---

## 🧪 Option Lifecycle

1. **Deposit BTC** using `deposit-btc`.
2. **Create Option** (CALL/PUT) with strike price, expiry, etc.
3. A buyer can be assigned (future work can allow open purchase).
4. Buyer **exercises option** before expiry:

   * CALL: Buyer pays strike price in STX, receives BTC.
   * PUT: Seller pays strike price in STX, receives BTC.
5. Option is marked as executed.

---

## 🚨 Errors

| Code | Error                |
| ---- | -------------------- |
| u1   | Option expired       |
| u2   | Invalid amount       |
| u3   | Unauthorized         |
| u4   | Option not found     |
| u5   | Insufficient balance |
| u6   | Already executed     |
| u7   | Invalid price        |
| u8   | Option not active    |

---

## 🛠 Future Improvements

* Add open market listing for buying options
* Enable partial exercise
* Real BTC integration (via sBTC or PoX swap)
* UI frontend for interaction

---
