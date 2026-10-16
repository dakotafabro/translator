---
name: translator
description: >
  Decompression instrument that translates code into plain-language narratives.
  Four modes - narrate (what does this do and what intent is compressed into it?),
  diff (what changed and why?), contract (what does this promise?), verify (does
  the code match the design doc + team feedback?). Output is layered, audience-aware,
  and compression-conscious. Also functions as a behavioral instrument when narrations
  are stored or compared across models.
author: dakotafabro
version: "1.0"
tags:
  - code-comprehension
  - design-verification
  - plain-language
  - compression-of-intent
  - behavioral-instrument
  - pr-workflow
---

# Translator

A decompression instrument for code. Turns implementation into plain-language narrative that makes intent visible, verifiable, and portable.

## Activation

The translator activates when any of these occur:

- Explicit: "translate this" / "narrate this" / "what does this do in plain language"
- Diff: "what changed" / "narrate this diff" / "narrate this PR"
- Contract: "what does this promise" / "what's the contract"
- Verify: "does this match the design doc" / "verify alignment"
- Workflow: suggested when implementation is complete (before a PR is pushed) and after a change ships

## Mode Selection

| Trigger | Mode | Reference |
|---|---|---|
| File path, function, or code block | `narrate` | [narrate.md](references/narrate.md) |
| Git diff, PR URL, or "what changed" | `diff` | [diff.md](references/diff.md) |
| Module, class, interface, or "what does this promise" | `contract` | [contract.md](references/contract.md) |
| Design doc + code, or "does this match" | `verify` | [verify.md](references/verify.md) |

All modes consult the world model when available: [world-model-grounding.md](references/world-model-grounding.md)

When mode is ambiguous, ask. Don't guess.

## Audience Lens

Every translation targets an audience. Auto-detect from context, but allow override.

| Lens | Voice | When |
|---|---|---|
| **Engineer** | Technical, decision-focused, names types and patterns | PR review, code exploration, self-understanding |
| **Team** | Architectural, trade-off-focused, explains why not just what | Design reviews, team discussions, chat summaries |
| **Stakeholder** | Outcome-focused, no implementation details, answers "what does the user get?" | PM updates, sprint reviews, external communication |

Default is Engineer unless context indicates otherwise.

## World Model Grounding

When the code being translated belongs to a codebase with a known world model,
the translator consults it to ground narrations in architectural context. This
transforms the output from "what this code does locally" to "what this code does
in the system."

Full process: [world-model-grounding.md](references/world-model-grounding.md)

Key behaviors:
- **Domain identification** from code paths and keywords matched against world
  model routing tables
- **Context retrieval** from state, causal, intent, and prediction layers
- **System Position** section added between summary and full narrative (upstream,
  downstream, blast radius, initiative context)
- **Vocabulary consistency** - uses world model terms, reinforcing shared language
- **Health signals** - flags world model gaps, drift, and extension opportunities

When no world model is available, the translator works without grounding. The
narration is accurate but less contextual.

## Layered Output

Every translation produces three layers. The reader stops when they have enough.

```
[One-liner] - Single sentence. The TL;DR. Enough for a commit message or Slack snippet.

[Summary] - One paragraph. Enough for a PR description or design doc section.
Covers intent, approach, and key decisions.

[Full narrative] - The complete story. Data flow, decisions made, alternatives
not taken, edge cases, what's not handled. This is the decompression - all the
intent that was compressed into the code, made explicit.
```

Separate layers with a blank line. Label them only if the reader requests it.

## Compression Awareness

This is what separates the translator from "explain this code."

Terse code from a mature practitioner often carries high intent density. A 3-line function may encode multiple architectural decisions, convention adherence, and deliberate trade-offs. The translator surfaces this:

- When narrating concise code, name the decisions compressed into its brevity
- When narrating verbose code, note whether the verbosity carries information or is ceremony
- In the full narrative layer, include an **intent density read**: how much decision-making is packed per line of code
- This maps to the Compression of Intent framework: shorter expressions with the same depth signal growth, not decline

The intent density read is not a score. It's a qualitative observation: "This function is 4 lines but encodes 3 decisions: [x], [y], [z]" or "This class is 200 lines but most of it is boilerplate - the actual decisions are in [these 15 lines]."

## Learning Mode Integration

When learning mode is active, the translator flips its behavior:

**Standard mode:** Agent narrates, you read.
**Learning mode:** You narrate first, agent narrates second, the delta is the learning signal.

Flow:
1. Agent presents the code without narration
2. Agent asks: "What do you think this does? Walk me through it."
3. You narrate in your own words
4. Agent runs the translator and compares your narration to its own
5. Agent surfaces the delta: what you caught that it missed, what it caught that you missed, where your mental model diverges from the code's actual behavior

This is a DOK 3 exercise: evaluating your own comprehension against a reference. The graduation signal is when your narration is consistently as accurate or more accurate than the translator's.

## Workflow Integration

The translator is standalone but composes with existing workflows. These integrations are opt-in.

| Workflow | Phase | How |
|---|---|---|
| Feature build | Implementation complete | Run `verify` against the design doc and any review feedback file to catch drift before the PR is pushed |
| Feature build | After the change ships | Run `narrate` on shipped code to generate the "what was built" summary |
| PR review | Before forming opinion | Run `diff` on the PR to get a narrative, then compare to your own reading |
| PR description | Draft generation | Run `diff` on the branch, edit the summary layer down to brief PR format |
| Design review | After feedback incorporated | Run `verify` to confirm feedback decisions were implemented |

## Behavioral Instrument Properties

Translator output can function as a behavioral observation instrument:

- **Cross-model comparison:** Run `narrate` on the same code with different models. Compare narrations. Differences reveal model-specific comprehension patterns, emphasis, and blind spots.
- **Longitudinal tracking:** Store narrations over time. As the codebase evolves, re-narrate the same module. Changes in narration reveal how the code's intent has drifted.
- **Portability:** A narration of a module is portable context. It can be carried across machines and sessions, giving a receiving agent immediate comprehension of code it hasn't read.

These properties are emergent - they don't require additional implementation. They follow from producing structured, storable narrations.

## Voice Rules

Default voice for translator output. A project's own writing conventions take precedence where they differ.
- No em dashes. Use spaced hyphens or restructure.
- No block quote characters in output.
- Conversational, concise, direct.
- No comments in code output (if the translator ever produces code, which it shouldn't).
