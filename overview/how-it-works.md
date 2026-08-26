# How TSN Works

A TSN payment begins as a signed intent. The intent binds the asset, amount, recipient relationship, validity window, replay material, and settlement commitments before any relay or cranker handles it.

## The live settlement flow

1. The sender device resolves the recipient identity and signs the payment intent.
2. The Receiver stores authenticated, redacted work while the Node verifies signatures, policy, commitments, sequence, and expiry.
3. A Cranker submits the authorized funding and acceptance instructions to Solana.
4. Solana records the accepted intent and the related treasury liability atomically.
5. Mother and TSN authorize the settlement receipt.
6. TCAP validates the receipt, predecessor commitment, successor commitment, sequence, policy, and one-time nullifier.
7. The recipient's encrypted balance snapshot advances, and the owner device reads the resulting private state locally.

The Receiver, Node, and Cranker can delay or fail to submit work, but none of them may change the signed amount, asset, recipient, commitment, policy, or nullifier.

## Why the roles are separate

The owner controls authorization and private reading. Mother and governance control protocol authority. The Node verifies evidence. The Cranker pays transaction fees and submits the exact authorized transaction. TCAP records commitment transitions. Separating these roles limits the authority of any single operational component.
