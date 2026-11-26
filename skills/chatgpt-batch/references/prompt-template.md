# Prompt template

Fill every `<...>`. Keep it self-contained: the agent has no other context.

```text
Repository: <owner>/<repo> (default branch: <main>)
Task: <issue title> — issue #<n>

Goal
<two to four sentences: what must be true after this PR, and why>

Scope
- Change only: <paths>
- Do not touch: <CI workflows / other areas> unless listed above.
- Repository-only work. Make no live changes, call no production
  systems, and do not add, request or print secrets or credentials.

Acceptance
- <observable criterion 1>
- <observable criterion 2>
- Tests: add or update tests in <path>. Run `<exact command>` and make it
  pass before opening the PR. If a blocker below keeps it from passing,
  open the PR anyway and put the failing command and its output under
  "Blocked".

Conventions
- Branch: <type>/<short-name>-<n> (exactly this name).
- Commit and PR title: imperative, under 72 characters, e.g.
  "<type>(<area>): <summary>".
- PR body uses these headings: <Summary / Changes / Testing>. End it with
  "Refs #<n>". Do not write "Fixes" or "Closes".
- No dates, model names or provenance notes in files.
- Do not weaken existing guards, tests, append-only ledgers or validation.

If blocked
If a fact you need is not in the repository, do not guess. Open the PR with
what you could do and list the blocker under a "Blocked" heading.

Deliverable
One pull request from the branch above. Open it ready for review, not as a
draft.
```
