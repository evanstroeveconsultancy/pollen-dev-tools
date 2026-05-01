# pollen-dev-tools

Personal Cowork plugin. A working subset of dev workflow skills plus the `reprompt` CTOC+ pre-flight skill.

Forked from the `pollen-dev-tools` plugin originally ported from `04-development-tools` for Claude Code.

## Skills

### Pre-flight
- **reprompt** - explicit-trigger CTOC+ restructurer. Fires only on `/reprompt`, `ctoc this`, `pre-flight my request`, or similar. Restructures a request into Context / Task / Output / Constraints / Audience / Success criteria, surfaces missing slots, asks targeted clarifying questions before executing.

### Planning
- **create-plans** - hierarchical project plans (BRIEF, ROADMAP, PLAN, SUMMARY)
- **executing-plans** - execute PLAN.md files in batches with review checkpoints
- **writing-plans** - best practices for writing implementation plans
- **brainstorming** - explore intent and design before implementation

### Development
- **backend-patterns** - Node.js / Express / Next.js: API design, repo pattern, DB optimisation, auth, error handling
- **frontend-patterns** - React / Next.js: composition, compound components, state, performance
- **senior-prompt-engineer** - LLM optimisation, RAG, agentic system design, evaluation

### Orchestration
- **subagent-driven-development** - execute plans via fresh subagent per task with two-stage review
- **requesting-code-review** - dispatch a code-reviewer subagent after completing tasks
- **using-superpowers** - framework for finding and invoking the right skill for any task

### Integration
- **connect** - connect Claude to 1000+ apps (Gmail, Slack, GitHub, Airtable, etc.)
- **connect-apps** - app-specific integration patterns via Composio

### Analysis
- **braintrust-analyze** - analyse Claude Code sessions via Braintrust for patterns and insights
- **continuous-learning** - extract reusable patterns from sessions and save as skills

## Installation

In Cowork:

1. Customize, Personal plugins, **+**, Add marketplace
2. Paste the URL of this repo (e.g. `evanstroeve/pollen-dev-tools`)
3. Sync, then install

## Notes

- Some skills reference `superpowers:` prefixed sub-skills. Those are Claude Code CLI conventions; in Cowork, invoke the equivalent skill directly.
- The `reprompt` skill is **explicit-trigger only**. It will not auto-fire on regular requests.

## Versioning

See [CHANGELOG.md](./CHANGELOG.md).

## Licence

[MIT](./LICENSE).
