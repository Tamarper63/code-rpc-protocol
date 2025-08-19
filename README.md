# CODE-RPC – Developer Refactor Protocol

A **gatekeeper protocol** that enforces software best practices across **architecture**, **consistency**, **hygiene**, **coverage**, and **benchmark**.  
It works from **natural‑language prompts in GPT** (no code required in prompts) and can be wired into **CI/API** with a deterministic **JSON response contract**.

**Primary files**
- `protocol/config/code_rpc_config.json` — single source of truth (protocol config)
- `protocol/schemas/code_rpc.schema.json` — JSON Schema (Draft‑07) for self‑validation
- `protocol/config/policy.txt` — optional operational policy (recommended)

> The `name` uses an EN DASH (U+2013): `CODE-RPC – Developer Refactor Protocol`.

---

## Policy (Operational)

Place this in `protocol/config/policy.txt` (or keep it in your internal policy doc).

```
POLICY — CODE-RPC (Operational)

- No Simulation: never fabricate artifacts, results, or code. If inputs are missing → fail‑closed.
- JSON Only (validate mode): responses MUST match the response_contract; no prose/markdown.
- Deny Artifact Synthesis: do NOT create openapi/manifest/test_results/lint_report unless user explicitly requested BUILD/scaffold.
- Deny Data-URI Artifacts: no inline base64 “data:” payloads.
- Secrets: no hard‑coded tokens. Read from ENV/Vault only. Provide `.env.example` instead.
- Architecture Boundaries: enforce architecture_policy and forbid cross‑layer calls unless override is active.
- Coverage Gates: public endpoints require tests; secured endpoints require 401/403 negative tests.
- Hygiene: unused/dead/duplicate/orphan findings are CI‑blocking per config.
- Refactor Limits: obey `refactor_limits` (max files/changes) unless `force-deviation-mode` with reason+issue.
- Auditability: every override must include reason + issue link; log to `mcp_overrides.log`.
```

---

## Instructions (behavior)

In `protocol/config/code_rpc_config.json` use the following `instructions.behavior` string (passes the schema regex and adds hardening):

```json
{
  "instructions": {
    "behavior": "Always perform deep search across all files; do not simulate; verify yourself; detect dead code and duplicated logic; mark objective deviation where needed; benchmark against architecture. Return JSON only per response_contract; fail closed on missing artifacts; do not create artifacts or scaffolds; do not use data URIs; enforce refactor_limits; never cross layers unless force-deviation-mode is active and documented."
  }
}
```

This ensures:
- **deep search / do not simulate / verify yourself / dead code / objective deviation / architecture** (as required by the schema),
- plus **JSON‑only**, **fail‑closed**, **no artifact/scaffold creation**, **no data URIs**, and enforcement of **refactor limits**.

---

## Required Artifacts (fail‑closed if missing)

- `openapi_spec_yaml`
- `repo_manifest_json`
- `test_results_json`
- `lint_report_json`

### Minimal formats

**`openapi_spec_yaml`** – OpenAPI (YAML/JSON) for public endpoints, security (bearer), and responses (e.g., 401/403/429).

**`repo_manifest_json`**
```json
{
  "files": ["app/api/health.py","src/clients/app_client.py"],
  "modules": ["api.health","clients.app"],
  "clients": ["AppClient.health_check"]
}
```

**`test_results_json`**
```json
{
  "endpoints_tested": {"GET /health": true},
  "negative_tests": {"GET /secure": true},
  "coverage_pct": 80.0
}
```

**`lint_report_json`**
```json
{
  "lint_score": 9.4,
  "duplication_rate": 0.012,
  "issues": []
}
```

> Auth‑secured endpoints **must** include 401/403 negative tests. Missing coverage yields the coverage exit code below.

---

## Deterministic Exit Codes

| Code | Meaning |
|-----:|---------|
| 0 | Ok |
| 21 | Consistency Fail |
| 22 | Hygiene Fail |
| 23 | Coverage Gate |
| 24 | Scheduler Error |
| 25 | Benchmark Below Threshold |

These codes are consumed by CI to block merges on policy violations.

---

## Using the Protocol in GPT (Natural Language)

### One‑time setup (Custom GPT)
1. Create a Custom GPT.
2. Upload as Knowledge:
   - `protocol/config/code_rpc_config.json`
   - `protocol/schemas/code_rpc.schema.json`
   - *(optional)* `protocol/config/policy.txt`
3. Capabilities: **Web browsing ON**, **File upload ON**, *Code Interpreter OFF*.
4. System intent: **Do NOT create artifacts or scaffolds. Fail‑closed on missing inputs. When validating, return JSON only per the response contract.**

### What users write (no JSON in prompts)
- **Validate (default, enforcement):**  
  “Validate the attached artifacts against the protocol. Do **not** create artifacts. If something is missing, **fail closed**. Return **JSON only** per the response contract.”

- **Validate from docs link (no local artifacts):**  
  “Validate coverage & security for the API at this official documentation URL. Do **not** create artifacts. If tests/manifest are missing, fail closed and report precise findings in JSON only.”

- **(Optional) Build scaffold from docs (code files output):**  
  “Create an automation scaffold from the official docs: client, positive & 401/403/429 negative tests, `.env.example` (no secrets), and **legal templates only** for artifacts (no simulated results). Output files with full paths.”

> In **Validate** mode the assistant must not fabricate inputs; it returns a **non‑zero exit code** and findings describing exactly what’s missing.

---

## CI Wiring (example: GitHub Actions)

```yaml
name: CODE-RPC Validate
on: [pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build & Test (produce artifacts)
        run: |
          # Your pipeline should export:
          # artifacts/openapi.yaml
          # artifacts/repo_manifest.json
          # artifacts/test_results.json
          # artifacts/lint_report.json
          echo "Export artifacts from your build/test pipeline"
      - name: Enforce protocol (consume JSON & exit codes)
        run: |
          # Call your validation harness / action that invokes CODE-RPC validation
          # Fail job if exit_code != 0
          echo "Validate artifacts with CODE-RPC and enforce exit codes"
```

---

## Troubleshooting

- **Got prose instead of JSON?** Add “Return **JSON only** per the response contract.” and ensure the config/schema are attached or loaded as Knowledge.  
- **Got `0` with TODO findings?** The protocol was not applied. Re‑start a chat with the config & schema attached, or re‑load your Custom GPT knowledge.  
- **Coverage keeps failing?** Provide real test results (especially 401/403 negatives for secured endpoints) and a repo manifest with client bindings for every public endpoint.  
- **Tokens/secrets?** Never hard‑code. Use ENV/Vault and provide `.env.example` only.

---

© 2025 — CODE‑RPC Protocol
