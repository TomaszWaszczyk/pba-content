---
title: Overview of Blockchains
description: Overview of blockchains for Web3 engineers
duration: 1 hour
instructors: ["Joe Petrowski"]
teaching-assistants: ["Dan Shields"]
---

# Overview of Blockchains

---

<widget-speaker name="Joe Petrowski" position="Common Good Parachains Team Lead at Web3 Foundation" image="../../../assets/img/0-Shared/people/joe.png" github=" joepetrowski" twitter="joepetrowski" linkedin="joe-petrowski-73538929" matrix="joe:web3.foundation"></widget-speaker>

---

## Upholding Expectations

What is the core problem we want to solve?

Notes:

Potential for some discussion here, engage the audience.

---

## Comparison with Cryptography

Cryptography provides strong guarantees about _messages_ without a trusted party, regardless of the conduit over which a message was transported.

---

## Web 1

Users generally transmit information peer-to-peer.

Cryptography upholds its guarantees when used like this.

---

## Web 2

"Send _us_ your information."

Platforms started offering digital services.

However, users must place faith in the service provider.
Any cryptographic guarantees are about interactions with the service provider, not peers.

---

## Web3

A provision of digital services without the need to trust a service _provider_.

---

## Desired Properties

### _Permissionless access_

Anyone should be able to access and interact with the system.

---

## Desired Properties

### _Privacy_

Users should have credible expectations about what information they give up about themselves.

---

## Desired Properties

### _Authenticity_

Users should have credible expectations about the messages they see, regardless of the platform the messages are on.

---

## Desired Properties

### _Finality_

Users should be able to form credible expectations about when a state transition is final.

---

## Desired Properties

### _Behavior_

The system should behave as expected, even if system operators do not.

---

## Desired Properties

### _Unstoppability_

No individual actor, company, state, or coalition should be able to degrade any of these properties.

---

## Trust

What is trust in this context? Blind faith.

---

## Trust Example

Two users on Twitter:

- Trust that we are seeing information from the same database\*
- Trust that if a tweet is from X, then X wrote that tweet\*
- Trust that others see our messages as from us\*
- Trust that the messages we see are the messages the users wrote\*
- Trust that we're interacting with the application as equals

Notes:

- Cryptography actually provides a lot of these guarantees, but not when an intermediary has stepped in between users.
- This is one example, but class should discuss.
  Also possible activity.

---

## Digital Services

People rely on digital services every day.
They are inescapable (and valuable).

- Twitter, Instagram, Facebook, etc.
- Journalism and sources
- Banks
- Lawyers, notaries, regulators

Notes:

- Ask class for more examples

---

## Disintermediation

Removing trust allows us to unpackage applications.

When users have credible guarantees that they are interacting with the same data and logic, many applications can be built on the same system.

---

## Application Disentanglement

<img style="width: 800px" src="../../../assets/img/3-Blockchain/3.1-application-disentanglement.png"/>

Note:

The idea here is to discuss how applications are often seen as an entire bundle: e.g.
Instagram is the database, the algorithms, the UX.
But when we have credible expectations that we're interacting with the same system, rules, data, it's possible to build lots of ways to access and interact with the system.
It also removes the need for a central authority to deal with all appeals/complaints from various users.

---

## Desired Approach

Allow users to interact with a common system without trusting any intermediaries.

Opens the door to new application stacks:

- Shared state and state change rules
- Custom rendering, moderation, interfaces

---

## State Machine

<img style="width: 900px;" src="../../../assets/img/3-Blockchain/3.1-state-machine.png"/>

---

# Blockchain, Finally

---

## Just Kidding

Blockchains were not the first attempt at distributed state machines.

- Redundant systems in mission-critical engineering
- Data centers
- High availability applications

---

## Constraints

However, most of these examples assume benevolent participants.
And an admin.

Blockchains are adversarial, with no admins.

---

## Blockchains

<widget-columns>
<widget-column>

<center>

**Consensus**

How does the state advance?

Which changes are final?

</center>
</widget-column>
<widget-column>
<center>

**State Transition Function**

What does the state hold?

What are the _rules_ to change it?

</center>
</widget-column>
</widget-columns>

---

## State Transition Function

A state transition function (STF) defines:

| Data Stored   | API & Behavior |
| :------------ | :------------- |
| Account Info  | Function calls |
| Balances      | Implementation |
| Authority Set | Responses      |
| Timestamp     |                |
| &c.           |                |

---

## Example: A Simple Storage Item Write

```rust
/// The ideal number of staking participants.
#[pallet::storage]
#[pallet::getter(fn validator_count)]
pub type ValidatorCount<T> = StorageValue<_, u32, ValueQuery>;

/// Sets the ideal number of validators.
#[pallet::call]
impl<T: Config> Pallet<T> {
	pub fn set_validator_count(origin: OriginFor<T>, new: u32) -> DispatchResult {
		ensure_root(origin)?;
		ValidatorCount::<T>::put(new);
		Ok(())
	}
}
```

Notes:

This example is from Substrate, which we will cover more in depth in the next module.

---

## Anatomy

<img style="width: 1100px" src="../../../assets/img/3-Blockchain/3.1-anatomy.png"/>

---

## Blocks

- Includes a header and a body.
- Header includes consensus-related info: number, parent hash, some digests.
- Body contains an ordered set of _extrinsics_: Packets from the outside world with _zero_ or more signatures attached.

---

