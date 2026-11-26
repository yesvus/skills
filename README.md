# Skills

A public collection of reusable agent skills for coding assistants and agent harnesses.

Skills follow the [Agent Skills specification](https://agentskills.io/specification). Each skill is a self-contained directory with a `SKILL.md` entrypoint and optional supporting files.

## Repository layout

```text
skills/
  <skill-name>/
    SKILL.md
    scripts/       # optional
    references/    # optional
    assets/        # optional
```

A minimal skill starts with YAML frontmatter:

```markdown
---
name: example-skill
description: What this skill does and when an agent should use it.
---

# Example skill

Describe the concrete workflow, prerequisites, safety boundaries, and verification steps.
```

## Published skills

- [`browser-mcp-ui-operations`](skills/browser-mcp-ui-operations/SKILL.md) — safe, verifiable operation of an existing browser through a Playwright-style MCP, including keyboard fallbacks for unstable clicks.
- [`github-app-browser-bootstrap`](skills/github-app-browser-bootstrap/SKILL.md) — permission-separated GitHub App registration, repository-scoped or all-repository installation, vault key escrow, and acceptance verification.
- [`chatgpt-batch`](skills/chatgpt-batch/SKILL.md) — offload verified repository tasks to ChatGPT or a similar chat agent with spare quota and a GitHub connector, via self-contained prompts, then take each PR through review, fixes and merge.

## Installing skills

Once skills have been published, install the desired skill using a compatible Agent Skills installer, for example:

```sh
npx skills add yesvus/skills
```

Or clone the repository and link the chosen skill directories into a compatible agent's skills directory. Follow the documentation for your specific harness.

## Scope

- `yesvus/skills` is the **public, portable skill library**.
- [`yesvus/agents`](https://github.com/yesvus/agents) is the separate **private agent configuration/harness repository**. It remains the home for agent profiles, plugins, personal configuration, and local skill-linking machinery.
- This repository should contain only skills and their necessary supporting documentation, scripts, references, and assets; no agent framework or deployment infrastructure.

## Publishing rules

- Keep each skill narrowly scoped and useful beyond a single session.
- Specify genuine prerequisites, supported environments, and how to verify results.
- Never commit credentials, client data, private infrastructure details, or host-specific configuration.
- Prefer existing well-maintained tools and standards over new orchestration layers.

**Status:** Three reusable skills published.
