# SIP-028: sBTC Signer Criteria

**SIP Number:** 028  
**Title:** Signer Criteria for sBTC, A Decentralized and Programmable Asset Backed 1:1 with BTC  
**Consideration:** Technical, Economics  
**Type:** Standard  
**Status:** Draft  
**Authors:**  
- Mårten Blankfors ([marten@trustmachines.co](mailto:marten@trustmachines.co))  
- Daniel Jordon ([daniel@trustmachines.co](mailto:daniel@trustmachines.co))
- Friedger Müffke ([friedger@ryder.id](mailto:friedger@ryder.id))  
- Jesus Najera ([jesus@stratalabs.xyz](mailto:jesus@stratalabs.xyz))  
- Jude Nelson ([jude@stacks.org](mailto:jude@stacks.org))  
- Tycho Onnasch ([tycho@zestprotocol.com](mailto:tycho@zestprotocol.com))  
- Andre Serrano ([andre@bitcoinl2labs.com](mailto:andre@bitcoinl2labs.com))  
- Ashton Stephens ([ashton@trustmachines.co](mailto:ashton@trustmachines.co))  
- Joey Yandle ([joey@trustmachines.co](mailto:joey@trustmachines.co))  

## Abstract

This SIP a new wrapped Bitcoin asset, called sBTC, which would be implemented on Stacks 3.0 and later as a SIP-010 token. Stacks today offers a smart contract runtime for Stacks-hosted assets, and the forthcoming Stacks [3.0 release](https://github.com/stacksgov/sips/blob/main/sips/sip-021/sip-021-nakamoto.md) provides lower transaction latency than Bitcoin for Stacks transactions. By providing a robust BTC-wrapping mechanism based on [threshold signatures](https://eprint.iacr.org/2020/852.pdf), users would be able to lock their real BTC on the Bitcoin chain, instantiate an equal amount of sBTC tokens on Stacks, use these sBTC tokens on Stacks, and eventually redeem them for real BTC at 1:1 parity, minus the cost of the relevant blockchain transaction fees.

This is the first of several SIPs that describe such a system. This SIP describes the threshold signature mechanism and solicits from the ecosystem both a list of signers and the criteria for vetting them. These sBTC signers would be responsible for collectively holding all locked BTC and redeeming sBTC for BTC upon request. Given the high-stakes nature of their work, the authors of this SIP believe that such a wrapped asset can only be made to work in practice if the Stacks ecosystem members can reach broad consensus on how these signers are chosen. Thus, the first sBTC SIP put forth for activation concerns the selection of sBTC signers.

This SIP outlines but does not describe in technical detail the workings of the first sBTC system. A separate SIP will be written to do so if this SIP successfully activates.

## Introduction

### Glossary

| Term                | Definition                                                                                                                                                            |
|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **SIP-10 Token**    | A token on the Stacks blockchain that adheres to the fungible token standards outlined in [SIP-10](https://github.com/stacksgov/sips/blob/main/sips/sip-010/sip-010-fungible-token-standard.md).                                                                       |
| **sBTC**            | A SIP-10 token on the Stacks Blockchain that can be turned back into BTC on the Bitcoin Blockchain. 1 sBTC is equivalent to 1 BTC on the Bitcoin Blockchain.           |
| **sBTC operation**  | An smart contract function call that initiates some action from the sBTC protocol.                                                                                                        |
| **.sbtc contract**  | A smart contract (or a collection of contracts) defining the sBTC token and functions related to it.                                                                    |
| **sBTC Peg Wallet** | The single UTXO holding the entire BTC balance that’s pegged into sBTC. This peg wallet is managed and maintained by the sBTC Signers.                                  |
| **Stacks Signer**   | An entity that receives PoX payouts for stacking their STX tokens and actively participating in the Stacks protocol by signing mined blocks.                            |
| **sBTC Signer**     | An entity that will sign sBTC operations and communicate with contracts on the chain to make that feasible. This entity has partial access to spending the sBTC UTXO.   |
| **sBTC Signer Set** | The set of all sBTC signers. Each is registered with the .sbtc contract and the transfer. These entities as a group have full democratic access to the sBTC UTXO.      |
| **sBTC Signer API** | An API exposed by the sBTC Signer that handles basic low-level commands.                                                                                                |
| **Deposit API**     | A third party API that communicates with the sBTC Signers via the sBTC Signer API.                                                                                      |

## Problem Statement

Bitcoin Script's computational expressiveness is limited, such that developers who want to build non-trivial decentralized applications must first build and maintain non-trivial off-chain services to make up the difference. Namely, because Bitcoin Script programs can neither store nor read arbitrary chain state (up to VM-imposed size limits), applications that maintain state across Bitcoin transactions must not only provide the means of storing it themselves but also somehow make it available to subsequent Bitcoin transactions.

Doing this in an open-membership peer-to-peer setting has been shown to be a difficult task (given the size and complexity of systems like Lightning, Taro, Bisq, and BitVM), which imposes a high barrier to entry for building decentralized applications on Bitcoin. Our key insight is that existing applications built on Bitcoin have built up most of the workings of an L2 blockchain like Stacks, but have done so implicitly within their interior components. This SIP makes this design choice explicit: the act of building non-trivial applications on Bitcoin is the act of building on a Bitcoin L2, and therefore the act of providing a rich programming environment for BTC is the act of implementing a wrapped BTC asset (sBTC) on a Bitcoin L2 with smart contracts. Indeed, this has been realized already with systems like Rootstock's RBTC.

## Proposed Solution

sBTC aims to mitigate Bitcoin’s limitations by combining the capability of the Stacks Blockchain with the reliability and security of Bitcoin. By enabling the secure movement of BTC in and out of the Stacks Blockchain via the sBTC protocol, users can interact with their BTC on Stacks using Clarity smart contracts which will benefit from faster block times than Bitcoin. The protocol is “secure” in that it is operated by a decentralized signer network, removing the risk of a single point of failure and trust in a single custodian. Users can deposit BTC into the protocol, seamlessly transact using sBTC on the Stacks blockchain, and have the freedom to redeem sBTC tokens for the underlying BTC at any time.

### Programmability

[Clarity](https://docs.stacks.co/clarity/overview) is the smart contract language on Stacks, which allows developers to encode essential business logic on a blockchain. Using smart contracts, developers can build more expressive decentralized applications that interact with sBTC, such as DeFi protocols, stablecoins, payments, and many others.

### Fast Blocks

The Stacks Nakamoto Upgrade, proposed in [SIP-021](https://github.com/stacksgov/sips/blob/main/sips/sip-021/sip-021-nakamoto.md#proposed-solution), enables fast blocks where user-submitted transactions will now take on the order of seconds, instead of tens of minutes. Thus, sBTC on Stacks Nakamoto will offer an improvement to Bitcoin’s current transaction times.

The sBTC protocol not only addresses the limitations of Bitcoin's scripting system but also provides a secure and decentralized solution for utilizing Bitcoin in various applications.

## Design

While the first sBTC implementation is under development, the wrapped nature of the sBTC token means that any such system would have the following properties:

- sBTC is a SIP-10 token backed 1:1 by BTC.
- The sBTC peg wallet is maintained by the set of sBTC signers. These signers are responsible for the security and maintenance of the wallet, ensuring that sBTC is redeemable for BTC.
- Bitcoin can be converted into sBTC within 3 Bitcoin blocks, and sBTC can be converted into Bitcoin within 6 Bitcoin blocks. sBTC relies on the forking behavior guaranteed by [SIP-021](https://github.com/stacksgov/sips/blob/main/sips/sip-021/sip-021-nakamoto.md) in order to maintain the peg wallet correctly across forks.

## Specification

Management of the sBTC peg wallet on the Bitcoin blockchain shall be managed by the proposed set of signers through a democratic process, involving the sBTC Signer Set rather than a single custodian. At launch, the sBTC protocol will be maintained by 15 independent entities that make up the sBTC Signer Set. The eligibility criteria to become an sBTC Signer is determined through the community governance process of ratifying this SIP.

sBTC Signers are responsible for accepting or rejecting all sBTC deposit and withdrawal operations submitted to the network. For a transaction to be fulfilled, at least 70% of the signers need to approve the transaction. This means that the liveness and reliability of the signers is crucial to the success of the protocol. The system is live ("resilient") if at least 70% of the sBTC Signer voting power are online and honest. Then (and only then), deposits and withdrawals happen in a timely manner. The system is safe ("trustworthy") if at least 30% of the sBTC Signer voting power is honest. Then, no theft of funds can occur.

Throughout this release, sBTC will have an unchangeable and distinct signer set that will not explicitly be part of Stacks consensus. As a result, this release can activate without a hard fork. Each unique signer receives exactly one vote. Given there are 15 unique signers that comprise the system, it would require at least 11 out of 15 signatures for an sBTC operation to be fulfilled.

While up to 30% of the signers can be offline without a user impact on the functioning of the protocol, it becomes more critical for the rest of the signers to approve sBTC operations because operations necessarily still need to meet 70% of the original signing power. If more than 30% of signers become unavailable, no sBTC operations will be approved because it will be impossible to get 70% approval when less than 70% are online. An operation that isn’t approved will become spendable by the user after a predetermined timeout has elapsed, measured in Bitcoin blocks.

### sBTC Signer Responsibilities

Overview of tasks the sBTC signers carry out:
- Accept requests to deposit BTC.
- Fulfill requests to withdraw BTC.
- Complete deposit and withdrawal requests in a timely manner.
- Maintain industry standard operational security around any hosts and private data (to include private keys).
- Move BTC to a new UTXO when private keys are rotated.
- Signers must perform UTXO consolidate as it is deposited [1].
- Signers must deduct transaction fees from users in order to fund BTC withdrawal transactions:
  - Ensure that the transaction fee is paid for (e.g., they deduct it from the user, and they set a minimum sBTC withdrawal amount).
  - Transaction fee must be estimated proportionally for the requested operation [2].
- Collectively, the signers must coordinate to calculate and advertise the fee parameters of the system:
  - A minimum sBTC peg-out.
  - STX transaction fee for minting the sBTC. This fee is paid by the user and can be sponsored by a 3rd party, which is described in the [Auxiliary Features](#auxiliary-features) section of this document.

### sBTC Signer Eligibility Criteria

Signers will run the sBTC binary in addition to the core Stacks signer software and must meet certain criteria in order to facilitate the reliable functioning of the sBTC protocol at all times.

The following eligibility criteria will be used to identify the sBTC Signers:

- **Company Standing:**  
  Does the company have an operating history to demonstrate their experience and reliability?
- **Stacks Participation:**  
  Has the Signer participated in running a signer node on Stacks 2.5 testnet or mainnet?
- **Network Uptime:**  
  Does the Signer agree to use reasonable efforts to maintain >99% uptime on the sBTC Signer?  
  *Note: This metric will be self-affirmed by the company.*
- **Communication & Availability:**  
  Does the signer have a direct communication channel set up with sBTC core engineers to be able to respond to urgent updates within 24 hours? (e.g., Slack, Telegram, Signal)
- **Ecosystem Alignment:**  
  Has the signer made contributions to Bitcoin or the Stacks network over the past year that demonstrate their commitment to the growth and success of the network? Examples include, but are not limited to: publishing independent research, marketing, co-authoring a SIP, submitting a Stacks pull request, providing feedback on Stacks core development, or contributing to a Stacks working group.

The criteria described above will be used to identify sBTC Signers that are able to meet some or all of the responsibilities described in the previous section.

### Selection Process

The sBTC Signer Set will be finalized from the list of eligible Signers, based on the above criteria, by the [sBTC working group](https://github.com/orgs/stacks-network/discussions/469). This process is to ensure that the sBTC Working Group can adjust to any changes in the sBTC Signer Set quickly and without the overhead of an additional community vote. For example, if an sBTC Signer is no longer available to complete their responsibilities, it is imperative for the liveness of the system that the sBTC Working Group is able to implement a suitable replacement based on the above criteria.

## Related Work

### [WBTC](https://wbtc.network/assets/wrapped-tokens-whitepaper.pdf)

WBTC is a closed membership system. It is made up of 50+ merchants and custodians with keys to the WBTC multisig contract on Ethereum. WBTC deposits and withdrawals can only be performed by the authorized merchants, and end users purchase WBTC directly from the merchants. Although the merchants manage issuance and redemption, all BTC backing WBTC is held by a single custodian.

### [tBTC v2](https://whitepaper.io/document/691/tbtc-whitepaper)

tBTC is an open membership system, where the BTC is managed by a rotating set of randomly selected nodes which manage a threshold wallet. The system requires that 51-of-100 randomly selected wallet signers must collaborate to produce a proper signature.

### [RBTC](https://rootstock.io/static/a79b27d4889409602174df4710102056/RS-whitepaper.pdf)

rBTC, Rootstock’s (RSK) 2-way peg protocol, called “the Powpeg,” is a closed membership system. Peg operations settle to Bitcoin via merge mining on the RSK side-chain. Instead of collateralizing the system with a new token, peg operators are incentivized by earning a portion of transaction fees. PowPeg operators keep specialized hardware called PowHSMs active and connected to special types of Rootstock full nodes. Since the Bitcoin blockchain and the Rootstock sidechain are not entangled in a single blockchain or in a parent-child relation, peg-in and peg-out transactions require a high number of block confirmations. Peg-ins require 100 Bitcoin blocks, and peg-outs require 4000 Rootstock blocks.

## Activation

sBTC is designed to activate on Stacks Nakamoto as defined in [SIP-021](https://github.com/stacksgov/sips/blob/feat/sip-021-nakamoto/sips/sip-021/sip-021-nakamoto.md). Therefore, this SIP is only meaningful when SIP-021 activates. The sBTC Working Group plans to observe at least 2-4 weeks of network behavior on Stacks Nakamoto to ensure a stable release. After this period, sBTC can be activated on the Stacks network without requiring a separate hard fork.

### Process of Activation

Users can vote to approve this SIP with either their locked/stacked STX or with unlocked/liquid STX, or both. The criteria for the stacker and non-stacker voting is as follows.

#### For Stackers:

In order for this SIP to activate, the following criteria must be met by the set of Stacked STX:

- At least 80 million Stacked STX must vote at all to activate this SIP.
- Of the Stacked STX that vote, at least 66% of them must vote "yes."

The voting addresses will be:

- **Bitcoin Yes Address:** `3Jq9UT81fnT2t24XjNVY7wijpsSmNSivbK`  
- **Bitcoin No Address:** `3QGZ1fDa97yZCXpAnXQd6JHF4CBC6bk1r4`  
- **Stacks Yes Address:** `SP36GHEPEZPGD53G2F29P5NEY884DXQR7TX90QE3T`  
- **Stacks No Address:** `SP3YAKFMGWSSATYNCKXKJHE2Z5JJ6DH88E4T8XJPK`  

which encode the hashes of the following phrases into bitcoin / stacks addresses:

- **Yes to A Decentralized Two-Way Bitcoin Peg**
- **No to A Decentralized Two-Way Bitcoin Peg**

Stackers (pool and solo) vote by sending a stacks dust to the corresponding stacks address from the account where their stacks are locked.

Solo stackers only can also vote by sending a bitcoin dust transaction (6000 sats) to the corresponding bitcoin address.

#### For Non-Stackers:

Users with liquid STX can vote on proposals using the Ecosystem DAO. Liquid STX is the user’s balance, less any STX they have locked in the PoX stacking protocol, at the block height at which the voting started (preventing the same STX from being transferred between accounts and used to effectively double vote). This is referred to generally as "snapshot" voting.

For this SIP to pass, 66% of all liquid STX committed by voting must be in favor of the proposal. This precedent was set by [SIP-015](https://github.com/stacksgov/sips/blob/feat/sip-015/sips/sip-015/sip-015-network-upgrade.md).

The act of not voting is the act of siding with the outcome, whatever it may be. We believe that these thresholds are sufficient to demonstrate interest from Stackers -- Stacks users who have a long-term interest in the Stacks blockchain's successful operation -- in performing this upgrade.

## Appendix
[1] https://github.com/stacks-network/sbtc/issues/52

[2] https://github.com/stacks-network/sbtc/pull/186

### Specification

#### Deposits

The main steps of the sBTC Deposit flow will be as follows:

1. **Deposit request:** A bitcoin holder creates a transaction on Bitcoin.
2. The deposit transaction contains a UTXO (deposit UTXO) spendable by sBTC Signers, with an OP_DROP payload.
3. The payload contains the recipient address of the sBTC among other relevant info for the deposit.
   - The relevant info could contain a fee suggestion or max_fee.
4. **Proof of deposit:** The bitcoin holder submits a proof of deposit on Stacks by invoking the Deposit API.
5. **Deposit accept:** 
6. **Deposit redeem:** The sBTC Signers redeem the deposit by consuming the deposit UTXO, consolidating it into the sBTC UTXO.
7. **Mint:** The sBTC Signers finalize the deposit acceptance making a Clarity contract call that mints the sBTC on the Stacks Layer.

#### Withdrawals (Redeeming sBTC)

The main steps of the sBTC withdrawal flow are as follows:

1. **Withdrawal request:** An sBTC holder calls the `withdraw-request` function in the `.sbtc` contract.
   - This transfers the requested amount of sBTC to the `.sbtc` contract & mints the user a non-transferable locked-sBTC as a placeholder.
2. **Withdrawal accept:** If accepted, the following happens:
   - The signers create a transaction on Bitcoin which returns the requested amount to the designated address.
   - Once the Bitcoin transaction is confirmed, the signers make a smart contract call to one of the `.sbtc` contracts to mark the transaction as fulfilled.
   - If successful, the resulting Stacks transaction will record the withdrawal request as complete & will accordingly burn the user’s locked-sBTC.
3. **Withdrawal reject:** If instead the request is rejected, the sBTC signers will call the `withdraw-reject` function in the `.sbtc` smart contract. This function does the following:
   - Returns the sBTC to the holder.
   - Records the signer votes.

### Auxiliary Features

Auxiliary features of the sBTC protocol are described below.

#### Stacks Transaction Fee Sponsorship

sBTC will include the option to have sBTC transactions on Stacks be sponsored in return for some sBTC. Using the approach suggested in this [issue](https://github.com/stacks-network/stacks-core/issues/4235), sBTC users will be able to pay for their transaction fees in sBTC with support from an existing STX holder, provided the wallet supports it. The proposed solution introduces atomic transaction bundles on Stacks, which enable sBTC payments to sponsors for covering STX transaction fees. STX is maintained as the sole gas token, but the user only has to interact with sBTC.

#### Signer Key Rotation

Mechanisms are provided for the scenario where a signer wants to rotate their key. For this to happen, signers must coordinate offline and vote on-chain on the new signer set (aka set of keys). Once the new signer set is determined, the signers conduct a wallet handoff and re-execute the key generation event.