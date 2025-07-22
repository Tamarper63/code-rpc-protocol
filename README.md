# CODE-RPC – Developer Refactor Protocol (v1.3)

This repository defines the strict configuration protocol for GPT-based developer agents.

## 📂 Structure

```
schemas/
  code_rpc.schema.json       # JSON Schema to validate CODE-RPC config (v1.3)
protocol/
  code_rpc_config.json       # CODE-RPC configuration file (must match schema)
```

## ✅ Key Features

- Deep full-project search before reasoning
- No simulation or stylistic guessing
- Self-verifying reasoning with consistency check
- Architecture detection and benchmarked refactor paths
- Dead code and duplication detection
- Objective Deviation flag when suggested changes break current structure
- UX fully disabled: only terse, technical responses allowed

## 🧠 New in v1.3

- `ContextTracker` tool for cross-file, cross-prompt awareness
- `memory_policy` block:
  - `architecture_cache`
  - `file_reference_tracking`
  - `refactor_state_persistence`

## 🧪 Validation Example

```bash
npx ajv validate -s schemas/code_rpc.schema.json -d protocol/code_rpc_config.json
```
