# Vibe-Coding Playbook — Amendment v2.1
## Project Memory System · 3-Man Team Protocol · Milestone Discipline · Code Review

**Slots into:** The Vibe-Coding Playbook for Micro-SaaS (v1.0)
**Supersedes:** Amendment v2.0 entirely — do not use v2.0
**Correction from v2.0:** `CLAUDE.md` and `AGENTS.md` are not AI1/AI2 role files.
They are standard project-wide instruction files read automatically by AI coding tools.
See §0 for the full correction.

---

## §0 — What CLAUDE.md and AGENTS.md Actually Are

Before reading anything else, understand this correction.

### CLAUDE.md
**What it is:** A project-level instruction file that **Claude Code reads automatically at the start of every session**, before you type anything. It is Anthropic's native format. It contains project conventions, stack details, build commands, coding standards, and architectural context.

**What it is not:** A brief for "AI 1." It is a project-wide file. Any instance of Claude Code opening this project — whether acting as architect or implementer — reads it.

**Scope:** Lives at the project root. Committed to git. Shared by the whole project.

*(Source: Anthropic Claude Code docs, claude.com/blog/using-claude-md-files)*

---

### AGENTS.md
**What it is:** An **open cross-tool standard** — a "README for agents" — stewarded by the Agentic AI Foundation under the Linux Foundation. It is read automatically by Cursor, OpenAI Codex, GitHub Copilot, Google Jules, Aider, Zed, and every other major AI coding tool. It exists because developers were maintaining separate instruction files for each tool (`.cursorrules`, `CLAUDE.md`, `.github/copilot-instructions.md`) and needed one file that works across all of them.

**What it is not:** A brief for "AI 2." It is the same project-wide instruction file as `CLAUDE.md`, in the cross-tool format.

**Scope:** Lives at the project root. Committed to git. Read by whichever tool opens the project.

*(Source: agents.md, OpenAI Codex docs, morphllm.com/agents-md-guide)*

---

### The correct relationship between the two

| File | Read by | Purpose |
|------|---------|---------|
| `CLAUDE.md` | Claude Code (any role) | Project context for Claude-specific sessions |
| `AGENTS.md` | Cursor, Codex, Copilot, Jules, Aider, Zed, and others | Same project context, cross-tool standard |

**In practice for this playbook:** maintain both files with the same content. `CLAUDE.md` for when you are using Claude Code as either AI. `AGENTS.md` for when you are using Cursor, Windsurf, or any other tool as either AI. They are not role files — they are **project files**. Role assignment happens at session start via the confirmation prompt, every time, regardless of which file the tool reads.

---

### What replaces the v2.0 role brief files?

The 3-man team protocol lives **inside** `CLAUDE.md` and `AGENTS.md` as a section. Every agent that opens this project — in any role — reads the full team structure. Role *assignment* (am I AI 1 or AI 2 in this session?) is handled by the session start prompt, not by separate files. This is correct because:

1. You cannot guarantee which tool opens first
2. Both tools read the same project root
3. The role confirmation prompt is the only reliable mechanism for per-session role assignment

---

## The Continuity File System (corrected from v2.0)

Six files live at the project root. All committed to git.

| File | Read by | Updated by | Purpose |
|------|---------|------------|---------|
| `CLAUDE.md` | Claude Code (auto, every session) | AI 1 at session end | Project context + team protocol + conventions (Claude Code format) |
| `AGENTS.md` | Cursor/Codex/Copilot/etc (auto, every session) | AI 1 at session end | Same content as `CLAUDE.md` in cross-tool format |
| `SPEC.md` | Both AIs (manual read, session start) | AI 1 (with founder approval) | What is being built — the only source of truth |
| `DECISIONS.md` | Both AIs (manual read, session start) | AI 1 at session end | Append-only architectural decision log |
| `MILESTONE_LOG.md` | Both AIs (manual read, session start) | AI 1 at session end | Atomic milestone tracker — current + backlog + completed |
| `CONTINUITY.md` | Both AIs (manual read, session start) | AI 1 at session end | What happened last session, what comes next |

