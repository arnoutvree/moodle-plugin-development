# Moodle Plugin Development

A [Claude Code](https://claude.com/product/claude-code) skill that guides a **Moodle plugin** from a first idea to a released, tested version through a spec-driven workflow — instead of jumping straight from an idea to AI-generated code.

The idea: clear process, not the AI's technical skill, is what makes AI-assisted plugin development something you can actually trust. A written spec everyone agreed to, tests derived from that spec instead of invented afterward, and someone other than the builder taking an independent look before anything ships — that's what turns "vibe coding" into something you can rely on.

## The workflow

1. **Problem & context** — what's the core problem, who uses it, what changes for them, what are the constraints? → `intent.md`
   - *(optional)* a throwaway proof of concept if the feature depends on an untested external API
2. **Design** — requirements, user stories (*As / I want / so that*), test scenarios per story (*Given / When / Then*, happy path and sad path), optional visual design, and privacy-by-design if personal data is involved → `specs.md`, `user-stories.md`
3. **Approval gate 1** — a clean spec, explicit sign-off, before a line of code is written
4. **Build** — each user story becomes a task, each test scenario becomes an automated test, self-tested by the builder first
5. **Independent test phase** — code review (pairs well with [`moodle-plugin-vibe-review`](https://github.com/arnoutvree/moodle-plugin-vibe-review)), a functional test by someone other than the builder, an acceptance test by the stakeholder if there is one, then approval
6. **Release** — ship, update docs, and a short aftercare window watching for regressions

Full detail, including how each phase scales down for a solo developer, is in [`SKILL.md`](skill/moodle-plugin-development/SKILL.md).

## Install

```bash
git clone https://github.com/arnoutvree/moodle-plugin-development.git
ln -s "$(pwd)/moodle-plugin-development/skill/moodle-plugin-development" ~/.claude/skills/moodle-plugin-development
```

## Usage

In Claude Code, from wherever you're planning the plugin:

```
let's build a new Moodle plugin for X
```

or explicitly:

```
/moodle-plugin-development
```

## Related

- [`moodle-plugin-vibe-review`](https://github.com/arnoutvree/moodle-plugin-vibe-review) — the code-review skill this workflow calls into during Phase 5a.

## License

MIT — see [LICENSE](LICENSE).
