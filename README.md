# build-second-brain

A [Claude Code](https://claude.com/claude-code) skill that builds you a **second brain**: an AI-maintained Obsidian-compatible markdown vault that gives Claude persistent knowledge of you — your people, projects, companies, decisions, and goals — across every session.

You ask for a second brain. Claude interviews you, scaffolds the vault, seeds it from your connected tools, and sets up a nightly "sleep cycle" that keeps it organized while you sleep.

## What it builds

```
your-vault/
├── CLAUDE.md          # the AI's operating manual for your vault
├── inbox.md           # quick capture from any device; filed nightly
├── _identity/         # user.md (who you are) · soul.md (how the AI acts) · identity.md (its role)
├── People/            # one linked note per person in your life
├── Projects/          # goals, status, next actions
├── Companies/         # businesses you own or deal with
├── Decisions/         # what you decided, when, and why
├── Meetings/          # extracted decisions & commitments, not transcripts
├── Daily/             # day logs + auto-generated morning reviews
├── Knowledge/         # reusable frameworks and processes
├── MOCs/              # maps of content; Home.md = front door + open loops
├── Sources/           # immutable raw inputs
└── Archive/           # nothing gets deleted, it gets archived
```

Plus three maintenance skills inside the vault:

- **`/consolidate`** — the nightly pass (via your OS scheduler): files your inbox, creates notes for newly-mentioned people/projects, merges duplicates, lints contradictions and stale facts, and writes you a morning review with ≤3 things actually worth your attention.
- **`/ingest`** — extract decisions, commitments, preferences, and insights from any dump (meeting transcript, email thread, article).
- **`/daily`** — a 3–5 line end-of-day log, under a minute.

Everything is plain markdown with `[[wikilinks]]` — Obsidian is the viewer, git is the history, Syncthing (optional) is the multi-device sync. No database, no cloud storage required, no lock-in.

## Install

```bash
git clone https://github.com/dcolliervb23/build-second-brain ~/.claude/skills/build-second-brain
```

(Windows: clone into `%USERPROFILE%\.claude\skills\build-second-brain`.)

Then open Claude Code anywhere and say:

> build me a second brain

The skill triggers, interviews you first (your goal, what role the AI should play, how proactive it should be), and builds from your answers — not from guesses.

## Design notes

- **Interview before build** — the identity layer (`soul.md`'s voice + proactivity contract) is what turns a filing cabinet into a chief of staff.
- **Link density drives retrieval** — every entity mention gets a wikilink; *unresolved* links are how the nightly pass discovers new entities to file.
- **Deterministic stop criteria** — the consolidation loop stops at zero orphan links + zero duplicates; a clean vault yields a one-line review instead of invented work.
- **Facts carry provenance** — "(as of YYYY-MM)" recency markers and confidence tags (`stated | high | medium | speculation`) power stale-claim and contradiction linting.
- **Privacy posture** — credentials never enter the vault; sync is peer-to-peer; your files stay yours. (AI processing itself goes through Anthropic's API — know where that boundary sits.)

## Credits

Synthesized from [Dan Martell's digital-brain system](https://www.youtube.com/watch?v=b4d32pBa3UY) (identity files, entity folders, sleep-cycle consolidation), [Andrej Karpathy's LLM-wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) (sources → wiki → schema, with lint), and the [Claude Code loops article](https://claude.com/blog/getting-started-with-loops) (verification as skills, deterministic stop criteria). Built and battle-tested with Claude Code — including a baseline test proving what agents miss without it.

## License

MIT
