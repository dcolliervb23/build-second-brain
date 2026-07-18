# Second-brain templates

Adapt names/paths to the user. `<USER>` = user's confirmed name. Nothing here is optional decoration — each piece exists because a baseline agent omitted it and the system underperformed.

## Folder tree

```
vault/
├── CLAUDE.md          # operating manual (short; @imports the schema)
├── inbox.md           # append-only quick capture; /consolidate files it nightly
├── _identity/         # user.md, soul.md, identity.md
├── People/            # one note per person
├── Projects/          # one note per project, status + next action
├── Companies/         # businesses owned/worked with
├── Decisions/         # what was decided, when, why, alternatives
├── Meetings/          # YYYY-MM-DD-name.md, extracted not transcribed
├── Daily/             # day logs + morning reviews
├── Knowledge/         # reusable frameworks, processes, insights (incl. vault-schema.md)
├── MOCs/              # maps of content; Home.md = front door + open loops
├── Sources/           # immutable raw inputs; extract, never edit
├── Archive/           # completed/dead notes; move, don't delete
└── .claude/skills/    # consolidate/, ingest/, daily/
```

## Vault CLAUDE.md

```markdown
# Second Brain — Operating Manual

This is <USER>'s second brain: a persistent, AI-maintained knowledge vault. You read it, write it, and keep it healthy. It is not a codebase.

## Every session
- Read `_identity/user.md`, `_identity/soul.md`, `_identity/identity.md` first. Adopt the soul's voice.
- Answer questions FROM the vault, citing notes with wikilinks. If the vault doesn't know, say so — never invent facts about <USER>'s life.
- Before the session ends, file durable new facts (people, decisions, commitments, insights) into the structure — anything unfiled goes to `inbox.md` rather than dying with the conversation.

## Rules
- Full note conventions: @Knowledge/vault-schema.md
- New information goes to the correct folder immediately. When in doubt: inbox.md, and let /consolidate file it.
- Every person, project, or company mentioned gets a wikilink — even if the target note doesn't exist yet. Orphan links are how /consolidate finds new entities.
- `Sources/` is immutable raw input: extract from it, never edit it.
- Facts that can go stale carry "(as of YYYY-MM)" and a confidence tag: `stated | high | medium | speculation`.
- Never store passwords, credentials, or full account numbers anywhere in this vault.
- Commit to git after any meaningful batch of changes.

## Maintenance loops
- `/ingest` — extract from a dump/transcript into the structure
- `/daily` — end-of-day log
- `/consolidate` — nightly sleep pass: file orphans, merge duplicates, update MOCs, lint, write morning review
```

## Knowledge/vault-schema.md

```markdown
---
type: reference
created: <TODAY>
tags: [schema, meta]
---

# Vault schema

## Naming
- Files: lowercase-with-hyphens (`People/jane-doe.md`). Meetings: `YYYY-MM-DD-short-name.md`. Daily: `YYYY-MM-DD.md`; morning reviews: `YYYY-MM-DD-morning-review.md`.

## Frontmatter (every note)
type: person|project|company|decision|meeting|daily|knowledge|moc|reference · created/updated: YYYY-MM-DD · tags: [lowercase] · status: active|done|archived (projects/decisions)

## Note shapes
- **Person**: who they are + relationship → contact info (no sensitive IDs) → context bullets → shared `[[projects]]`/`[[meetings]]` → how they work/communicate.
- **Project**: goal in one sentence → status + next action → key `[[people]]`/`[[decisions]]` → dated timeline, newest first.
- **Company**: what it is + user's relationship → key `[[people]]` → operational facts → linked `[[projects]]`.
- **Decision**: what/when/who → why → alternatives → consequences observed later (append, dated).
- **Meeting**: attendees as wikilinks → decisions → commitments (who/what/when) → preferences learned → insights. Skip small talk.
- **Knowledge**: state frameworks so future-AI can APPLY them, not just recall them.

## Writing for retrieval
- Notes are for future AI retrieval, not human prose. Dense, factual, linked.
- Wikilink every entity mention, including ones without a note yet. Exception — the vault owner: refer to them as a link to the `_identity/user` note, never a People/ note (prevents a duplicate self-entity).
- Documentation examples of link syntax must be escaped in backticks so the lint never sees them as real links.
- Stale-able claims: "(as of YYYY-MM)" + confidence `stated` (user said it) | `high` | `medium` | `speculation`.
- "Every note" = files in People/Projects/Companies/Decisions/Meetings/Daily/Knowledge/MOCs and `_identity/` (type: reference). CLAUDE.md, inbox.md, and dotfiles carry no frontmatter.

## Lint definitions (used by /consolidate)
- **Orphan mention**: a wikilink with no note behind it → create the note or fix the link. Scope: only links in entity folders, Daily/, Meetings/, Knowledge/, MOCs/, and `_identity/`; ignore links inside backticks or code blocks.
- **Duplicate**: two notes, one entity → merge, retarget inbound links, `git rm` the loser (move to Archive/ instead if unsure).
- **Contradiction**: incompatible facts → keep newer/higher-confidence, log in morning review; never silently rewrite the user's stated facts.
- **Stale claim**: "(as of …)" > 6 months on an active note → flag in morning review.
```

## _identity/ files (write from interview answers, not guesses)

