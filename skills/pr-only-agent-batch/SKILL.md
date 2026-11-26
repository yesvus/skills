---
name: pr-only-agent-batch
description: Run a batch of repository tasks through external chat agents that can only open pull requests (for example a web chat agent the owner drives by hand), then take every resulting PR through review, fixes and merge. Use when an owner wants to parallelize issue work onto agents that cannot read your rules, watch CI or answer review comments.
---

# PR-only agent batch

Some agents can clone a repository and open a pull request but cannot see
webhooks, CI results or review threads, and cannot read the orchestrator's
standing rules. They are cheap parallel capacity, but every PR they open
stops at "opened". This skill splits the work so the orchestrator owns
everything before dispatch and everything after the PR appears.

Roles:

- **Owner**: approves the batch, pastes prompts into the external agents,
  and reports PR numbers if the orchestrator cannot discover them.
- **Orchestrator** (you): selects and verifies tasks, writes prompts, and
  takes each PR from open to merged.
- **External agent**: implements one task and opens one PR. Nothing else.

Do not drive a consumer chat UI with browser automation to dispatch prompts
yourself; that usually violates the provider's terms. The owner pastes them.

## Prerequisites

- Repository write access for the orchestrator: push to PR branches, reply
  to and resolve review threads, merge.
- A way to receive PR events (CI results, reviews, comments), such as a
  webhook relay or a harness PR-subscription tool. Without one, check each
  open PR's state when the owner prompts you or at a slow cadence (tens of
  minutes, not seconds), and say so in the report.

## 1. Select candidates

1. List open issues that are repository-only: the change can be written,
   tested and reviewed offline. Exclude anything that needs live systems,
   credentials, account changes, production access or owner-held decisions.
2. Exclude areas the owner is working in, and anything already claimed.
3. Verify every candidate against the default branch **and** against open
   PRs before writing a prompt. In practice most backlog items turn out to be
   done, partly done, owned by an open PR, or blocked on live state. Delegate
   this bulk check to a cheap model or subagent with a fixed output schema:

   ```text
   <issue> | DONE | OWNED_BY_PR #<n> | NEEDS_LIVE | BLOCKED_BY #<n> | FREE
   <one line of evidence: file path, PR number, or missing live fact>
   ```

   Spot-check its FREE verdicts yourself; dispatch only those.
4. Check for collisions between FREE items: two prompts touching the same
   file or CI workflow will conflict. Serialize them or drop one.

## 2. Write self-contained prompts

The agent sees only the prompt, so merge every rule into it. Use
[references/prompt-template.md](references/prompt-template.md). Each prompt:

- names one issue, one fixed branch name, and the exact files or directories
  in scope;
- states the acceptance criteria and the exact test command to run;
- forbids live changes, secrets, new credentials, and edits outside scope;
- states the PR title format, the PR template headings, and `Refs #<n>`;
- tells the agent to describe a blocker in the PR body instead of guessing.

Hand the owner all prompts in one message, each in its own fenced block,
ready to paste without editing.

## 3. Intake each PR

Discover PRs by the branch names you assigned, or ask the owner for numbers.
Then, per PR:

1. **Subscribe to events** (CI, reviews, comments) instead of polling; see
   Prerequisites for the fallback.
2. **Draft PRs**: some agents open drafts, and some review bots skip drafts.
   Mark ready for review if review should start.
3. **Stale base**: if the branch was cut before a newly required check or its
   config landed (for example a secret scanner whose config file is new),
   that check fails on the PR for reasons unrelated to the diff. Merge the
   default branch into the PR branch; never rebase or force-push it.
4. **Read the diff yourself.** Confirm it matches the prompt's scope and
   introduces nothing live, privileged or unrequested.
5. **Verify every bot finding before acting.** Review bots produce real
   finds and false positives. Typical false positives: claiming a list
   mutation does not propagate when the variable aliases the same object,
   or misreading an action input as a shell command. Reproduce the claim
   (a two-line script, the CI log), then fix or reply with the evidence.
6. **Fix small, confident, in-scope issues** directly on the PR branch, with
   the repository's fast checks run first. For a test that guards a
   behavior, mutate the guarded code once to prove the test fails, then
   restore it. Take larger asks or design changes to the owner.
7. **Batch follow-up commits.** Some review bots pause after several pushes
   in a row; fewer, complete pushes keep reviews flowing.
8. **Resolve only threads you read and addressed.** Reply with what changed
   or why nothing changed.
9. **Merge** when the prompt's acceptance criteria are met, the PR has no
   "Blocked" section or other unresolved blocker, required checks are
   green, no thread is open, and the head is the one you reviewed: squash
   with an expected-head-SHA guard, then unsubscribe and remove any local
   worktree. A blocked PR is reported to the owner, not merged.

## 4. Report

Tell the owner, per PR: merged or blocked, what you changed on top of the
agent's work, and which findings you rejected and why. Feed recurring
lessons back into the prompt template.

## Verification

- Every dispatched issue was marked FREE with evidence before its prompt
  was written.
- Every merged PR had green required checks on the merged head SHA.
- No thread was resolved without a reply.
