# Contributing

Thanks for helping build this product.

## Ground rules

1. **Read `PACKAGE.md`** — import boundaries (software) and placement rules (hardware) are normative; every folder's `purpose.txt` says what belongs there.
2. **Every new folder ships with a `purpose.txt`** (`PURPOSE:` / `SHOULD CONTAIN:` / `SEE:`).
3. **No empty scaffolding** — create folders when the work arrives.
4. **Choices go through process:** RFC in `rfcs/` → discussion → ADR from `docs/decisions/TEMPLATE.md`.
5. **Compliance claims need evidence** — link the standard number or lab report.
6. **Generated files are regenerated**, never edited (Gerbers, STEP/STL, API clients, tokens).

## Workflow

```bash
pnpm install && pnpm dev     # apps + platform
pnpm lint && pnpm test       # includes boundary checks + contract tests
pnpm build
make -C firmware build test  # firmware (per board)
```

## Pull requests

- One concern per PR; a release-affecting change updates `products/<line>/release/` too
- Contract changes update `kernel/contracts` FIRST, then `platform/api/openapi`, then consumers
- Firmware protocol changes update the protocol doc + contracts together (they are the hardware/software seam)
- Quality defects reference a `manufacturing/quality/ncr/` record

## Commit style

Conventional commits with scope: `feat(firmware):`, `fix(electronics):`, `feat(apps):`, `chore(platform):`, `docs(certification):`.
