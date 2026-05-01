---
name: reprompt
description: Use ONLY when the user explicitly invokes this skill. Trigger phrases are "/reprompt", "ctoc this", "ctoc that", "pre-flight my request", "restructure this ask", "reprompt that", or "run reprompt on [X]". Does NOT auto-trigger on ordinary user requests. When invoked, restructures the user's prior message (or a referenced request) into a CTOC+ executable brief (Context, Task, Output, Constraints, Audience, Success criteria), surfaces missing slots, and asks targeted clarifying questions before executing.
---

# Reprompt: explicit CTOC+ pre-flight

Run only when the user explicitly invokes you. Do not auto-trigger on regular requests. When invoked, restructure the relevant request (the user's prior message, the request they reference, or text they paste) into the CTOC+ format below. Show the restructured brief back to the user as part of the response. This skill is explicit, so transparency is the point.

## When this skill applies

- The user types a trigger phrase: `/reprompt`, `ctoc this`, `ctoc that`, `pre-flight my request`, `restructure this ask`, `reprompt that`, `run reprompt on [X]`
- The user explicitly hands over a draft prompt and asks you to tighten it
- The user asks "can you ctoc this for me" or similar variant

## When this skill does NOT apply

- Any ordinary user request without an explicit trigger phrase
- Conversation, lookups, or work briefs that the user just wants you to action directly
- Implicit ambiguity in a regular request: handle that with judgement, not this skill

## CTOC+ structure

Every restructured request should answer six slots:

1. **CONTEXT.** Who is asking, what do they already know, what is the surrounding situation. Pull from `user_preferences`, memory, prior turns, project CLAUDE.md.
2. **TASK.** The single, specific outcome being asked for. Strip ambiguity. State as a verb + object.
3. **OUTPUT.** Deliverable shape: file type, length, structure, save location, format conventions.
4. **CONSTRAINTS.** Hard rules: English variant, tone, formatting prefs, exclusions, source restrictions, time budget, token budget.
5. **AUDIENCE.** Who reads or uses the output. Their role, level, language.
6. **SUCCESS CRITERIA.** How the user judges pass/fail. What "good" looks like in one sentence.

## Workflow

### Step 1: Identify the target request

Decide which message you are restructuring:
- If the user says "reprompt that" or "ctoc this": the most recent prior user message
- If the user pastes a request: the pasted text
- If the user references "the request about X": find it in the conversation
- If unclear: ask which message to restructure

### Step 2: Parse

Extract from the target request:
- The verb (research, draft, fix, summarise, plan, audit, etc.)
- The object (the thing being acted on)
- Any explicit format, length, audience, or location cues
- References to files, folders, prior context, or memory

### Step 3: Diagnose gaps

For each of the six CTOC+ slots, mark **CLEAR**, **INFERABLE**, or **MISSING**.

- **CLEAR.** Stated outright in the request.
- **INFERABLE.** Safely derivable from `user_preferences`, project context, prior turns, memory, or sensible defaults. State the inference next to the slot.
- **MISSING.** Would require a guess that could materially change the output.

### Step 4: Render the restructured brief

Produce a clean CTOC+ block back to the user, with each slot filled or marked `[MISSING - please clarify]`. Use a tight format:

```
CONTEXT:    ...
TASK:       ...
OUTPUT:     ...
CONSTRAINTS: ...
AUDIENCE:   ...
SUCCESS:    ...
```

### Step 5: Resolve gaps and execute

- **0 MISSING**: ask the user "Run this now?" before proceeding.
- **1-2 MISSING**: list the missing slots, ask the most consequential one first, then offer to proceed once filled.
- **3+ MISSING**: return the brief with gaps highlighted, ask the user to fill them, do not start work.

### Step 6: Apply user preferences on execution

Once the user confirms, run the work against the restructured brief. Inherit stated style preferences from `user_preferences` and memory. These override any conflicting defaults the request implies.

## Anti-patterns

- Do not auto-trigger. This skill is explicit-only. If there is no trigger phrase in the user's message, do not invoke.
- Do not ask the user the same question twice. Check `user_preferences`, memory, and prior turns first.
- Do not invent constraints the user did not ask for and that are not in `user_preferences` or memory.
- Do not interrogate. One question per response, max two for high-stakes work, only when the gap genuinely blocks execution.
- Do not silently rewrite the user's intent. Show the brief back so they can correct it.

## Heuristics by request shape

| Raw request shape | Default diagnosis |
|---|---|
| "Make a presentation about X" | MISSING audience, length, tone, key points, ask |
| "Fix this script" with script in context | INFERABLE, proceed unless behaviour is ambiguous |
| "Research X" | MISSING depth, format, intended use, ask |
| "Send Y to Z" | High-stakes, confirm content, recipient, send-vs-draft before any send |
| "Summarise this doc" | INFERABLE, proceed with sensible default length |
| "Write a memo on X" | Check `user_preferences` for tone/format, ask only about audience and length |
| "Audit this folder" | INFERABLE if folder is mounted, proceed; ask about scope only if folder is mixed-domain |
| "Plan a project" | MISSING scope, deadline, owners, success criteria, ask |
| "What's the difference between A and B" | Pure knowledge, no restructure, answer directly |

## Integration with other skills

- **AskUserQuestion**: the only mechanism for resolving MISSING slots. Never ask in plain text when this tool exists.
- **TodoList / TaskCreate**: name CTOC+ slots in task titles so the user can see what is being optimised for.
- **Document-creation skills** (`docx`, `pptx`, `xlsx`, `pdf`): pass the OUTPUT and CONSTRAINTS slots directly to the chosen skill.
- **`repo-assessor`**: invoked when a request involves a repo URL or README. The CTOC+ output for that skill is fixed by its own workflow.
- **`folder-audit-and-reorganize`**: invoked when a request involves a cluttered folder. The skill's own pillars cover the OUTPUT slot.
- **`memory-management`**: read before parsing to inherit project context and shorthand.

## Output formatting when proceeding

Default to the user's stated style. If none is set, default to:
- Quick verdict or headline answer first
- Short prose for explanation
- Tables for matrix-style data
- Dot points for steps and caveats
- Files via `computer://` links when delivered, no padding postamble

## Output formatting when asking

- One question per AskUserQuestion call where possible
- Multiple-choice options with sensible defaults
- Lead with the gap that most changes the output, not the easiest to answer
