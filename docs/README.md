# Documentation Index

Knowledge base for this project — this README is the entry point.

| Section | Contents |
|---|---|
| `architecture/` | Full system architecture: mechanical + electrical + firmware + apps + device cloud, interfaces, power budget, data flows |
| `product/` | Per-product-line specs and requirement baselines |
| `strategy/` | Brand, GTM, monetization, competitive, roadmap, metrics |
| `decisions/` | Landed ADRs — `TEMPLATE.md` starts a new one, `ADR-0001-Structure.md` records this layout |
| `legal/` | Engineering-adjacent legal incl. privacy (corporate docs belong in a private company-ops repo) |

## Conventions

- **Pending** proposals live in `/rfcs`, not here — an RFC graduates into an ADR when accepted.
- ADRs are never silently rewritten; amendments are appended with a date.
- The hardware/software seam (firmware protocol, OTA manifest format) is documented here and expressed in `kernel/contracts` — changes to it always require an ADR.
