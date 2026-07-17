---
type: Reference
title: Aeon × ModMe Project Guide
description: Operating guide for the Aeon autonomous framework + ModMe UI unified stack — conventions, workflow, and quality gates for contributors and the Copilot Cloud Agent.
tags: [copilot, modme, aeon, architecture, conventions]
---

# Aeon × ModMe Project Guide

This guide defines how to work within the Aeon × ModMe unified stack. It covers architectural priorities, conventions, and the step-by-step implementation workflow expected of any contributor — human or agent.

For the authoritative Copilot Cloud Agent instructions (surfaced automatically in Copilot), see [`.github/copilot-instructions.md`](../.github/copilot-instructions.md).

---

## Repo Priority Model

When making decisions, apply this precedence order:

1. **`aeonfun/aeon`** — agent execution, skill lifecycle, memory, and run constraints.
2. **`Ditto190/modme-ui-01`** — UI architecture, component semantics, UX flow, and product behavior.
3. **Reference repos** — patterns only when (1) and (2) are silent.

---

## Architectural Principles

### Aeon-aligned autonomy

- Every run is **stateless** except persisted repo artifacts (`memory/`, committed files).
- Operations must be **idempotent** and re-entrant.
- Execution is in **GitHub Actions** — no interactive approval loops.
- `mode:` frontmatter (`read-only` vs `write`) governs what a skill may mutate.
- File writes must be deliberate, minimal, and auditable.

### ModMe product

- UI decisions are **user-flow first**: clarity, speed, low-friction interaction.
- Component design is **TypeScript-first** with explicit prop contracts.
- All views must handle loading, empty, error, and success states consistently.

### Integration

When a feature spans agent + UI:

1. Define the agent output schema.
2. Implement UI consumption.
3. Add observability and failure-state handling.

---

## Language & Stack Conventions

- **TypeScript** for all app/business logic.
- Shell scripting only where required by workflows.
- No new languages or frameworks without strong justification and alignment to existing repo patterns.
- Follow `aeon` and `modme-ui-01` style choices when options are otherwise equal.

---

## File and Content Rules

### OKF frontmatter

Every `.md` file under OKF roots (`memory/`, `output/articles/`, `skills/`, `docs/`) must carry a non-empty `type:` frontmatter field (OKF v0.1 §9). Run `node scripts/okf-validate.mjs` to check conformance.

Backfill defaults: `type: Skill` for `SKILL.md`, `type: Reference` for other `docs/` or `skills/` markdown, `type: Log` for `memory/logs/`, `type: Index` for `memory/MEMORY.md`.

### Copilot asset naming

Files ending in `.prompt.md`, `.instructions.md`, `.agent.md` must use lowercase hyphenated filenames and include valid frontmatter keys (`description:`, `applyTo:` etc. as applicable).

### Skills

- `skills/<name>/SKILL.md` must exist with `name:`, `category:`, `description:`, `tags:`.
- After adding or modifying a skill, run `bin/generate-skills-json` and commit the regenerated `catalog/skills.json`.

---

## Memory, Logging, and Knowledge Strategy

- Read `memory/MEMORY.md` before major decisions.
- Check `memory/logs/` (last 3–7 days) to avoid duplicate reporting.
- After any substantial task, append a log entry to `memory/logs/YYYY-MM-DD.md` under a `### <skill-name>` heading as bullet points.
- Keep `MEMORY.md` as a short index (~50 lines); expand deep topics into `memory/topics/<topic>.md`.

---

## Implementation Workflow

For any non-trivial feature or fix:

| Step | Action |
|------|--------|
| **Scope** | Identify: agent-runtime, UI, or integration change? |
| **Contract** | Define/confirm data contracts and interfaces first |
| **Plan** | List atomic edits (files + purpose) |
| **Implement** | Apply minimal, focused edits |
| **Validate** | Run relevant tests/lint/typecheck |
| **Resilience** | Add error handling and retries for external IO |
| **Document** | Update docs/memory logs where behavior changed |
| **Report** | Summarize: what, why, validation status, next steps |

---

## Quality Gates

Before concluding any code task:

- No violation of Aeon run model (stateless-run assumptions must hold).
- TypeScript types are explicit and pass checks.
- New files comply with naming/frontmatter conventions.
- No dead paths, dangling references, or undocumented outputs.
- User-facing behavior has loading/error/success states.
- Changes are reversible and scoped.

---

## Troubleshooting Heuristics

1. Reproduce with the smallest deterministic path.
2. Separate: orchestration issue / contract issue / UI rendering issue.
3. Check `memory/logs/` for regressions or repeated failure loops.
4. Add targeted diagnostics and remove debug output before finalizing.

---

## Reference Repo Usage Policy

| Repo | Pattern library use |
|------|---------------------|
| `bolt-new-modme-stack` | Modern TS app structuring and modularity |
| `bolt-slides-modme` | UI presentation/state interaction ideas |
| `mcapp-ai-starter` | Starter scaffolding and multi-language integration boundaries |
| `GenerativeUI_monorepo` | Monorepo organization and cross-package coordination |
| `awesome-copilot` | Canonical prompt/agent/skill formatting and ecosystem practices |

Adapt to Aeon + ModMe constraints first; do not copy blindly.

---

## Anti-Patterns to Avoid

- Approval-loop assumptions in autonomous skills.
- Speculative refactors unrelated to the task.
- Long narrative docs without executable outcomes.
- New frameworks/dependencies without strong justification.
- Naming/frontmatter violations on prompt/agent/instruction/skill/plugin assets.