**Keep `CLAUDE.md` and `AGENTS.md` in sync.** They contain the same content. When you update one, update the other. The simplest approach: write one and copy it to the other. The only difference is the filename.

---

## The Three-Man Team

Every build in this system is operated by exactly three participants. Roles are fixed.

---

### AI 1 — The Architect
**Typical tool:** Any reasoning-capable AI tab — Claude.ai, ChatGPT, Gemini, or a Cursor/Claude Code chat tab in planning mode. No code execution required.
**Role:** Plans. Designs. Orchestrates. Searches for MCP servers. Updates continuity files. **Never writes code.**

**Per-milestone responsibilities:**
1. Read all continuity files before saying anything
2. Search for official MCP servers that reduce implementation burden
3. Decompose the milestone into a numbered implementation plan
4. Specify every file, function signature, and data flow
5. Define the browser test steps for milestone approval
6. Hand the plan to the founder (relay) for AI 2
7. Resolve disagreements or accept AI 2's counter-proposal
8. After milestone approval: update all continuity files, declare session closed

**Hard constraint:** AI 1 never opens a terminal. Never generates executable code. Any code AI 1 produces is pseudocode or schema description only.

---

### Me — The Relay
**Role:** Moves information between AI 1 and AI 2. Makes final calls when they disagree. Performs all browser live testing. Approves milestone completion.

**Per-session responsibilities:**
1. Open both AI tabs simultaneously
2. Confirm both AIs know their role before any work starts
3. Copy AI 1's plan → paste into AI 2's tab
4. Copy AI 2's review or counter → paste into AI 1's tab
5. Carry the agreed spec to AI 2 for implementation
6. Perform browser tests when AI 1 signals the live test gate
7. Report test results back to AI 1
8. Give the final approval to close the milestone

---

### AI 2 — The Implementer
**Typical tool:** Cursor Agent, Windsurf Cascade, Claude Code terminal — must have file editing and terminal access.
**Role:** Reviews AI 1's plan. Challenges it. Implements only after both agree. **Only AI 2 writes code.**

**Per-milestone responsibilities:**
1. Read all continuity files before saying anything
2. Receive AI 1's plan from the founder
3. Review critically: missing edge cases, security gaps, spec conflicts, over-engineering
4. Either approve and implement, or send a counter-proposal back via the founder
5. Once agreed: implement completely, following all conventions in the project files
6. Run all automated checks before declaring done
7. Never implement a plan that has not been agreed with AI 1

**Hard constraint:** AI 2 never designs features unilaterally. Counter-proposals go to AI 1. Alternatives are not implemented without agreement.

---

## Phase 0.5 — Project Memory System

**What this phase is:** Creating the six continuity files before any other work begins.
**Why it comes before Phase 1:** Every subsequent phase generates information that must be captured here. These files cannot be created retroactively without reconstructing decisions that are already lost.

### Entry Criteria
1. Phase 0 exit gate is true (rules file, secret scanning live)
2. No migration or application code has been written
3. Both AI tools are chosen

---

### File 1: `CLAUDE.md` — Project Instructions (Claude Code format)

