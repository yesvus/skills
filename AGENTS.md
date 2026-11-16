# Agent instructions

This repository contains **public, portable Agent Skills**. It is not an agent runtime or infrastructure repository.

- Read `README.md` and the applicable `skills/<name>/SKILL.md` before editing.
- Put each skill under `skills/<name>/SKILL.md` with valid Agent Skills `name` and `description` frontmatter. Keep optional scripts, references, and assets inside the same skill directory.
- Make workflows actionable, concise, and harness-neutral wherever practical. Document actual prerequisites and verification steps; do not imply untested behavior is verified.
- Avoid creating generic skills that only restate available CLI help. Prefer specific repeatable workflows with non-obvious operational knowledge.
- Never publish secrets, account identifiers, private URLs, internal IPs, private repository contents, customer data, or host-specific environment configuration.
- Keep destructive, privileged, billable, or production-changing operations behind explicit operator approval and document rollback when relevant.
- Do not import private files from `yesvus/agents` or edit that repository as a side effect of maintaining this library.
- No publishing pipeline, registry, deployment resources, or new framework is needed without a separately justified request.
