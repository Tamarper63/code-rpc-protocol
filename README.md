# CODE-RPC – Developer Refactor Protocol (v1.4)

This repository defines version 1.4 of the CODE-RPC protocol for GPT-based developer agents.

## 📂 Structure

```
schemas/
  code_rpc.schema.json             # JSON Schema for validation
protocol/
  code_rpc_config.json             # Protocol configuration (v1.4)
instructions.txt                   # GPT Instructions to paste in setup
```

## ✅ What's New in v1.4

- 🔧 `module_boundary_policy`: enforce cross-layer discipline
- 🧠 `design_state_tracking`: track current architecture and detect shifts
- 📐 `refactor_scope_control`: limit number of files and boundary violations
- 🧪 `TestSurfaceAnalyzer`: detect structural impact on test coverage
- 🔗 `DependencyResolver`: maps cross-module dependency trees
- ⚡ `advanced_trigger_overrides`: enable conditional triggers such as:
  - `[force-deviation-mode]`
  - `[regression-scan]`
  - `[arch-consistency-check]`

## 🧠 GPT Instructions

To recreate this GPT in OpenAI ChatGPT:

1. Go to https://chat.openai.com/gpts/new
2. Paste the contents of `instructions.txt` into the **Instructions** field
3. Upload the following:
   - `protocol/code_rpc_config.json`
   - `schemas/code_rpc.schema.json`

## 🧪 Validation

Validate configuration using [AJV](https://ajv.js.org/) (or similar JSON Schema validator):

```bash
npx ajv validate -s schemas/code_rpc.schema.json -d protocol/code_rpc_config.json
```

## 🧠 Prompt Trigger Examples

```text
[force-deviation-mode] :: Refactor even if breaking layered structure
[regression-scan]      :: Look for reintroduced logic bugs in last commit
[arch-consistency-check] :: Validate controller-service-di alignment
```

## 📌 Version

This is version 1.4 – schema-enforced, memory-aware, boundary-sensitive refactor agent configuration.