# Transfer Settlement Network

The Transfer Settlement Network (TSN) is the payment-settlement system built by TrustLink Labs. It gives a payment a verifiable lifecycle: a sender authorizes an intent, the network validates its rules, and the settlement layer records the resulting state.

TSN is currently Solana-first. Solana provides the live execution environment for authorization, source-side settlement, treasury accounting, and protocol state. The system is designed around explicit authority and replay protection so that operators can submit work without changing the terms authorized by the sender.

The current stack separates payment identity, routing, authorization, settlement liability, and confidential balance accounting. That separation makes each responsibility auditable and keeps infrastructure operators from becoming spend authorities.

## Current technology

- TSN coordinates payment intents, accepted intents, settlement authorization, and treasury liability on Solana.
- Transfer Identity Numbers (TINs) abstract payment identity from on-chain account addresses.
- GPRU provides scoped, non-custodial routing and does not hold balances.
- TCAP records private balance transitions through commitments and encrypted snapshots.
- Nullifiers, sequence checks, validity windows, and signed fields prevent replay and unauthorized substitution.

Start with [How It Works](how-it-works) or [Getting Started](../developers/getting-started).
