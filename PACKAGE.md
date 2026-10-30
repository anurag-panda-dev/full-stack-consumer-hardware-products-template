# PACKAGE.md — Repository conventions

## Layout

Two worlds, one repo: **hardware** by engineering discipline (`products/`, `mechanical/`, `electronics/`, `firmware/`, `certification/`, `manufacturing/`, `supply-chain/`, `validation/`, `packaging/`, `test-tools/`) and **software** by deployability (`apps/`, `crosscutting/`, `platform/`, `kernel/`, `tests/`), plus shared `datasets/`, `content/`, `docs/`, `business/`, `legal/`.

Root `README.md` has the rules of thumb; every folder's `purpose.txt` is normative for what it may contain.

## Software import boundaries (normative)

| From | May import | Never |
|---|---|---|
| `apps/*` | `crosscutting/*`, `kernel/*` | `platform/*` internals, hardware folders |
| `crosscutting/*` | `kernel/*` | `apps/*`, `platform/*` |
| `platform/*` | `kernel/*` | everything above it |
| any `logic/` | framework-free libs | React, HTTP, DB clients |

**Across the platform boundary:** `platform/api/openapi` (contract-first, generated from `kernel/contracts`).
**Hardware ⇄ software seam:** firmware messages/OTA manifests derive from `kernel/contracts`; the protocol spec lives in `docs/architecture/`.

## Hardware placement (normative)

| Rule | Detail |
|---|---|
| Product folders link, never copy | `products/<line>/` = specs, requirements, variants, release records |
| Release pins versions | `products/<line>/release/<rev>/` names PCB rev, firmware tag, BOM rev, tooling revisions, cert report ids |
| Generated outputs | Only from `tooling/exporters/` (STEP/STL/Gerber/PDF) — never hand-edited |
| Validation vs certification | `validation/` = internal testing; `certification/` = regulatory evidence |
| Jigs | Fixture documents in `manufacturing/test-jigs/`; fixture software in `test-tools/` |
| Sandbox/labs | Never feed production folders implicitly — promotion is an explicit reviewed move |

## Data & content

- Every `datasets/curated/<x>/` file validates against `datasets/schemas/<x>.schema.ts` in CI.
- `content/` holds written copy (manuals copy, troubleshooting); `packaging/inserts/` holds print-ready layout.

## Process

- **Decisions:** RFC (`rfcs/`) → ADR (`docs/decisions/` from `TEMPLATE.md`), append-only amendments.
- **Enforcement:** `kernel/config/eslint` boundary rules + `tests/contract` pin API consumers; CI validates `purpose.txt` presence and BOM completeness.
- **Tooling:** pnpm workspaces + Turborepo for software; KiCad/make for hardware/firmware (commands fill in per project).
