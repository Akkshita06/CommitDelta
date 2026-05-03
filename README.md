# CommitDelta

> **Git commits as a learning signal.**  
> Not what you built — how you built it.

---

```
$ commitdelta report

  Session: auth-module · 2h 31min · 11 commits
  ┌─────────────────────────────────────────────┐
  │  Learning Velocity          74 / 100   ↑    │
  │  Revert rate                27%        ✓    │
  │  Avg commit interval        9 min      ✓    │
  │  Mechanical commits         1          ⚠    │
  └─────────────────────────────────────────────┘

  ⚠  10:34 — 244 lines, 0 reverts, 25min gap
     Low iteration pattern detected. Revisit to verify understanding.
```

---

## The problem

Every learning tool measures what you submitted.  
None of them measure whether you understood what you built.

A student who copies a solution and one who struggled to learn it produce **identical output**. Their commit history looks **completely different**.

CommitDelta reads that difference.

---

## How it works

Four signals, extracted from your existing git commits:

| Signal | What it measures |
|---|---|
| **Diff entropy** | How novel is each commit vs your recent baseline? High early = exploration. Declining = solidification. Spike late = paste. |
| **Commit interval** | 5–15 min gaps = iterative, engaged work. 45min gap → large commit = bulk drop. |
| **Revert rate** | Reverts are gold. Students who understand something make mistakes and fix them. Students who copy don't. |
| **Churn convergence** | Does commit size shrink over the session? Genuine learning converges. Copy-paste doesn't. |

These combine into a single **Learning Velocity score** per session.  
No inference engine. No LLM. Pure behavioral signal from data you already produce.

---

## Install

```bash
pip install commitdelta
```

```bash
cd your-project/
commitdelta init        # installs a post-commit hook into .git/hooks/
```

That's it. No account. No API key. No config.  
Code exactly as you normally would.

---

## Usage

```bash
# View your last session
commitdelta report

# View your 7-day Learning Velocity trend
commitdelta week

# Enable commit nudges every 15 minutes (opt-in)
commitdelta nudge --on
```

---

## What the output looks like

```
Session · 2025-05-03 · data-structures-hw

Score breakdown
  09:03  91  [exploratory]  Initial scaffold — binary tree
  09:14  88  [exploratory]  Add insert logic (3 reverts)
  09:26  82  [exploratory]  Fix null pointer edge case
  09:38  71  [solidifying]  Refactor node traversal
  09:51  68  [solidifying]  Add height calculation
  10:09  65  [solidifying]  Write unit tests
  10:34  21  [MECHANICAL]   ← Add balancing logic (244 lines, 0 reverts)
  10:51  58  [solidifying]  Wire to main
  11:02  63  [solidifying]  Fix off-by-one in rotation
  11:18  79  [exploratory]  Add deletion case
  11:34  84  [exploratory]  Handle duplicate keys

Weekly trend (last 7 days)
  Mon  ████████████████████  81
  Tue  ███████████████       62
  Wed  ████████████████████  83
  Thu  ████████████████████  79
  Fri  ████████████          51  ← worth reviewing
  Sat  —
  Sun  ████████████████████  74  (today)
```

---

## Signal scoring

```python
LV = (0.45 × entropy_score)
   + (0.30 × interval_score)
   + (0.15 × revert_premium)
   + (0.10 × convergence_bonus)
```

A commit is flagged **mechanical** when all four are true:
- Diff entropy > 0.85 (very large novel change)
- Preceding interval > 30 minutes
- Lines added > 100
- Revert count = 0

Conservative by design. Better to miss a real paste than to falsely flag genuine work.

---

## Privacy

- **All data stays local.** No network calls, ever.
- The hook only fires on commits you explicitly make.
- Session logs written to `~/.commitdelta/sessions/` — readable plaintext, yours to delete.
- CommitDelta has no server, no account, no telemetry.

---

## What CommitDelta is not

| Not this | Why |
|---|---|
| A code quality tool | Output quality is already measured. Process isn't. |
| A plagiarism detector | We flag patterns, not intent. The insight is yours to interpret. |
| An instructor dashboard | Local-first, student-first. Always. |
| A replacement for understanding | A mirror, not a judge. |

---

## Architecture

```
.git/hooks/post-commit          ← fires on every commit
        │
        ▼
~/.commitdelta/sessions/*.jsonl ← append-only event log
        │
        ▼
commitdelta report              ← signal processor + renderer
        │
        ▼
localhost browser tab           ← interactive session report
```

Three components. ~400 lines total. No framework. No database.

---

## Roadmap

- [x] Git hook collector
- [x] Diff entropy scoring
- [x] Interval and revert analysis
- [x] Session report (CLI + browser)
- [x] Weekly trend view
- [ ] Language-aware diff parsing (AST-level entropy)
- [ ] Per-repo baseline calibration
- [ ] VS Code sidebar panel
- [ ] `commitdelta export` — portable session archive

---

## Contributing

```bash
git clone https://github.com/yourhandle/commitdelta
cd commitdelta
pip install -e ".[dev]"
pytest
```

Signal weight tuning and calibration research especially welcome.  
Open an issue before a large PR.

---

## License

MIT. Use it, fork it, study it.

---

*Built at a student hackathon. The irony of using git commits to measure learning while building a tool about git commits was not lost on us.*