```markdown
# [PROJECT NAME] — Project Instructions

> This file is read automatically by Claude Code at the start of every session.
> Keep it under 200 lines. Every line competes for context window space.
> Update this file at the end of every session if conventions change.

---

## Project
[One sentence: what this product does and who it is for]

## Stack
- Framework: [e.g., Next.js 14, App Router]
- Database: [e.g., Supabase (Postgres + pgvector + Auth)]
- Deployment: [e.g., Vercel]
- Payments: [e.g., Paddle]
- Error tracking: [e.g., Sentry]
- Uptime: [e.g., BetterStack]

## Commands
```bash
npm run dev          # local dev server
npm run build        # production build
npm run lint         # lint check
npx tsc --noEmit     # type check
npm test             # run tests
npx snyk test        # security audit
supabase start       # start local Supabase
supabase migration new [name]   # create a new migration
supabase db push --local        # apply migrations locally
```

## Coding Conventions
- All schema changes via migration files only — never `db push` to production, never via dashboard
- RLS enabled on every table — `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` in every `CREATE TABLE` migration
- Zod validation is the first operation in every API route, before any db call
- `service_role` key: server-side API routes only — never in `app/`, `components/`, or `pages/`
- Error responses: generic message to client, full error logged server-side with context
- External API calls: explicit timeout ≤8 seconds + user-facing fallback on failure
- New dependencies: ask before installing — name, npmjs.com URL, purpose, download count

## What Never Changes Without Explicit Permission
- Auth provider (currently: [Supabase Auth / Clerk / etc.])
- ORM / database client (currently: [Prisma / Supabase client])
- Payment provider (currently: [Paddle / Stripe])
- Any file listed in DECISIONS.md as locked

## The 3-Man Team Protocol

This project is built by three participants. Read this section every session.

**AI 1 — The Architect**
Plans. Designs. Searches for MCP servers. Updates continuity files.
NEVER writes code. NEVER implements. Pseudocode and schemas only.

**The Founder — The Relay**
Moves plans between AI 1 and AI 2. Performs all browser testing.
Approves milestone completion. Makes final decisions on disagreements.

**AI 2 — The Implementer**
Reviews plans. Writes all code. Runs all checks.
NEVER designs features from scratch. Counter-proposals go back to AI 1.
Implements only after plan is agreed.

**Your role this session is confirmed by the session start prompt.**
If you have not been told your role, ask: "What is my role for this session?"
before doing anything else.

## Session Discipline
- One milestone per session. Current milestone: see MILESTONE_LOG.md.
- At session start: read SPEC.md, DECISIONS.md, MILESTONE_LOG.md, CONTINUITY.md before any work.
- At session end (AI 1 only): update CONTINUITY.md, DECISIONS.md, MILESTONE_LOG.md.
- Never suggest starting a second milestone in the same session.

## Source of Truth
SPEC.md is the authoritative description of what this product is and does.
If anything conflicts with SPEC.md — a plan, a previous conversation, a clever idea —
SPEC.md wins. Flag the conflict to the founder before proceeding.
```

---

### File 2: `AGENTS.md` — Project Instructions (cross-tool format)

**`AGENTS.md` contains identical content to `CLAUDE.md`.** Copy it verbatim. The only difference is the filename — `AGENTS.md` is read by Cursor, Codex, GitHub Copilot, Jules, Aider, and all other tools; `CLAUDE.md` is read by Claude Code.

Keep both in sync. When you update one, update the other.

---

### File 3: `SPEC.md` — The Source of Truth

```markdown
# SPEC.md — Product Specification

**Last updated:** [date]
**Status:** [Active / Under revision]

> SPEC.md is the only authoritative description of what this product does.
> Nothing is built that is not in SPEC.md.
> Changes to SPEC.md require a DECISIONS.md entry explaining why.

---

## Product
[Name and one-sentence description]

## Target User
[Who they are. What problem they have. What they currently use instead.]

## Core Features — v1.0 Scope
Each feature is described as a user-facing capability.
Include: what the user can do, what they see, what they cannot do in v1.0.

1. [Feature name]: [description]
2. [Feature name]: [description]

## Explicitly Out of Scope — v1.0
[List what will NOT be built. This prevents scope creep in AI plans.]

## Data Model (plain English)
[Every table, column, and RLS rule described in plain language.
The SQL lives in supabase/migrations/ — this section is the human-readable version.]

## API Surface
[Every endpoint: path, method, inputs, outputs, auth requirement]

## Non-Functional Requirements
- Uptime: 99.9%
- Response latency: ≤3s for all user-facing operations
- Error responses: generic client message, full server-side log
- GDPR: [specific requirements if targeting EU users]
```

---

### File 4: `DECISIONS.md` — Architectural Decision Log

