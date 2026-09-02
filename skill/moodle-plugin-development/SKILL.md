---
name: moodle-plugin-development
description: |
  Guides a Moodle plugin from a first idea to a released, tested version through a spec-driven workflow: problem framing, a written spec with user stories and Given/When/Then test scenarios, an approval gate before any code is written, task-by-task implementation with self-testing (happy path and sad path), an independent review/test phase, and a second approval gate before release. Produces intent.md, specs.md and user-stories.md along the way.

  Use for requests like "let's build a new Moodle plugin for X", "help me spec out this Moodle feature", "moodle-plugin-development", or any time you're about to ask an AI to just start coding a Moodle plugin without a plan first.

created: 2026-09-02
---

# Moodle Plugin Development

A structured alternative to jumping straight from an idea to AI-generated code. The discipline — not the AI's technical skill — is what makes AI-assisted Moodle plugin development something you can actually trust: a written spec everyone agreed to, tests derived from that spec instead of invented afterward, and someone other than the builder taking an independent look before anything ships.

This skill walks you through six phases. It's meant to be used conversationally, over as many sessions as the plugin needs — not filled in mechanically in one sitting.

## Scope: what this skill is and isn't

- **Is:** a process for the *design and delivery* of a Moodle plugin feature — what to decide, in what order, with what approval gates.
- **Isn't:** a testing framework or a coding-standards check. For "does this code follow Moodle's conventions and is it secure", use a code-review skill during Phase 5a — [`moodle-plugin-vibe-review`](https://github.com/arnoutvree/moodle-plugin-vibe-review) is built for exactly that step.
- **Isn't:** a replacement for Moodle's official [`moodle-plugin-ci`](https://moodledev.io/general/development/tools/pluginci) — that stays your automated CI/CD gate; this skill governs the human/conversational process around it.
- **Scales down for solo developers.** Where a phase below assumes a separate reviewer, tester or stakeholder and you're working alone, don't skip the phase — get any second pair of eyes you can (a colleague, a community reviewer, even a fresh AI session with no memory of how the code was built), because the point of an independent look is that it doesn't share the builder's blind spots.

---

## Phase 1: problem & context → `intent.md`

Before any design work: what's the core problem? Who will use this? What changes for them? What are the constraints — budget, timeline, target Moodle version, hosting environment?

This is a conversation, not a fixed checklist — ask targeted questions based on what's already known. Write the answers to `intent.md`. This becomes the opening section of the spec in Phase 2.

### Phase 1a (optional): proof of concept for an external API

Only when the core functionality depends on an external API whose rate limits, available fields, auth flow or pagination haven't been tested in practice. Test against a disposable sandbox or test environment — never production. Not feasible? Adjust scope before Phase 2 continues; don't let a spec get built on an unverified assumption.

---

## Phase 2: design phase → `specs.md`

Everything that fills the spec: requirements, user stories, test scenarios, and visual design. Four sub-steps, each with its own output.

### 2a: gather wishes and requirements

Targeted questions depending on the goal and context — no fixed questionnaire. If this surfaces a technical assumption Phase 1a didn't cover (e.g. "oh, this also needs to call that external API"), go back to 1a before requirements keep building on it. Append raw requirements to `specs.md`, alongside `intent.md`.

### 2b: write user stories → `user-stories.md`

Format: **As / I want / so that.**

Every item from the raw requirements — including a throwaway detail like a data source or an error-handling need — must show up in at least one user story. This is a coverage check: whatever's missing here never gets tested in 2c.

### 2c: test scenarios per user story → part of `specs.md`

Format: **Given / When / Then.**

One scenario per possible outcome, not one scenario for "the" edge case. Each Then also states what must *not* happen. This is a mandatory part of the spec, not optional polish — it's what turns a user story into something testable.

### 2d (optional): visual design

UI/UX work, user-flow, accessibility — for anything with a user-facing surface. Should be finished before the approval gate in Phase 3, so the approver reviews text and design together rather than approving text now and design later.

### 2e (conditional — only if the plugin touches personal data): privacy by design

When the plugin collects, syncs or enriches personal data: settle the legal basis, opt-out handling and retention period *in the spec*, before code exists — not bolted on afterward. This gets checked again in Phase 5, this time against the actual built code.

---

## Phase 3: document hygiene & approval — **Gate 1**

This is a single approval moment, not two separate steps — don't clean up the spec first and only then send it off for approval later. Clean it up, then get sign-off on that same clean version, in one pass. That way the approver is always reviewing the finished document, never a rough draft, and you never end up shipping something that only *looked* approved because nobody re-read it after the cleanup.

**Document hygiene:** the spec should read as a finished document, not a working log — no revision history, no numbered list of open questions, no internal process jargon. State only what will be built, as settled fact, plus explicitly what's out of scope. Discussion and weighed-up alternatives belong in the conversation (and, for a decision with lasting architectural weight, in a separate decision record) — not in the spec itself.

**Approval:** whoever owns the decision signs off — a stakeholder, a client, or yourself if it's a personal project, but make it a deliberate, recorded moment rather than an implicit "I guess we're doing this now". Record it somewhere durable (an issue, a ticket, a commit message) so it's traceable later.

No code is written before this gate.

---

## Phase 4: build

### 4a: implementation

Each user story becomes an implementation task; each test scenario becomes a concrete test to write. Starts only after Gate 1 — a change discovered mid-build that alters the agreed spec goes back to Phase 2, not straight into the code.

### 4b: self-test

Each Given/When/Then scenario becomes an automated test (Behat, PHPUnit, or whatever the plugin already uses) — covering both the happy path and the sad path: invalid input, a missing capability, data that doesn't exist. The person or agent who built the feature tests it first, before anyone else looks at it.

---

## Phase 5: independent test phase

A second, independent look that doesn't share the builder's assumptions — a green self-test only proves the builder didn't catch their own blind spot, not that nobody has one. Four sub-steps.

### 5a: code review

Check the diff against coding standards/security and against the spec itself — does the code do exactly what the user story and test scenario asked, no more and no less (including any privacy-by-design commitments from 2e)? And does the implemented test actually exercise the Given/When/Then, sad path included, not just the success path?

This is the step where [`moodle-plugin-vibe-review`](https://github.com/arnoutvree/moodle-plugin-vibe-review) fits — call it here. If an AI agent performs this review, keep a human in the loop; never let it be fully autonomous. Findings become issues, not silent fixes.

### 5b: functional test by someone other than the builder

Walks through the test scenarios from 2c manually against the running plugin — acceptance criteria, edge cases, error states, integration with the existing system, plus the Phase 2e privacy commitments where relevant. Findings become issues immediately.

### 5c (if there's a separate stakeholder or client): functional test by that stakeholder

Same scenarios from 2c, from their perspective, after 5b. This is the acceptance test — findings become issues immediately.

### 5d: approval — **confirms Phase 5 is done**

Confirmation that every finding from 5a/5b/5c has been resolved. Phase 5 isn't complete until this is recorded. Ready for Phase 6.

A finding from any sub-step isn't automatically a bug — it can also be a change request. A bug goes back to Phase 4. A change request re-enters Phase 2 for that piece of scope, then needs its own Gate 1 approval before it's built.

---

## Phase 6: release

### 6a: ship

Release only after written/explicit sign-off from whoever approved in 5d, recorded somewhere durable.

### 6b: documentation

Update the plugin's README/CHANGELOG (and any external docs it's linked from) to reflect what shipped.

### 6c: aftercare

A short monitoring window after release — how long depends on the plugin's size and blast radius, a few days for a small feature, a few weeks for something touching core workflows. Watch logs, error reports and support channels for regressions tied to the shipped user stories. A regression found during this window follows the normal bugfix path (back to Phase 4); fixing it doesn't reset the window.

---

## Limitations

- A process skill, not a testing framework — it tells you *what* to test and *when*, not how to write Behat/PHPUnit for your specific plugin.
- Phases 5b/5c assume a separate tester and/or stakeholder. Working solo doesn't excuse skipping the independent look — see "Scope" above.
- No built-in artifact storage between sessions — `intent.md`, `specs.md` and `user-stories.md` are meant to live in your plugin's own repo, not in this skill.
