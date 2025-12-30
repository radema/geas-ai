# Technical Specification: The Trinity Lock Protocol

Version: 2.0 (GEAS Revision)

## Overview

The Trinity Lock is the cryptographic engine behind GEAS. It replaces simple file hashing with a multi-layered security model that binds Intent (Specs), Implementation (Code), and Verification (Tests) into a single, immutable chain of custody.

## The Three Pillars

The Trinity Lock is defined by the convergence of three cryptographic proofs:

| Pillar | Concept | Implementation |
| --- | --- | --- |
| Identity | Non-Repudiation | Ed25519 Signatures. Every action must be signed by a registered Public Key (Human or Agent). |
| Integrity | Physical State | Merkle Tree / Manifest. A JSON map of file paths to SHA-256 hashes. |
| Audit | Traceability | Hash Chain. Each lock references the hash of the previous lock, forming a local blockchain. |

## Data Structures

### The Identity Store ( `config/identities.yaml`)

A public registry of authorized actors. committed to the repo.

```yaml
# .geas/config/identities.yaml

identities:

- name: "arch-lead"
    role: "human"
    public_key: "ed25519_pub_..."
- name: "claude-coder"
    role: "agent"
    public_key: "ed25519_pub_..."
```

### The Code Manifest (manifest.json)

Generated dynamically during geas seal code. It represents the "Physical" state of the code.

```json
{
  "timestamp": "2025-01-01T12:00:00Z",
  "scope": "src/geas_ai",
  "files": {
    "src/geas_ai/cli.py": "sha256:a7f8...",
    "src/geas_ai/utils.py": "sha256:b2c3..."
  },
  "root_hash": "sha256:..." // The Merkle Root of the files map
}
```

### The Trinity Lock File (lock.json)

The final artifact that binds everything together.

```json
{
  "version": "2.0",
  "binding_id": "feature-login",
  "layer": "CODE", // INTENT | CODE | PROOF

  "physical": {
    "manifest_hash": "sha256:..." // Points to manifest.json (or spec files)
  },

  "audit": {
    "parent_lock_hash": "sha256:...", // Points to the previous lock (The Chain)
    "timestamp": "2025-01-01T12:05:00Z",
    "context": "Implemented OAuth logic"
  },

  "identity": {
    "signer_id": "claude-coder",
    "signature": "base64_signature_of_physical_and_audit"
  }
}
```

## Algorithms & Logic

### Key Resolution (Identity Layer)

**Goal:** securely locate the Private Key to sign operations.

**Logic:**

* Check Env Var: GEAS_AGENT_KEY_{NAME} (Best for CI/Agents).
* Check Local Keyring: ~/.geas/keys/{name}.key (Best for Humans).
* If not found $\rightarrow$ Abort("Identity not found").

**Library:** python-dotenv for env vars, cryptography.hazmat for loading keys.

### Manifest Generation (Integrity Layer)

**Goal:** create a consistent fingerprint of the source code.

**Logic:**

* Read 03_plan.md to identify target directories (Scope).
* Walk the directory tree.
* Crucial: Use pathspec to respect .gitignore rules (ignore **pycache**, .git).
* Hash each file (SHA-256).
* Sort keys alphabetically (to ensure deterministic JSON).
* Calculate Root Hash.

**Library:** pathspec (Gitignore parsing), hashlib.

### The Locking Sequence (Audit Layer)

**Goal:** Create the blockchain link.

**Logic:**

* Load Previous: file lock.json. If it exists, calculate its hash (parent_hash). If not, parent_hash = null (Genesis).
* Construct Payload: Create the JSON dictionary with Physical + Audit data.
* Sign: Sign the canonical JSON string of the payload using the Identity Key.
* Write: Save the new lock.json.

## Implementation Roadmap (Steps)

### Phase 1: Identity & Keyring

**Objective:** geas init and geas identity.

**Tech:** Generate Ed25519 keys. Create the identities.yaml schema.

**Deliverable:** Ability to sign a simple string and verify it.

### Phase 2: The Manifest Engine

**Objective:** geas seal code (Part A).

**Tech:** File walker with .gitignore support. deterministic JSON serialization.

**Deliverable:** A function that takes a folder path and returns a manifest.json.

### Phase 3: The Trinity Lock Core

**Objective:** geas seal intent / geas seal code (Part B).

**Tech:** The Lock class that combines Manifest, Identity, and Audit.

**Deliverable:** A valid lock.json file that allows geas verify to pass.

### Phase 4: Proof & Automation

**Objective:** geas prove and geas ward.

**Tech:** subprocess for running tests. watchfiles (Rust-based watcher) for the daemon.

**Deliverable:** End-to-end "Zero Friction" workflow.

### Recommended Tech Stack

**Crypto:** cryptography (Standard, audited).

**Schema Validation:** pydantic (Strict typing for Lockfiles).

**File Watching:** watchfiles (Performance).

**Ignore Parsing:** pathspec (Correctness).