```markdown
# DECISIONS.md — Architectural Decision Log

Append-only. Never delete an entry.
To reverse a decision, add a new entry that supersedes it and explains why.

---

## [YYYY-MM-DD] — [Decision title]

**Decision:** [What was decided, in one sentence]
**Why:** [What alternatives were considered and why they were rejected]
**Implications:** [What this rules out for future work]
**Decided by:** AI 1 proposed · AI 2 reviewed · Founder approved

---

## Example entries

## 2026-04-14 — Supabase Auth over Clerk
**Decision:** Supabase Auth is the authentication provider.
**Why:** Native RLS integration via auth.uid() eliminates a manual integration
layer. Clerk's $25/month advantage doesn't justify the additional wiring at
this stage.
**Implications:** Session handling uses Supabase server helpers only.
Clerk is not evaluated again unless Supabase Auth fails in a specific scenario.
**Decided by:** AI 1 proposed · AI 2 reviewed · Founder approved

## 2026-04-14 — Zod validation first on every API route
**Decision:** Every API route validates all inputs with Zod before any db call.
**Why:** Prevents FM-05 (CSRF/injection) and FM-18 (prompt injection).
Provides consistent error shapes across the API.
**Implications:** No route handler calls the database before safeParse succeeds.
This is a playbook standard — not up for re-debate.
**Decided by:** Playbook standard
```

---

### File 5: `MILESTONE_LOG.md` — Atomic Milestone Tracker

```markdown
# MILESTONE_LOG.md — Milestone Tracker

---

## Milestone Size Rules

A milestone MUST be completable in one session (≤3 hours of AI work).
A milestone MUST have a binary exit condition — true or not, no partial credit.
A milestone MUST specify whether a browser test is required.

**A milestone is too large if it:**
- Involves more than 2 new database tables
- Touches more than 4 unrelated files
- Requires more than 1 external API integration
- Contains the word "and" in its objective more than once
→ Split it before starting it.

---

## Current Milestone

**ID:** M-[n]
**Title:** [One sentence — what will be true when this milestone is done]
**Status:** [ ] Not started / [ ] In progress / [x] Complete
**Session date:** [YYYY-MM-DD]
**MCP servers identified by AI 1:** [list, or "none found"]
**Browser test required:** [Yes — [exact steps] / No — [reason]]
**Exit condition:** [Binary statement that is either demonstrably true or not]

---

## Completed Milestones

### M-001 — [Title]
**Completed:** [date]
**What was built:** [2–3 sentences]
**Decisions recorded:** [DECISIONS.md entries from this milestone]
**Browser test performed:** [Yes / No — what was tested and what passed]
**Notes for future sessions:** [anything AI 1 or AI 2 flagged]

---

## Backlog (ordered by dependency)

- M-002: [Title] — depends on M-001
- M-003: [Title] — depends on M-002
- M-004: [Title] — independent

---

## Deferred (explicitly out of v1.0 scope)

- [Feature]: deferred — [reason] — reconsider at [milestone or date]
```

---

### File 6: `CONTINUITY.md` — Session Handoff

```markdown
# CONTINUITY.md — Session Handoff

> The last thing AI 1 writes before closing a session.
> The first thing both AIs read at the start of the next session.
> This is the bridge. Without it, every session starts blind.

---

## Last Session
**Date:** [YYYY-MM-DD]
**Milestone:** M-[n] — [title]
**Outcome:** [Complete / Partially complete — X of Y steps done]

## What Was Done
- Created: [filename] — [what it does]
- Modified: [filename] — [what changed and why]
- Migration: [migration filename] — [what schema change]
- Decision: [see DECISIONS.md — date and title]

## What Was NOT Done (and why)
- [Item]: [reason it was skipped] — must be completed before M-[n] can close

## Known Issues / Warnings from AI 2
- [Issue]: [description] — [suggested resolution]

## State of the Codebase Right Now
[Honest 3–5 sentence description of what exists, what is wired up, what is not]

## Next Session

**Milestone:** M-[n] — [title]
**AI 1's first task:** [Specific — usually: search for MCP servers for this milestone]
**Open questions for AI 1:** [Design decisions needed before implementation]
**Open questions for AI 2:** [Implementation specifics, file locations, gotchas]

## Context AI 1 Needs
[Information not in SPEC.md or DECISIONS.md that AI 1 needs to plan the next milestone]

## Context AI 2 Needs
[Specific codebase patterns, existing abstractions, or file locations relevant to next milestone]
```

