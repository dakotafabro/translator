# World Model Grounding

When a world model is available for the codebase being translated, the translator
consults it to ground narrations in architectural context. This transforms the
output from "what this code does locally" to "what this code does in the system."

## When to Activate

World model grounding activates when:

- The code being translated belongs to a codebase with a known world model
- The world model path is configured (see Configuration below)
- The code touches a domain that has world model coverage

World model grounding is silent when:

- No world model is configured for the codebase
- The code is purely local (utility functions, formatters, test helpers)
  with no system-level connections

## Configuration

World models are discovered by looking for a `MANIFEST.yaml` at the world model
root. The translator resolves the active world model in this order:

1. `WM_REPO_DIR` environment variable (explicit path)
2. `~/.config/world-model/active.env` (managed by the world-model package)
3. A `MANIFEST.yaml` in the project root or a parent directory

For codebases without a world model, grounding is skipped. The translator still
functions in all modes - it just narrates the code without system context.

## Grounding Process

### Step 1: Domain Identification

From the code being translated, identify which world model domain(s) it touches.
Two methods, use both:

**Path-based routing** (fast, coarse):
Map file paths in the codebase to world model domains. Each project defines its
own path-to-domain mapping. Examples:
- `feature/auth` -> auth domain
- `feature/payments` -> payments domain
- `src/api/` -> api domain

**Keyword-based routing** (precise, uses frontmatter):
Each world model state file has `routing.keywords` in its YAML frontmatter.
Match terms from the code (class names, service names, API paths, content types)
against these keywords. The routing table in AGENTS.md has the full keyword map.

### Step 2: Context Retrieval

For each matched domain, read the relevant sections from:

1. **State layer** - What exists. Services, entities, data flows, API contracts.
   Read the matched file's relevant section (use `routing.lines` from frontmatter
   for precision).

2. **Causal layer** - What breaks. Check the corresponding `*-failures.md` file
   for failure modes related to the code's domain. Surface any that the code
   could trigger or should handle.

3. **Intent layer** - Why it's this way. Check `goals-and-migrations.md` and
   `adrs.md` for architectural decisions that constrain or explain the code's
   approach. Check `initiatives.yaml` for the initiative context.

4. **Prediction layer** - What could go wrong at scale. Check `blast-radius.md`
   for documented blast radius of the systems the code touches.

Do not read all four layers for every narration. Read state always. Read causal
when the code handles errors or could fail. Read intent when the code makes
architectural choices. Read prediction when the code touches Tier 0 services.

### Step 3: Weave Into Narration

The world model context is not a separate section. It's woven into the narration
at the points where it's relevant:

- When narrating a function that calls a service, name the service using world
  model vocabulary and note its tier and role
- When narrating error handling, connect to documented failure modes
- When narrating an architectural choice, connect to the ADR or migration goal
  that explains it
- When narrating a feature flag, connect to the initiative it gates

## System Position (added to narrate and diff modes)

When world model grounding is active, add a System Position section between the
summary and full narrative layers:

```
[One-liner]

[Summary]

System Position:
- Upstream: [what feeds this code - services, data sources, events]
- Downstream: [what consumes this code's output - screens, other services, analytics]
- Blast radius: [what breaks if this code fails - from prediction layer]
- Initiative: [what initiative or migration this connects to - from intent layer]

[Full narrative]
```

The System Position section answers: where does this sit, what triggers it, and
what does it affect? This is the architectural context that makes a PR review
meaningful beyond the local diff.

## Vocabulary Consistency

When the world model uses a specific term for a concept, the translator uses
that same term. This builds shared language:

- Use the service name from the world model, not a generic description
- Use the tier designation when the service has one documented
- Use initiative names as documented in the intent layer
- Use failure mode IDs when referencing documented failure modes

Over time, consistent vocabulary in translator output reinforces the world model's
language across the team. The translator becomes a teaching surface for the world
model's terminology.

## World Model Health Signals

During narration, the translator may discover:

- **Code that touches a service with no world model entry** - flag as a coverage
  gap. Note it in the output: "This code calls [service] which has no world model
  documentation."

- **Code behavior that contradicts the world model** - flag as a potential world
  model drift. Note it: "The world model says [X] but the code does [Y]."

- **Code that implements a pattern the world model doesn't document** - flag as
  an extension opportunity. Note it: "This establishes a [pattern] that could be
  documented in [layer]."

These signals are informational. They don't block the narration. But they
accumulate into a world model maintenance backlog that can be addressed during
Phase 6 (Encode) or during dedicated world model maintenance sessions.

## Without a World Model

When no world model is available, the translator still works. It narrates the
code's local behavior, decisions, and edges. The System Position section is
omitted. The narration is accurate but less contextual - like reading a sentence
without knowing the paragraph it belongs to.

This is the baseline. World model grounding is the enrichment layer on top.
