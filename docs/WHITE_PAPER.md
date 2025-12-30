# GEAS: Governance Enforcement for Agent Systems

A proposal for a protocol for the "Software Engineering 3.0" Era

## Executive Summary

GEAS (pronounced gesh) is a repository-native governance protocol designed to solve the "Day 2" problem of AI-driven development: Trust.

As software engineering shifts from Human-Authored to Agent-Generated, the bottleneck moves from velocity to verification. Organizations face a new risk: "Agent Drift," where autonomous models deviate from intent, hallucinate features, or introduce subtle bugs that bypass traditional review.

GEAS solves this by treating Agent instructions as a Binding Vow (a Geas). It enforces a strict "Filesystem Sovereignty" model where no code is accepted unless it is cryptographically linked to a sealed intent.

## The Core Philosophy

In mythology, a Geas is a magical obligation. If the hero breaks the vow, they lose their power.
In our protocol:

* The Hero(es): The AI Agent(s).
* The Vow: the technical documents (requirements, specifications, plan, etc.)
* The Power: The ability to merge code.

### The "Day 2" Reality

Current tools focus on creation. GEAS focuses on the Chain of Custody.

* Identity: Who wrote this function? (Human or Machine?)
* Integrity: Has the Spec changed since the code was written?
* Audit: Can we prove the tests passed at the moment of sealing?

## The Architecture: "Steering & Engine"

GEAS is not a SaaS platform. It is a local protocol defined by a hidden directory structure (.geas/) that lives alongside your code.

* The Steering (The Protocol): The geas CLI manages the lifecycle of "Bindings" (Units of Work). It acts as the notary.

* The Engine (The Agent): Your existing AI tool acts as the runtime. It can read the GEAS state to understand its boundaries.

## The Lifecycle of a "Binding"

GEAS replaces the generic "Ticket" with a "Binding"—a cryptographically secured folder representing a single unit of work.

### Phase I: The Ritual (Intent)

1. `geas bind <feature>`: Creates the workspace.
2. Drafting: The Human/Agent writes and review the Request and Specs.
3. `geas seal intent`: The Human signs the Spec. This creates the "Vow." The Agent cannot change the Spec from this point on without breaking the seal.

### Phase II: The Execution (Code)

1. Coding: The Agent reads the sealed Spec and generates implementation files in src/.
2. `geas seal code`: The Agent signs its work. GEAS generates a Manifest (Merkle Tree) of the generated code, proving exactly what the Agent produced.

### Phase III: The Judgment (Proof)

1. `geas prove`: The System runs the test suite (e.g., pytest).
2. Sealing: If tests pass, the logs are captured and signed. This generates the MRP (Merge Request Package), a portable proof artifact.

## The Trinity Lock

The core innovation of GEAS is the Trinity Lock. Unlike a simple file hash, the Trinity Lock binds three distinct layers into a single cryptographic record:

* Physical Integrity: "The content hasn't changed." (Merkle Tree)

* Identity: "We know exactly who authorized this." (Ed25519 Signatures)

* Audit History: "We know the sequence of events." (Hash-Chain Ledger)

## Roadmap Summary

[ ] Step 1 (Identity): Establishing the "Keyring" for Humans and Agents.

[ ] Step 2 (The Manifest): Moving from single-file locks to whole-project Merkle Trees.

[ ] Step 3 (Trinity Lock): Implementing the binding logic.

[ ] Step 4 (Evidence): Automating the generation of the MRP (Proof Package).

[] Step 5 (The Ward): Background daemons (geas ward) for friction-free governance.