## Blocks in Substrate

```rust
/// Abstraction over a Substrate block.
pub struct Block<Header, Extrinsic: MaybeSerialize> {
	/// The block header.
	pub header: Header,
	/// The accompanying extrinsics.
	pub extrinsics: Vec<Extrinsic>,
}
```

Notes:

This example is also from Substrate, we will cover more in depth in the next module.

---

## Headers

Consensus-related info. Varies per blockchain, but in Substrate:

- Parent hash
- Number
- State root
- Extrinsics root
- Digest

The parent hash links blocks together (cryptographically linked list). The other info is handy for other infrastructure and applications (more on that later).

---

## Other Headers

<widget-columns>
<widget-column>

<center>

**Bitcoin**

</center>

- Version
- Previous Hash
- Tx Merkle Root
- Time
- N_Bits
- Nonce

</widget-column>
<widget-column>

<center>

**Ethereum**

</center>

- Time
- Block NUmber
- Base Fee
- Difficulty
- Mix Hash
- Parent Hash
- State Root
- Nonce

</widget-column>
</widget-columns>

---

## Extrinsics

Packets from the outside world with _zero_ or more signatures attached.

- Function calls to the STF
- Some functions require signatures (e.g., transfer some tokens)
- Others don't, but usually have some validation means

---

### Consensus

Whole next lecture is dedicated to consensus, going to stay strictly conceptual here.

---

## Liveness

- Property that ensures state transitions will happen.
  The system should be _available_.
- Someone needs to propose a state transition (within a block) and a resultant state.
- Motivation for actors to do so?

Notes:

Class Discussion: What are some motivations?

---

## Liveness Examples

- Static leader
- Round robin
- Proof of Work
- Random assignment protocols

---

## Safety

Once a state transition occurs, under what conditions can users have a credible expectation that it will never revert?

- Probabilistic
- Deterministic

---

## Safety Examples

- Longest chain
- Most difficulty
- Practical Byzantine Fault Tolerance (PBFT) (per state transition)
- PBFT (on batches)

---

## Deterministic Finality

What does is mean when a final block is reverted?

---

## View

No node has an omniscient view of the network.
Therefore, one node may always have more information than another node.

But they should never believe an incongruity.

Example: I believe that block $B$ is final.
You may not see $B$ as final _yet_, but you should never believe that a chain that excludes $B$ is final.

---

## Security

What actually backs up the system's guarantees?

- Lazy: Omnipotence (AWS, Twitter, Bank)
- Some "proof" of authority, as defined within the system itself
  - Wasting energy (trivial)
  - Putting stake at risk
  - Being elected by participants via their willingness to put their own stake at risk

---

## Punishment

If some of the authorities in a system fail to uphold their guarantees, the system should have a way to hold them accountable.

In blockchains, this is usually accomplished via either lack of reward or explicit seizure/destruction.

---

## Unscrupulous Actors

**The _system_ should uphold its credibility even if individual participants do not.**

| Technical Design               | Economic Design    |
| ------------------------------ | ------------------ |
| Key choices                    | Cost of misuse     |
| Public API                     | Fees & Deposits    |
| Hash choices                   | Slashing & Rewards |
| Stored data                    |                    |
| Transactional/Autonomous Logic |                    |
| Authorship & Censorship        |                    |

---

## STF Upgrades

All systems need to upgrade to stay relevant.

Bug fixes, new functionality, new information.

This can be tricky in blockchains...

Note:

We'll be getting to this soon and also in 3.4.

---

# Short History Lesson

---

## Bitcoin

<img style="width: 900px;" src="../../../assets/img/3-Blockchain/3.1-bitcoin-blocks.png"/>

Uses an unspent transaction output (UTXO) model & Proof of Work (PoW) consensus.

Notes:

Source: [Bitcoin white paper](https://bitcoin.org/en/bitcoin-paper)

---

## Ethereum

- Generalisation of Bitcoin: provides a quasi-Turing-complete VM
- Uses an account-based system
- Accounts can store balances, but can also store executable code (smart contracts)
- Each contract can have its own internal state and API

---

## Proof of Stake

Ethereum uses Proof of Work, but planned\* to transition to Proof of Stake.

Many new protocols came out in the coming years with suggested implementations of PoS (NXT, Ouroboros).

Covered more in depth in the next lecture.

---

## Hard Forks

Historically, upgrading blockchains meant getting everyone to update their node software.

"This had made many people very angry and has been widely regarded as a bad move."

---

<img style="width: 500px;" src="../../../assets/img/0-Shared/logo/Polkadot_Pink-White.svg"/>

Notes:

Wrap up with some notes about Polkadot

---

## Consensus Over the STF

Who actually defines the STF?

In Substrate chains (like Polkadot), it's part of the state itself!

`:CODE => <some-wasm-executable>`

---

## Many State Machines

- If one state machine with such guarantees is useful, people will probably want more.
- But security can be diluted.
- Polkadot is designed to _host_ a large number of sovereign state machines.
  We call them parachains.

---

## Specialisation

Different state machines will specialise in different things (assets, privacy, credentials, social media).

But applications will want to pull from many of these capabilities.

So these state machines should have a messaging protocol to interact with each other.

---

## Substrate

Practically, these parachains will have a lot in common:

- Similar needs for accounting units, databases, RPCs, etc.
- All need to "speak the same language", i.e. compile to Wasm

Substrate was created to make this part easy.

---