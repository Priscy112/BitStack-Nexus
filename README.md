

# Bitstack-Nexus - BitStack Analytics Contract

The ** BitStack Analytics Contract** is a decentralized finance (DeFi) smart contract written in Clarity for the Stacks blockchain. It enables advanced staking, multi-tiered rewards, on-chain governance, and emergency controls to ensure robust and transparent platform management.

---

## 🚀 Features

- **Multi-Tier STX Staking**  
  Stake STX tokens with optional lock periods to earn rewards. Higher stakes and longer lock-ins yield better multipliers.

- **Dynamic Tier System**  
  Automatically assigns users to tier levels (1-3) based on stake amount. Tiers control access to features and reward multipliers.

- **Governance Protocol**  
  Users can propose and vote on governance actions. Only those with enough voting power (≥ 1M) can create proposals.

- **Emergency Controls**  
  Owner can pause/resume contract operations and enable safe modes.

- **Cooldown-Based Unstaking**  
  Protect the system from instant withdrawal risks by enforcing a cooldown window before users can unstake.

- **Advanced Reward Calculations**  
  Rewards are computed using a base rate, tier multiplier, and optional lock period multiplier.

---

## 🛠 Contract Components

### 📄 Constants

- `contract-owner`: Sets deployer as admin.
- Error codes (`ERR-*`) handle reverts and user feedback.
- `ANALYTICS-TOKEN`: Native platform token (fungible).

---

### ⚙️ Configuration Variables

| Variable              | Description                                  | Default       |
|-----------------------|----------------------------------------------|----------------|
| `contract-paused`     | Pauses all user interactions                 | `false`        |
| `emergency-mode`      | Activates emergency behavior (future use)    | `false`        |
| `stx-pool`            | Total STX staked in the system               | `0`            |
| `base-reward-rate`    | Base reward rate (5%)                        | `500`          |
| `bonus-rate`          | Bonus reward for longer stake                | `100`          |
| `minimum-stake`       | Minimum required stake to participate        | `1,000,000`    |
| `cooldown-period`     | Cooldown period before unstaking             | `1,440 blocks` |

---

### 📊 User Data Maps

#### `UserPositions`
Tracks each user's financial and staking data, including:
- Total collateral/debt
- STX staked
- Voting power
- Tier level
- Reward multiplier

#### `StakingPositions`
Tracks:
- Stake amount
- Lock period
- Last claimed block
- Cooldown status
- Accumulated rewards

#### `TierLevels`
Defines tier settings:
- Required minimum stake
- Reward multiplier
- Feature access flags

---

## 🔒 Staking System

### ✅ Stake STX
```clojure
(stake-stx amount lock-period)
```
- Stake STX with optional lock.
- Lock boosts reward:
  - No lock: `1x`
  - 1 month: `1.25x`
  - 2 months+: `1.5x`

### 🕒 Initiate Unstake
```clojure
(initiate-unstake amount)
```
- Starts a cooldown timer before funds can be withdrawn.

### 🔓 Complete Unstake
```clojure
(complete-unstake)
```
- Finalizes withdrawal after cooldown.

---

## 💰 Rewards System

Rewards are calculated using:
```
rewards = (stake × base-rate × multiplier × blocks) / 14,400,000
```
Where:
- `base-rate` = `500` (5%)
- `multiplier` = Tier + Lock Bonus

---

## 🗳 Governance System

### 📌 Create Proposal
```clojure
(create-proposal description voting-period)
```
- Requires ≥ 1M voting power.
- Set a voting window in blocks.

### 🗳 Vote on Proposal
```clojure
(vote-on-proposal proposal-id vote-for)
```
- Vote **for** or **against** active proposals.

Proposals track:
- Creator, description, block range
- Vote tally, execution status

---

## 🛑 Emergency & Admin Controls

### 🚫 Pause Contract
```clojure
(pause-contract)
```
- Only `contract-owner` can pause.

### ▶️ Resume Contract
```clojure
(resume-contract)
```
- Only `contract-owner` can resume.

---

## 🧩 Tier Levels

| Tier | Min Stake (uSTX) | Reward Multiplier | Features Enabled                  |
|------|------------------|-------------------|------------------------------------|
| 1    | 1,000,000        | 1x (100)          | `[true, false, ...]`               |
| 2    | 5,000,000        | 1.5x (150)        | `[true, true, true, false, ...]`   |
| 3    | 10,000,000       | 2x (200)          | `[true, true, true, true, true...]`|

---

## 🧪 Initialization

Initialize the contract post-deployment to set tiers:
```clojure
(initialize-contract)
```

Must be called by `contract-owner`.

---

## 🛡 Security & Error Codes

| Code              | Error Description                  |
|-------------------|------------------------------------|
| `ERR-NOT-AUTHORIZED` | Unauthorized access              |
| `ERR-INVALID-PROTOCOL` | Bad proposal ID              |
| `ERR-INVALID-AMOUNT` | Invalid staking amount           |
| `ERR-INSUFFICIENT-STX` | Not enough STX to stake/unstake |
| `ERR-COOLDOWN-ACTIVE` | Cooldown already in progress     |
| `ERR-NO-STAKE` | No stake found for user                |
| `ERR-BELOW-MINIMUM` | Stake below platform minimum       |
| `ERR-PAUSED` | Contract is currently paused              |

---
