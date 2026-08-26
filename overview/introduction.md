---
title: "TSN: Identity-First, Intent-Based Stablecoin Settlement"
sidebarTitle: "Introduction"
description: "Transfer Settlement Network (TSN) separates identity, intent, and transport so stablecoin settlement is enforced cryptographically on-chain."
---

Transfer Settlement Network (TSN) is an identity-first, intent-based settlement protocol for stablecoin payments. It separates _who is authorized to pay_ (identity), _what is being settled_ (the intent and its bindings), and _who submits the transaction_ (transport), so settlement correctness is enforced on-chain and the parties that move transactions cannot alter the economic terms.

## Why TSN exists

Public-ledger payment rails carry three structural weaknesses that block real payments use cases:

<CardGroup cols={3}>
  <Card title="Address-based identity" icon="fingerprint">
    Wallets are public keys, not identities. There is no protocol-level notion of who is authorized to pay, which makes authorization, compliance, and recovery awkward.
  </Card>

  <Card title="Total transparency" icon="eye">
    Every transfer exposes sender, recipient, amount, and the accumulating graph, preventing on-chain payroll, supplier payments, and treasury flows.
  </Card>

  <Card title="Trusted bridges" icon="link-slash">
    Cross-chain value movement depends on custodial bridges and centralized oracles, single points of failure that have repeatedly lost funds.
  </Card>
</CardGroup>

TSN's thesis: settlement should be **identity-first**, **intent-based**, and **privacy-preserving**, and its correctness must never depend on the honesty of the parties that transport transactions.

## The five layers

TSN operates on Solana as the settlement and authorization domain, organized into five layers with distinct trust:

| Layer | Components | Role |
| --- | --- | --- |
| **Identity** | TIN, TIP, TIN registrar, GPRU/PRU | Private, human-meaningful payment identity |
| **Intent** | Signed payment intents (off-chain) | Declares recipient, amount, asset, nonce, validity |
| **Authorization** | Mother Escrow, permit signer, settlement DNA | Binds intent to on-chain authority |
| **Transport** | Receiver, Node, Cranker | Moves work, cannot alter settlement semantics |
| **Settlement** | Epoch Treasury, payout, TCAP credit | Executes and records the payout |

The defining property: **transport does not authorize, and authorization does not require transport to be honest.** A compromised Receiver, Node, or Cranker can delay, censor, or reorder, but cannot manufacture a valid authorized settlement.

## Core primitives

<AccordionGroup>
  <Accordion title="TIN and TIP — private identity" icon="id-card">
    A **Transfer Identity Number (TIN)** is a human-meaningful payment identity resolved privately and never written on-chain in plaintext. **TIP** cryptographically binds a TIN to its settlement context as a private commitment, so the identity graph is not reconstructable from on-chain data.
  </Accordion>

  <Accordion title="Mother Escrow and the permit signer" icon="shield-halved">
    **Mother Escrow** is the root TSN authority and epoch controller, a PDA whose stored `authority` is a governed external keypair. The **permit signer** authorizes private-settlement payouts using domain-separated Ed25519 templates (`TSN_PRIVATE_SLOT_SETTLEMENT_V1`, `TSN_PRIVATE_SLOT_REFUND_V1`) that bind program, Mother, operator, treasury, claim slot, commitment, nullifier, recipient, mint, amount, fee, lease, and expiry. Signers are governance-rotatable and never sign arbitrary messages.
  </Accordion>

  <Accordion title="Settlement DNA and replay protection" icon="dna">
    **Settlement DNA** binds payout parameters (recipient, amount, mint, nonce, digest, expiry) to an opaque slot. Nullifiers, receipts, and claim slots enforce one-time state consumption, so a settlement authorization can be executed exactly once.
  </Accordion>

  <Accordion title="Node, Receiver, Cranker" icon="network-wired">
    The **Node** is a stateless off-chain verifier with no signing authority. The **Receiver** is durable infrastructure for work state, leases, and Cranker authentication. The **Cranker** transports the exact authorized transaction and pays fees; it cannot change recipient, mint, amount, commitment, nonce, nullifier, or DNA.
  </Accordion>
</AccordionGroup>

## Cross-chain settlement without a bridge

TSN's cross-domain design uses **Creditcoin's Attestcoin Protocol** to verify a source-chain settlement fact and settle on Creditcoin without a trusted bridge.

- **Readability (live).** The Block Prover precompile (`0x0FD2`) verifies transaction inclusion and block continuity synchronously within a Creditcoin block. The consuming contract must check `status == 0x1`.
- **Writability (in development).** Messages from Creditcoin to destination chains, not yet available on testnet.
- **Current constraint.** Readability supports EVM source chains only (Ethereum Mainnet, Sepolia). Solana as a source chain is on the roadmap.

The custody model is **correspondent settlement, not a bridge**: source funds remain in the Solana Epoch Treasury, destination liquidity is a Creditcoin pool that advances funds against the verified obligation, and Attestcoin transports no funds.

See [Cross-chain architecture](/architecture/cross-chain) and [Settlement domains](/architecture/settlement-domains) for the full model.

## Privacy today and the roadmap

<Info>
  Privacy layers are **optional modes**. They wrap observation, never authorization. Mother authority, amount binding, nullifiers, and tip sequence are unchanged across modes.
</Info>

| Mode | Hides | Status |
| --- | --- | --- |
| Public (current) | — | Live |
| Confidential Token Extensions | Amounts, balances | Deployable |
| MPC confidential settlement (Arcium CSPL / MXE) | Amounts, parties, graph | Mainnet alpha |
| Helius Privacy (Rings) | Amounts, parties, graph \+ selective disclosure | Private beta |

The final on-chain payout in Public mode still exposes sender wallet, recipient wallet, amount, and graph. Closing that gap is the focus of the privacy roadmap. See [Current privacy model](/privacy/current-model), [MPC settlement](/privacy/mpc-settlement), and the [Privacy roadmap](/privacy/roadmap).

## Where to go next

<CardGroup cols={2}>
  <Card title="How it works" icon="diagram-project" href="/overview/how-it-works">
    Follow a payment intent from signature to on-chain payout through all five layers.
  </Card>

  <Card title="Trust model" icon="scale-balanced" href="/architecture/trust-model">
    See exactly what each role can and cannot do, and where trust is rooted.
  </Card>

  <Card title="Developer quickstart" icon="code" href="/developers/getting-started">
    Build against TSN: create intents, integrate the Node, and settle payouts.
  </Card>

  <Card title="Whitepaper" icon="file-lines" href="/whitepaper">
    The full protocol specification, including proofs, invariants, and status tags.
  </Card>
</CardGroup>