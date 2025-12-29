# Getting Started with PACT-AI

Welcome to **PACT-AI (Protocol for Agent Control & Trust)**, the governance layer for **Software Engineering 3.0**. PACT ensures that AI agents operate within a controlled, "Spec-First" environment, where every action is anchored in a cryptographically sealed blueprint.

## 🛠 Prerequisites

- **Python**: 3.10 or higher.
- **uv**: We recommend using [uv](https://github.com/astral-sh/uv) for fast and reliable package management.

## 🚀 Installation

Install PACT-AI using `uv`:

```bash
uv pip install pact-ai
```

Alternatively, you can run it directly without a permanent installation:

```bash
uv run pact --help
```

## 🏗 Initializing Your Project

To bootstrap PACT in your repository, run:

```bash
pact init
```

This command creates a `.pacts/` directory containing:

- `config/agents.yaml`: Defines your AI team personas (Architect, Developer, QA, etc.) and their public keys.
- `config/models.yaml`: Configures your LLM providers.
- `bolts/`: The directory where all units of work (Bolts) will reside.
- `active_context.md`: A pointer file indicating the current active Bolt.

## ⚡️ The PACT Workflow

PACT enforces a rigorous lifecycle for every feature or bug fix, organized as a **Bolt**.

### 1. Create a New Bolt

Start a new unit of work:

```bash
pact new feature-name
```

This creates a folder in `.pacts/bolts/feature-name/` and sets it as the active context.

### 2. The Blueprinting Process

Every Bolt follows a standard sequence:

1. **Request (`01_request.md`)**: Define the user's intent or feature request.
2. **Specs (`02_specs.md`)**: The **Spec Writer** agent translates the request into technical requirements.
3. **Plan (`03_plan.md`)**: The **Architect** agent designs the implementation plan.

### 3. Sealing the Blueprint

Before any code is written, the artifacts must be "sealed" to ensure integrity:

```bash
pact seal specs
pact seal plan
```

Sealing hashes the content and records it in `approved.lock`. Once sealed, PACT enforces that these files cannot be modified without being explicitly unsealed.

### 4. Implementation and Verification

1. **Code**: The **Developer** executes the approved plan.
2. **MRP (`mrp/summary.md`)**: The **QA Engineer** verifies the output against the specs and seals the Master Release Plan.

## 🛡 Verification and Status

At any point, you can check the status of your current Bolt:

```bash
pact status
```

Or verify the cryptographic integrity of all sealed artifacts:

```bash
pact verify
```
