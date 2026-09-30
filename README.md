# Stratus

**Agentic AWS CloudOps platform.** Engineers describe intent in English; a fleet of specialist
agents investigates live AWS state, proposes changes, gets human approval, executes, verifies,
and writes a hash-chained audit record.

Pure Python 3.12, no agent frameworks. Azure OpenAI (`gpt-4.1`). Multi-tenant by design.

> **Status (30 Sep 2026):**
> - 25 agents and 195 tools are built; about 916 tests pass; the Azure OpenAI call is verified.
> - Not demo-ready yet: accounts bound through the API are invisible to agents, and gated writes can't complete.
> - All state is in memory, and there is no UI.
>
> The ranked gap list is in [CLAUDE.md §9](CLAUDE.md).

## Quick start

```bash
uv venv --python 3.12 .venv
uv pip install --python .venv/Scripts/python.exe -r pyproject.toml --extra dev azure-identity
cp .env.example .env        # fill in Azure OpenAI endpoint, key, deployment names
.venv/Scripts/python.exe -m pytest -q --no-cov -m "not evals"
.venv/Scripts/python.exe -m uvicorn stratus.api.app:app --port 8000   # → http://localhost:8000/v1/docs
```

Local auth: `Authorization: Bearer dev.<tenant_uuid>.<user_uuid>.owner,operator,approver`.

## What it does

| Mode | Endpoint | What happens |
|---|---|---|
| **Connect** | `POST /v1/accounts` → deploy CFN → `POST /v1/accounts/{id}/verify` | Read-only cross-account role with a per-tenant ExternalId. No access keys, ever. |
| **Ask** | `POST /v1/ask` (SSE) | Routed to one agent, read-only tools, cited answer. |
| **Act** | `POST /v1/act` (SSE) | Supervisor plans a DAG. Agents investigate, then propose writes. Risk, policy and approval gate each write. |
| **Automate** | `/v1/workflows`, `/v1/workflows/design` | YAML pipelines, authored by conversation or by hand, with branching and approval gates. |

## Documentation

| Doc | Contents |
|---|---|
| **[CLAUDE.md](CLAUDE.md)** | Build contract and as-built map: rules, end-to-end flows, endpoints, **known gaps** |
| [docs/agents-and-tools.md](docs/agents-and-tools.md) | Agent roster, tool contract, inventory |
| [docs/governance-and-security.md](docs/governance-and-security.md) | Risk, policy, approvals, ledger, AWS access, onboarding |
| [docs/pipelines.md](docs/pipelines.md) | Workflows, `when:` grammar, design sessions, ticketing |
| [docs/memory-and-evaluation.md](docs/memory-and-evaluation.md) | Memory fabric, context engine, self-learning, evaluation |
| [docs/mcp.md](docs/mcp.md) | AWS MCP servers, hardening, OpenShift hosting |
| [docs/compliance.md](docs/compliance.md) | Compliance agent design spec and RBI 2026 check catalogue |
| [docs/research.md](docs/research.md) | Competitive landscape |
| [docs/roadmap.md](docs/roadmap.md) · [docs/plan-and-timeline.xlsx](docs/plan-and-timeline.xlsx) | Timeline, milestones, status |
| [docs/stratus-diagrams.drawio](docs/stratus-diagrams.drawio) | 24 diagram pages. Read [docs/README-diagrams.md](docs/README-diagrams.md) first. |