---

## Session Protocols

### Session Start Protocol (mandatory, every session, no abbreviations)

**Step 1:** Open both AI tabs simultaneously.

**Step 2:** Paste into Tab 1 (AI 1) verbatim:
```
Before you do anything: what is your role in this project?
State it clearly and wait for my confirmation before proceeding.

Then read these files in this order:
1. CLAUDE.md (or AGENTS.md if you're not Claude Code)
2. SPEC.md
3. DECISIONS.md
4. MILESTONE_LOG.md — current milestone section only
5. CONTINUITY.md

After reading all five, tell me:
- Your confirmed role
- What the current milestone is
- What the last session accomplished
- What the first task is for this session

Do not plan anything until you have confirmed all of the above.
```

**Step 3:** Paste into Tab 2 (AI 2) verbatim:
```
Before you do anything: what is your role in this project?
State it clearly and wait for my confirmation before proceeding.

Then read these files in this order:
1. AGENTS.md (or CLAUDE.md if you're Claude Code)
2. SPEC.md
3. DECISIONS.md
4. MILESTONE_LOG.md — current milestone section only
5. CONTINUITY.md

After reading all five, tell me:
- Your confirmed role
- What the current milestone is
- What the last session accomplished
- What you are waiting for before implementation begins

Do not implement anything until you receive AI 1's plan via the founder.
```

**Step 4:** Verify both AIs state the correct role and the correct current milestone. If either is wrong: paste the relevant section from `CLAUDE.md`/`AGENTS.md` directly into that tab. Do not proceed until both are correctly oriented.

**Step 5 (AI 1 only):** MCP server search — before producing any plan. See MCP Search Protocol below.

---

### Session End Protocol (mandatory, every session)

AI 1 executes this. The founder confirms each item is complete.

```
The milestone work is complete (or we are pausing mid-milestone).
Execute the session close sequence now:

1. Update CONTINUITY.md:
   - What was done this session (specific files, migrations, decisions)
   - What was not done and why
   - Known issues flagged by AI 2
   - Honest state of the codebase right now
   - What the next session's first task is

2. Update DECISIONS.md:
   - Append any new architectural decisions made this session
   - If any prior decision was reversed, add a superseding entry with reason

3. Update MILESTONE_LOG.md:
   - Mark M-[n] as Complete (if browser test passed) or In-Progress (if not)
   - Add notes for future sessions under the completed milestone entry
   - Confirm next milestone is correctly listed in Backlog

4. Update CLAUDE.md and AGENTS.md:
   - If any new conventions were established this session, add them
   - If the stack changed, update the stack section
   - Keep both files identical in content

5. Confirm to the founder:
   "Session closed. Files updated: CONTINUITY.md, DECISIONS.md,
   MILESTONE_LOG.md, and [CLAUDE.md / AGENTS.md if changed].
   Next session: M-[n] — [title].
   AI 1's first task next session: [specific action]."

6. Do not suggest continuing. This session ends here.
```

---

## Milestone Design Rules

### Rule 1: One milestone per session. One session per milestone.
A session starts by naming a milestone. It ends when that milestone is complete (browser test passed and exit condition met) or is explicitly recorded as in-progress in `CONTINUITY.md`. A session never starts a second milestone while the first is incomplete.

### Rule 2: Binary exit condition only
The exit condition is a statement that is demonstrably true or not. No partial credit.

**Good:** "User can sign up, receive a confirmation email, and see the dashboard"
**Good:** "Stripe webhook receives a test payment and writes the subscription record"
**Bad:** "Auth is mostly working" — not binary
**Bad:** "The dashboard looks good" — not verifiable

