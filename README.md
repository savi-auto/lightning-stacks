# LightningStacks - Dynamic Payment Channel Network

## Overview

LightningStacks is a smart contract designed to facilitate fast, secure, and scalable off-chain transactions on the Stacks blockchain. It enables participants to create, fund, and manage payment channels, allowing for efficient peer-to-peer transactions without requiring on-chain operations for every payment. The contract supports cooperative and unilateral channel closures, dispute resolution, and emergency withdrawals, ensuring a trustless and seamless experience for users.

Built on Stacks L2, LightningStacks leverages Bitcoin's security while enabling high-speed, low-cost transactions. This makes it ideal for microtransactions, recurring payments, and other use cases where on-chain transactions would be impractical.

---

## Key Features

1. **Payment Channel Creation**:

   - Participants can create a payment channel by depositing funds.
   - Each channel is uniquely identified by a `channel-id` and involves two participants.

2. **Funding Channels**:

   - Participants can add funds to an existing channel at any time.

3. **Cooperative Channel Closure**:

   - Participants can close a channel cooperatively by agreeing on the final balances and signing off on the transaction.

4. **Unilateral Channel Closure**:

   - If one participant becomes unresponsive, the other can initiate a unilateral closure after a dispute period.

5. **Dispute Resolution**:

   - A built-in dispute period ensures fairness in unilateral closures, allowing the other party to challenge the proposed balances.

6. **Emergency Withdrawal**:

   - The contract owner can withdraw funds in case of emergencies, ensuring the safety of locked funds.

7. **Read-Only Functions**:
   - Participants can query the status of a channel, including balances and dispute deadlines.

---

## Contract Structure

### Constants

- `CONTRACT-OWNER`: The principal address of the contract owner.
- Error codes (`ERR-NOT-AUTHORIZED`, `ERR-CHANNEL-EXISTS`, etc.): Standardized error messages for better debugging and user feedback.

### Data Structures

- **Payment Channels**:
  - Stored in a map (`payment-channels`) with the following fields:
    - `channel-id`: Unique identifier for the channel.
    - `participant-a` and `participant-b`: Principals of the two participants.
    - `total-deposited`: Total funds deposited in the channel.
    - `balance-a` and `balance-b`: Balances for each participant.
    - `is-open`: Indicates whether the channel is open or closed.
    - `dispute-deadline`: Timestamp for dispute resolution in unilateral closures.
    - `nonce`: Prevents replay attacks.

### Functions

1. **`create-channel`**:

   - Creates a new payment channel.
   - Requires a unique `channel-id`, the other participant's principal, and an initial deposit.

2. **`fund-channel`**:

   - Adds funds to an existing channel.
   - Requires the `channel-id`, the other participant's principal, and the additional funds.

3. **`close-channel-cooperative`**:

   - Closes a channel cooperatively.
   - Requires both participants to sign off on the final balances.

4. **`initiate-unilateral-close`**:

   - Initiates a unilateral channel closure.
   - Requires the initiating participant to propose final balances and provide a signature.

5. **`resolve-unilateral-close`**:

   - Resolves a unilateral closure after the dispute period has passed.
   - Transfers funds based on the proposed balances.

6. **`get-channel-info`**:

   - A read-only function to query the status of a channel.

7. **`emergency-withdraw`**:
   - Allows the contract owner to withdraw funds in case of emergencies.

---

## Usage

### Creating a Channel

To create a payment channel, call the `create-channel` function with the following parameters:

- `channel-id`: A unique identifier for the channel (32-byte buffer).
- `participant-b`: The principal of the other participant.
- `initial-deposit`: The initial amount of STX to deposit.

Example:

```clarity
(create-channel 0x1234... participant-b 1000)
```

### Funding a Channel

To add funds to an existing channel, call the `fund-channel` function with the following parameters:

- `channel-id`: The identifier of the channel.
- `participant-b`: The principal of the other participant.
- `additional-funds`: The amount of STX to add.

Example:

```clarity
(fund-channel 0x1234... participant-b 500)
```

### Closing a Channel Cooperatively

To close a channel cooperatively, call the `close-channel-cooperative` function with the following parameters:

- `channel-id`: The identifier of the channel.
- `participant-b`: The principal of the other participant.
- `balance-a` and `balance-b`: The final balances for each participant.
- `signature-a` and `signature-b`: Signatures from both participants.

Example:

```clarity
(close-channel-cooperative 0x1234... participant-b 700 800 signature-a signature-b)
```

### Initiating a Unilateral Closure

To initiate a unilateral closure, call the `initiate-unilateral-close` function with the following parameters:

- `channel-id`: The identifier of the channel.
- `participant-b`: The principal of the other participant.
- `proposed-balance-a` and `proposed-balance-b`: The proposed final balances.
- `signature`: The initiating participant's signature.

Example:

```clarity
(initiate-unilateral-close 0x1234... participant-b 700 800 signature)
```

### Resolving a Unilateral Closure

To resolve a unilateral closure, call the `resolve-unilateral-close` function with the following parameters:

- `channel-id`: The identifier of the channel.
- `participant-b`: The principal of the other participant.

Example:

```clarity
(resolve-unilateral-close 0x1234... participant-b)
```

### Querying Channel Information

To query the status of a channel, call the `get-channel-info` function with the following parameters:

- `channel-id`: The identifier of the channel.
- `participant-a` and `participant-b`: The principals of the participants.

Example:

```clarity
(get-channel-info 0x1234... participant-a participant-b)
```

### Emergency Withdrawal

To withdraw funds in an emergency, the contract owner can call the `emergency-withdraw` function.

Example:

```clarity
(emergency-withdraw)
```

---

## Security Considerations

- **Input Validation**: All inputs are rigorously validated to prevent invalid or malicious data from affecting the contract.
- **Signature Verification**: Signatures are verified to ensure that only authorized participants can modify channel states.
- **Dispute Period**: A built-in dispute period ensures fairness in unilateral closures.
- **Emergency Withdrawal**: The contract owner can withdraw funds in emergencies, providing an additional layer of security.

---

## Future Enhancements

- Support for multi-hop payments across channels.
- Integration with decentralized oracles for dispute resolution.
- Gas optimizations for large-scale usage.
