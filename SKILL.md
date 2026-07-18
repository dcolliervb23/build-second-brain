---
name: build-second-brain
description: Use when a user asks for a "second brain", persistent AI memory of their life/contacts/projects, a personal knowledge vault, or an AI chief of staff that remembers them across sessions.
---

# Build a second brain

An AI-maintained Obsidian-compatible markdown vault: the AI reads it, writes it, and keeps it healthy on a nightly loop. Synthesis of Dan Martell's digital-brain system (identity files, entity folders, sleep-cycle consolidation), Karpathy's LLM-wiki pattern (sources → wiki → schema, with lint), and Claude Code loop design (verification as skills, deterministic stop criteria).

**Core principle: the vault is plain markdown + wikilinks; the AI is the librarian. Link density drives retrieval — every entity mention gets a `[[wikilink]]`, and unresolved links are how the system discovers new entities.**

## Process — in this order

1. **Interview the user FIRST** (AskUserQuestion; don't infer): where the vault lives; their #1 goal (the brain holds them accountable to it); what the AI should be (chief of staff / coach / copilot / life manager); proactivity level (observer / advisor / partner); communication style; sync needs; which connected sources (email, calendar, task manager, business tools) to seed from. Verify the user's actual name from data, not assumptions.
2. **Scaffold** the vault + `git init -b main`: folders and starter files per [templates.md](templates.md) — People, Projects, Companies, Decisions, Meetings, Daily, Knowledge, MOCs, Sources, Archive, `inbox.md`. Put `.gitkeep` in empty folders (git drops them otherwise) and ensure a git identity exists (`git config user.name/email`, vault-local is fine) or every automated commit fails.
3. **Write the identity layer** from interview answers: `_identity/user.md` (who they are, goal, frameworks), `_identity/soul.md` (AI voice + proactivity contract + hard limits), `_identity/identity.md` (AI role + prime directive). This is what turns retrieval into a chief of staff.
4. **Write the vault CLAUDE.md** (short) + schema note (full conventions): templates in templates.md. Facts carry "(as of YYYY-MM)" + confidence (`stated|high|medium|speculation`) — these feed the lint pass.
5. **Create the three maintenance skills** in the vault's `.claude/skills/` (`disable-model-invocation: true`): `/consolidate` (nightly: file orphan wikilinks, merge duplicates, update MOCs, lint contradictions/stale claims, morning-review note; stop when zero orphans + zero duplicates + review written), `/ingest` (extract decisions/commitments/preferences/insights from any dump), `/daily` (3–5 line day log). Full text in templates.md.
6. **Seed from the user's connected sources** — parallel read-only agents, one per source (email → People; calendar → rhythms + People; task manager → Projects; business tools → Companies). Each agent reads the schema first, never clobbers existing notes, never stores credentials. Expect duplicates; /consolidate merges them.
7. **Automate the nightly consolidation** on the user's OS: Windows Task Scheduler / cron / launchd running the wrapper script at the canonical path `.claude/nightly-consolidate.cmd` (or `.sh`), ~23:00. The allowlist MUST cover everything /consolidate does — including `git log` (scoping) and `git rm`/`git mv` (duplicate deletion, archiving) — see templates.md for the exact command; an allowlist that can't delete leaves every merge half-done. Session-schedulers die with the session — use the OS scheduler.
8. **Sync (if multi-device)**: Syncthing (free, P2P, no cloud storage) or Obsidian Sync. `.stignore`: `.git`, workspace files, logs. Write the user a per-device setup note in Knowledge/.
9. **Make every session aware**: with the user's explicit confirmation (never in a test/demo build — global config outlives the vault), add a 2–4 line pointer in their global `~/.claude/CLAUDE.md`: vault location, "read its CLAUDE.md + _identity/ when personal context matters, file durable facts back per its schema."
10. **Verify end-to-end** before claiming done: run /consolidate once (must merge/file and write a morning review; a second run must find nothing), then ask a *fresh* `claude -p` session 3 real questions about the user — answers must come from vault notes with citations, in the soul's voice.

## Common mistakes

- Building the vault without the interview — you get a filing cabinet, not a chief of staff. The identity layer is the difference.
- Folders without wikilinks — retrieval never compounds. Link every entity mention, even to notes that don't exist yet.
- Skipping baseline verification — "the files exist" is not "the brain works." A fresh session answering real questions is the test.
- Storing credentials/account numbers "for convenience" — never; the vault syncs to phones.
- Letting seeded guesses harden into facts — tag confidence, and have the user confirm identity basics (name, relationships) explicitly.
- Consolidation that invents work — deterministic stop criteria; a clean vault yields a one-line morning review.