### Rule 3: As small as possible — split aggressively
Correct size: the smallest thing that produces a testable outcome.

**Split if:**
- More than 2 new tables → split by table group
- More than 1 external API integration → split by integration
- Both frontend and backend components → split data layer first, UI second
- A bug in one part would block testing of another → split at that boundary

**Example — "Build authentication" is too large. Correct split:**
- M-001: users table schema + RLS policies (exit: migration runs clean, RLS coverage query returns zero uncovered tables)
- M-002: sign-up API + email confirmation (exit: test user receives confirmation email)
- M-003: sign-in + session management + dashboard redirect (exit: test user can sign in and reach /dashboard)
- M-004: sign-out + session expiry (exit: signing out clears session and redirects to /)

### Rule 4: Every user-facing milestone has named browser test steps
AI 1 defines the exact browser steps as part of the implementation plan. The founder performs them. The milestone does not close on "it looks fine."

### Rule 5: Dependencies are explicit before the milestone starts
A milestone does not start until all its prerequisites are listed as Complete in `MILESTONE_LOG.md`.

---

## MCP Server Search Protocol

**When:** Before AI 1 produces the implementation plan for any milestone.
**Who:** AI 1.
**Why:** Finding an official MCP server before planning means the plan is designed around what already exists, not around custom code that duplicates it.

### AI 1's search prompt for every milestone:

```
Before I design the implementation plan for [milestone title], I need to
search for official MCP servers that can reduce what AI 2 needs to build.

Search the following sources now:
1. https://mcp.so — community registry
2. https://github.com/modelcontextprotocol/servers — official reference servers
3. Vendor documentation for: [list every service this milestone touches]
   e.g., if this milestone involves Stripe: search "Stripe official MCP server 2025"
   e.g., if this involves GitHub: search "GitHub official MCP server"
   e.g., if this involves Supabase: search "Supabase MCP server"
4. The current tool's connected MCP server list (check settings)

For each relevant server found, report:
- Server name and official source URL
- Who maintains it (vendor vs. community)
- What capabilities it provides
- What it eliminates from the implementation plan

Based on findings, state:
- Which MCP servers (if any) to connect before this milestone
- What each server replaces in terms of code AI 2 would otherwise write
- Whether any server requires credentials to configure before the session continues
```

### What to do with results:
- **Official vendor MCP server found** (Stripe, GitHub, Supabase, Linear, etc.): connect it in AI 2's environment, note it in `MILESTONE_LOG.md`, incorporate its tools into the plan
- **Community MCP server only:** evaluate maintenance status and last update date before using — note the trust decision in `DECISIONS.md`
- **Nothing relevant found:** record "none found" in `MILESTONE_LOG.md` — proceed with standard implementation

---

## Browser Live Testing Protocol

### When AI 1 must trigger a browser test:
- Any milestone that creates or modifies a user-facing page or flow
- Any milestone completing an end-to-end action (sign up, payment, form submission, data display)
- Any milestone where a visual or interactive outcome is part of the exit condition
- Any milestone touching auth, authorization, or data visibility

### When browser testing is NOT required:
- Migrations only (no UI change)
- Background jobs with no direct user interaction
- Internal API routes with no frontend caller yet
- Infrastructure or configuration changes with no user-facing effect

### The live testing gate — AI 1's instruction to the founder:

When AI 2 confirms all automated checks pass, AI 1 sends this:

```
## 🧪 LIVE BROWSER TEST REQUIRED — M-[n]

Implementation is complete and all automated checks pass.
Before I can close this milestone, perform the following browser test:

**Environment:** [staging URL / production URL]
**Open as:** [incognito window / logged-out state / specific user role]

**Test steps:**
1. [Exact step]
2. [Exact step]
3. [Exact step]

**Pass looks like:**
[Exactly what the founder sees if everything worked]

**Fail looks like:**
[Most likely failure modes and what they look like on screen]

**If the test passes:** reply "browser test passed — M-[n]"
**If the test fails:** describe exactly what you saw and I will diagnose

Do not approve this milestone without completing every step above.
```