- **user.md** — basics (confirmed name, emails, location) · family · work & ventures · **#1 goal verbatim, dated** · operating frameworks · communication preferences · open identity questions.
- **soul.md** — voice (match the user's stated style) · behavior: be resourceful before asking; answer from vault with citations; "the brain doesn't know this yet" over invention; **proactivity contract** (observer = record + answer / advisor = surface findings, never act externally unasked / partner = safe reversible actions, report after) · hard limits: no credentials stored; no external sends/money/changes without go-ahead.
- **identity.md** — the AI's role(s) and prime directive (tie to the #1 goal) · name (let it propose its own after ~2 weeks of use, or user picks now) · rhythms.

## .claude/skills/consolidate/SKILL.md

```markdown
---
name: consolidate
description: Nightly sleep pass — file orphan mentions, merge duplicates, update MOCs, lint the vault, write a morning review.
disable-model-invocation: true
---

# Consolidate the vault

Work in order. Lint definitions: Knowledge/vault-schema.md.

1. **Scope**: `git log --since=yesterday --name-only` (fallback: files modified in 48h). First run: whole vault.
2. **File inbox.md** entries into the structure; clear what you file.
3. **File orphan mentions** (lint scope per Knowledge/vault-schema.md — entity folders only, skip backticked examples): each orphan link → create the note from context (correct folder, schema frontmatter) or retarget to an existing note.
4. **Merge duplicates** → better note wins, retarget inbound links, `git rm` the loser (Archive/ if unsure).
5. **Update MOCs** + the "Open loops" list in MOCs/Home.md.
6. **Lint**: contradictions and stale claims → list in the morning review; don't silently rewrite the user's stated facts.
7. **Morning review**: Daily/YYYY-MM-DD-morning-review.md, dated with the RUN date (it's read the next morning) — filed/merged/linted, ≤3 genuinely strategic flags, open loops. Under a screen.
8. **Commit.**

**Stop criteria (deterministic)**: zero orphan wikilinks (minus intentional future links listed in the review), zero known duplicates, review written, committed. A clean vault = one-line review; do not invent work.

**Never**: delete content you can't place (→ Archive/), edit Sources/, store credentials.
```

## .claude/skills/ingest/SKILL.md

```markdown
---
name: ingest
description: Extract decisions, commitments, preferences, and insights from a dump (transcript, thread, article, notes) into the vault.
disable-model-invocation: true
---

# Ingest: $ARGUMENTS

If input is a file, copy the raw original to Sources/ first. Extract four things:
1. **Decisions** (what/who/why) → Decisions/ or the project note.
2. **Commitments** (who/what/when) → person + project notes; meeting note if applicable.
3. **Preferences** (how people work) → their People/ note.
4. **Insights** (frameworks, shifts) → Knowledge/.
Skip small talk. Wikilink every entity; stub notes for new ones. List what changed, then commit.
```

## .claude/skills/daily/SKILL.md

```markdown
---
name: daily
description: End-of-day log — capture the day in 3-5 lines plus anything durable.
disable-model-invocation: true
---

# Daily log

1. Ask for a quick brain-dump if not already given. 2. Daily/YYYY-MM-DD.md: 3–5 lines. 3. Extract durable items per /ingest rules. 4. Note tomorrow's most important thing. 5. Commit. Under a minute of the user's time.
```

## Nightly automation

Canonical wrapper path: `<vault>/.claude/nightly-consolidate.cmd` (Windows) or `.sh` (Linux/macOS). It must cd to the vault and run:

```
claude -p "/consolidate" --permission-mode acceptEdits --allowedTools "Read,Glob,Grep,Edit,Write,Bash(git add:*),Bash(git commit:*),Bash(git status:*),Bash(git log:*),Bash(git diff:*),Bash(git rm:*),Bash(git mv:*)" >> <vault>/.claude/logs/consolidate-<date>.log 2>&1
```

(The allowlist mirrors what /consolidate actually does: `git log` for scoping, `git rm`/`git mv` for duplicate deletion and archiving — trim it and merges finish half-done.)

- **Windows**: register via `schtasks /Create /SC DAILY /ST 23:07 /TN "SecondBrain Nightly Consolidate" /TR <wrapper>`.
- **Linux/macOS**: cron `7 23 * * *` or launchd.
- Log dir `.claude/logs/` is gitignored. Test-run the wrapper once end-to-end before trusting it.

## Sync (Syncthing)

Share the vault folder; `.stignore`: `.git`, `.obsidian/workspace*`, `.trash`, `.claude/logs`. Phone: Syncthing-Fork + Obsidian mobile. Best topology: an always-on hub (NAS/server) every device pairs with. Privacy: relays/discovery see only metadata/ciphertext; both can be disabled for LAN-only. `.gitignore`: workspace files, `.trash/`, `.stfolder/`, `.stversions/`, logs.

## Global awareness pointer (user's ~/.claude/CLAUDE.md)

```markdown
# Second Brain
My persistent second brain is the vault at <VAULT_PATH>. When personal context matters (who someone is, my projects, commitments, decisions, goals), read that vault's CLAUDE.md and _identity/ files and answer from its notes. When a session produces durable personal facts, file them into the vault per its schema — don't let them die with the conversation.
```
