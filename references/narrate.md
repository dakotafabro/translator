# Narrate Mode

Translate a file, function, or code block into a plain-language narrative.

## Input

A file path, function name, class name, or pasted code block.

## Process

1. **Read the target.** Load the full code. If it's a function, include its containing context (class, imports) for grounding.

2. **Identify intent.** Before describing what the code does mechanically, answer: what problem does this solve? Why does this code exist? This is the first sentence.

3. **Walk the flow.** Trace execution from entry point through exit. Name the data transformations, not the syntax. "Takes a list of tracks and groups them by artist" not "iterates over the list using groupBy."

4. **Surface compressed decisions.** For each non-obvious choice in the code, name it:
   - Why this data structure and not another?
   - Why this access pattern?
   - What convention is being followed?
   - What alternative was implicitly rejected?
   - If the code is terse, what's packed into the brevity?

5. **Note edges.** What inputs would break this? What's explicitly not handled? What assumptions does this code make about its callers?

6. **Read intent density.** Qualitative observation: how much decision-making is compressed per line? Where are the high-density lines (lots of intent in few characters) vs low-density lines (ceremony, boilerplate, plumbing)?

## Output

Four layers when world model grounding is active, three without:
- One-liner (single sentence, the TL;DR)
- Summary (one paragraph, enough for a PR description)
- System Position (when world model is available - upstream, downstream, blast
  radius, initiative context. See world-model-grounding.md)
- Full narrative (complete story with decisions, edges, intent density)

## Audience Adjustment

- **Engineer:** Name types, patterns, and architectural decisions. Assume platform familiarity.
- **Team:** Explain trade-offs and why this approach was chosen. Assume domain familiarity but not implementation familiarity.
- **Stakeholder:** Describe the outcome. What does the user experience? What capability now exists? No implementation details.