---

## Code Review Protocol

### Stage 1: AI 2 Reviews AI 1's Plan (before writing any code)

```
Review AI 1's plan against this checklist before implementing anything.
For each item that fails, state the specific problem and send a counter-proposal
back to AI 1 via the founder. Do not implement a plan with unresolved issues.

SECURITY
[ ] Does any step expose service_role key to client code?
[ ] Does any new table lack an RLS policy in the plan?
[ ] Does any API route accept user input without a Zod validation step?
[ ] Does any step fetch a user-supplied URL without SSRF protection?
[ ] Does any error handler return a stack trace to the client?
[ ] Does any step pass user input to an LLM prompt without sanitisation?

DATA INTEGRITY
[ ] Does any step use db push or a non-migration schema change method?
[ ] Are all foreign key relationships in the plan?
[ ] Does any step modify production data without an explicit safety step?

SPEC COMPLIANCE
[ ] Does this plan implement what SPEC.md says — no more, no less?
[ ] Does any part of this plan contradict DECISIONS.md?
[ ] Does this plan add anything explicitly listed as out of scope?

CODEBASE CONSISTENCY
[ ] Does this plan create a new abstraction where an existing one could be reused?
[ ] Does this plan use a different pattern for an operation already established?
[ ] Does this plan introduce a dependency that duplicates an existing one?
```

### Stage 2: AI 1 Reviews AI 2's Implementation (after implementation, before browser test)

```
Review AI 2's implementation before scheduling the browser test:

[ ] Every file in the plan exists and is in the correct location
[ ] No files were created or modified that were not in the agreed plan
[ ] The implementation matches SPEC.md for this feature
[ ] CONTINUITY.md accurately describes what was built
[ ] No new DECISIONS.md entries are needed that AI 2 did not flag

If any item fails: state the discrepancy and ask AI 2 to resolve it
before the browser test is scheduled.
```

### Stage 3: Founder Spot-Check (5 minutes, before every browser test)

```bash
# Run all five. Every one must pass before browser testing begins.

# 1. service_role key not in client code
grep -r "service_role" app/ components/ pages/
# Expected: zero results

# 2. No hardcoded secrets
grep -rn "OPENAI_API_KEY\|DATABASE_URL\|SECRET\|PASSWORD" \
  app/ components/ --include="*.ts" --include="*.tsx" | grep -v "process.env"
# Expected: zero results (env var references are fine)

# 3. Zod validation in every new API route
# Open any new file in app/api/ — first non-import line should be a Zod schema

# 4. RLS in every new migration
grep -L "ENABLE ROW LEVEL SECURITY" supabase/migrations/*.sql
# Expected: any file listed here must have ENABLE ROW LEVEL SECURITY added

# 5. Type check
npx tsc --noEmit
# Expected: zero errors
```

---

## Context Management During a Session

### Signs of context degradation (watch for these):
- An AI re-proposes something already decided this session
- AI 2 uses a different pattern for the same operation it used 10 messages ago
- AI 1's plan contradicts an earlier step in the same session
- Either AI produces generic advice not specific to this codebase

### Mid-session reset prompt (paste into the affected tab):
```
Context reset. Stop what you are doing.

Re-read: [CLAUDE.md or AGENTS.md], SPEC.md, DECISIONS.md,
MILESTONE_LOG.md (current milestone only), CONTINUITY.md.

State in one sentence:
- What is the current milestone
- What has been completed in this session so far
- What remains

Then continue from where we left off, consistent with
decisions already made in this session.
```

### Proactive re-anchor (every 45–60 minutes of work):
```
Quick anchor: current milestone is M-[n] — [title].
Completed so far: [X]. Working on: [Y]. Proceed.
```

### Session length limit:
3 hours of active AI work maximum. After 3 hours: update `CONTINUITY.md` with precise state, run the session end protocol, close both tabs, resume next session fresh.

---

