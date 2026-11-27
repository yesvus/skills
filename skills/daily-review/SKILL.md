---
name: daily-review
description: Review the user's day or recent days using ActivityWatch, Calendar, GitHub, and conversation context. Invoke on /daily-review, günlük analiz, gün analizi, daily review, dünü analiz et, or haftalık analiz.
---

# Daily review

## Scope and access
- Default `/daily-review` to today from midnight to the current time in the user's local timezone; label incomplete days. For yesterday or weekly requests, use the requested complete local dates. Compare the preceding day and seven complete days where coverage supports it.
- Read-only by default. Do not modify repositories, calendars, devices, tasks, or monitoring configuration without explicit authorization. Never expose credentials or raw private URLs/window titles in reports.
- Use existing connected tools and sources; discover exact schemas before calling. Do not ask for a new export just to finish a daily review.

## Collect evidence
1. **Desktop ActivityWatch:** check authenticated Grafana/Prometheus for ActivityWatch aggregates first. If unavailable or stale, use authorized read-only remote access to the desktop and query its local ActivityWatch API. Discover buckets and freshness, then query AFK and window events for the review interval; use web events only as a cross-check after verifying foreground alignment. Never expose raw URLs, titles, or credentials. If offline, label coverage missing rather than zero.
2. **Android ActivityWatch:** check the user's configured export folder for newly uploaded exports. Inspect metadata and actual event coverage. Process new exports when the user adds them, or during the weekly review. Do not require daily manual export, send daily export reminders, or duplicate imports. If the newest export does not cover the day, mark mobile data unavailable and continue the review. Distinguish ActivityWatch device activity from OnWatch model quota telemetry; if the user means quota data, use OnWatch separately.
3. **Calendar:** enumerate relevant primary, wellbeing, work, academic, project, deadline and task calendars. Report study/work blocks, sleep opportunities and routine events as plans, not actual activity. A scheduled class is not evidence of attendance; a scheduled sleep block is not measured sleep. Deduplicate recurring and mirrored events. Use the user's report or independent evidence for actual completion.
4. **GitHub:** query `user:<username>` across the user's repositories using exact local-day UTC boundaries, with separate `is:pr created`, `is:pr merged`, `is:issue created`, `is:issue closed` searches. If connector results do not honor qualifiers, use an authorized read-only GitHub CLI/API, without printing secrets. Check total counts, pagination, per-repo breakdown and evidence links. Count PRs/merges separately; distinguish agent-assisted repository output from personal effort. Never equate merges with deployment or raw PR counts with impact. Highlight concrete engineering outcomes and outstanding risks.
5. **Conversation context:** consider user-reported sleep, energy, recovery, real work, academic priorities and significant decisions. Prefer self-report over an incompatible calendar plan. Treat unknowns as unknown, not failures. Consult Todoist directly for completion evidence if relevant and available.

## Calculate and validate
- Clip intervals to review boundaries. Union overlapping active intervals; exclude AFK for desktop. Calculate app durations by intersecting foreground events with active intervals. Browser/media watcher data can overlap window activity; never add it to desktop totals, and omit misleading domain durations if browser foreground alignment fails. Deduplicate historical exports by device, bucket and event identity/time.
- Compute cross-device union only when both devices have adequate timestamp-level coverage; otherwise report device totals separately. Count unlocks only from a relevant bucket. Include hourly pattern, top apps, late-night use and 7-day baseline when coverage supports it.
- Device inactivity is not sleep. Label sleep as self-reported or inferred proxy; never invent sleep duration. Do not penalize recovery days or treat scheduled wake targets as obligations.
- Track last record time and source coverage. Do not invent numbers, productivity scores or causal claims. Report changes in sources or calculation limitations.

## Report (in the user's preferred language)
1. Brief evidence-grounded headline and review cutoff.
2. Device usage and trends, preferably compact charts (hourly active minutes, top foreground apps, last seven days).
3. Engineering outcomes, PR/issue counts, repository breakdown, and links to material changes. Separate direct work from agent output.
4. Planned vs actual: school, projects, personal time and recovery. Mark unverified attendance/completion explicitly.
5. Two or three realistic next steps, balancing academic priorities, recovery and engineering. Avoid moralizing about entertainment or rewarding overwork.
6. Source coverage and caveats, especially mobile freshness and incomplete days. Keep the report concise but substantive.
