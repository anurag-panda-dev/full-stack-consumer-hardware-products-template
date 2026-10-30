# ADR-0001: One Monorepo for Hardware, Firmware, Apps, and Cloud

**Status:** Accepted
**Date:** 2026-10-30
**Deciders:** Template author
**Related:** `PACKAGE.md`, root `README.md`, sibling `consumer-hardware-products-template` and `big-software-product-template` ADRs

---

## Context

A connected-hardware product spans four worlds that must stay in lockstep: **physical design** (CAD, PCB), **embedded software** (bootloader, drivers, OTA agent), **companion software** (mobile/web/admin), and **device cloud** (API, OTA service, telemetry). Typical failure modes:

1. **Version skew** — app ships against an API the firmware doesn't speak; OTA manifest mismatches hardware revisions.
2. **Split repos** — protocol definitions drift, hardware and software teams lose the shared map, releases become archaeology.
3. **Boundary collapse** — apps import cloud internals; firmware embeds UI copy; nobody can refactor either side.
4. **Toolchain interference** — KiCad/CAD noise breaks software CI and vice versa.

## Decision

Keep **one monorepo** with two clearly bounded worlds and an explicit seam, organized by discipline (hardware) and deployability (software):

- **Hardware side** (mirrors `consumer-hardware-products-template`): `products/`, `mechanical/`, `electronics/`, `firmware/`, `certification/`, `manufacturing/`, `supply-chain/`, `validation/`, `packaging/`, `test-tools/`.
- **Software side** (mirrors `big-software-product-template`): `apps/` (thin shells), `crosscutting/device-sdk`, `kernel/`, `platform/`, `tests/`, `datasets/`, `content/`.
- **The seam:** `kernel/contracts` is the single source of truth for data shapes — firmware message schemas, the OTA manifest format, and the public API derive from it. The protocol spec lives in `docs/architecture/`; changes to the seam always require an ADR.
- **Release coherence:** `products/<line>/release/<rev>/` pins PCB revision + firmware tag + BOM revision + app/API versions + certification report ids — one record reconstructs a shipped batch *and* the software it runs.
- **Toolchain isolation:** software tasks run under pnpm/Turborepo; hardware/firmware under make/KiCad — CI workflows are split per discipline so CAD noise never breaks app builds.
- **Stack neutrality where undecided:** `platform/api` (Express ⇄ FastAPI) and `apps/mobile` (RN ⇄ Flutter) defer choice via RFC with zero consumer impact.
- **Every folder carries a `purpose.txt`;** decisions flow `rfcs/` → `docs/decisions/` (this ADR family).

## Consequences

### Positive
- Protocol/manifest/API drift becomes a compile-time or contract-test failure instead of a field failure.
- One release record spans hardware *and* software versions — full traceability per batch/OTA wave.
- Either world can be extracted later (monorepo does not forbid it) because boundaries are already explicit.
- New contributors (and AI agents) get one map: `purpose.txt` everywhere + `AGENTS.md`.

### Negative & mitigations
| Cost | Mitigation |
|---|---|
| CI must serve two toolchains (Node + KiCad/make) | Split workflows per discipline; run hardware checks only when their paths change |
| Repo weight grows with firmware binaries/CAD | Git LFS for large binaries; release artifacts to storage, pin by hash in release records |
| Team without hardware experience edits "a hardware repo" | Boundary rules + `purpose.txt` + lint failures that teach; `PACKAGE.md` is normative |
| Seam (`kernel/contracts`) becomes a bottleneck | Changes require an ADR but are small and reviewed once; codegen fans out to consumers |

### Neutral
- `datasets/`/`content/` mirror the software template; hardware-only teams still use them for telemetry/quality data and support copy.

## Alternatives considered

1. **Two repos (hardware + software)** — rejected: the protocol/OTA seam is where drift kills releases; versioning it across repos is precisely the archaeology problem. Revisit only if separate release cadences demand it.
2. **Product-folder-first for everything** — rejected: shared firmware, common components, single BOM truth, and shared cloud infra duplicate across lines.
3. **Monorepo without an explicit seam (conventions only)** — rejected: without `kernel/contracts` as single source, manifest/protocol drift returns within two release cycles.
4. **PLM + IoT platform as systems of record (repo is secondary)** — rejected for a template: repo must remain meaningful without paid tools; integrations can be layered later.

## Compliance

- CI enforces: `purpose.txt` presence, software boundary lint, contract tests (apps vs `platform/api/openapi`), manifest schema validation, BOM completeness.
- Contract changes update `kernel/contracts` FIRST → `platform/api/openapi` → consumers (PR checklist).
- Release PRs must update `products/<line>/release/` with all pinned versions.
- Revisit at: first mass-production partner, first OTA at scale, or if a world outgrows shared CI.

---

*Amendments: append below, dated — never rewrite the original text.*