## Quick Reference Card

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SESSION START
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1.  Open Tab 1 (AI 1) + Tab 2 (AI 2) simultaneously
2.  Paste role confirmation prompt into both tabs
3.  Wait — if either AI doesn't know its role, paste the
    team protocol section from CLAUDE.md/AGENTS.md
4.  AI 1: reads all 5 continuity files → confirms milestone
5.  AI 2: reads all 5 continuity files → confirms milestone
6.  AI 1: searches for MCP servers for this milestone
7.  AI 1: produces implementation plan
8.  You: copy plan → paste into AI 2 tab
9.  AI 2: reviews plan against pre-implementation checklist
10. If counter-proposal: relay to AI 1 → resolve → relay back
11. AI 2: implements agreed plan only
12. AI 2: runs all automated checks → reports pass/fail
13. You: run 5-minute founder spot-check
14. AI 1: post-implementation review
15. AI 1: determine if browser test required
16. If yes: AI 1 outputs exact test steps with pass/fail criteria
17. You: perform browser test → report exact result
18. If pass → proceed to close
    If fail → AI 1 diagnoses → AI 2 fixes → repeat from step 12

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SESSION END
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
19. AI 1: updates CONTINUITY.md
20. AI 1: updates DECISIONS.md (if new decisions)
21. AI 1: updates MILESTONE_LOG.md (close or mark in-progress)
22. AI 1: updates CLAUDE.md + AGENTS.md (if conventions changed)
23. AI 1: "Session closed. Next session: M-[n] — [title]."
24. Close both tabs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MILESTONE TOO LARGE IF:                SPLIT IT.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- More than 2 new tables
- More than 4 unrelated files
- More than 1 external API integration
- More than one "and" in the objective

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CONTINUITY FILES (all must be current)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CLAUDE.md   — project instructions (Claude Code)
AGENTS.md   — project instructions (all other tools) [same content]
SPEC.md     — what we're building (source of truth)
DECISIONS.md — why we built it this way
MILESTONE_LOG.md — what's done, what's next
CONTINUITY.md — what happened last session

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Amendments to Playbook v1.0

### Amendment to Phase 0
Add to Entry Criteria:
> "All six continuity files have been created from the templates above."

Add to Non-Negotiable Standards:
> "The session start protocol must be executed at the start of every session. Starting a new tab and immediately prompting a task without role confirmation and continuity file review is a violation. This is how sessions produce work that contradicts prior decisions."

### Amendment to Phase 1
Phase 1 becomes **Milestone M-001** in `MILESTONE_LOG.md` (database schema + migrations).
Before AI 1 designs the schema, it searches for official MCP servers for the database and auth layer.

### Amendment to Phase 3
Each feature is one or more milestones in `MILESTONE_LOG.md`. "One feature per prompt" becomes "one milestone per session." AI 1 writes the plan. AI 2 reviews against the pre-implementation checklist before writing any code.

### Amendment to Phase 7
Add to the Go-Live Checklist:

| # | Item | Verified? |
|---|---|---|
| 29 | `CLAUDE.md` and `AGENTS.md` are committed, identical in content, and reflect current conventions | ☐ YES / ☐ NO |
| 30 | `SPEC.md` matches what was actually built — no undocumented features, no missing ones | ☐ YES / ☐ NO |
| 31 | Every milestone in `MILESTONE_LOG.md` is marked Complete with browser test performed | ☐ YES / ☐ NO |
| 32 | `DECISIONS.md` has an entry for every major technical decision made during the build | ☐ YES / ☐ NO |
| 33 | `CONTINUITY.md` reflects the state as of the last completed milestone | ☐ YES / ☐ NO |

---

*Amendment v2.1 — April 2026.*
*Corrects v2.0: CLAUDE.md and AGENTS.md are project-wide instruction files, not role files.*
*Sources: Anthropic Claude Code docs · agents.md · OpenAI Codex AGENTS.md guide · morphllm.com/agents-md-guide*
*Use with Vibe-Coding Playbook v1.0. Neither document is complete without the other.*
