# Diff Mode

Translate a git diff, PR, or set of changes into a plain-language narrative of what changed and why.

## Input

A git diff, PR URL, branch comparison, or pasted diff output.

## Process

1. **Fetch the diff.** If given a PR URL, fetch the full diff. If given a branch, diff against the base.

2. **Identify the before state.** One sentence: what did the system do before this change?

3. **Identify the after state.** One sentence: what does the system do now?

4. **Narrate the delta by intent, not by file.** Group changes by what they accomplish, not by where they live. A change that touches 4 files to add one capability is one narrative unit, not four.

5. **Surface the motivation.** Why was this change made? If the diff includes test changes, what behavior was being validated? If it includes deletions, what was removed and why?

6. **Flag unintended side effects.** Anything that looks like it changed behavior beyond the stated intent. Renamed variables that might break callers. Modified shared utilities. Changed default values.

7. **Note what didn't change.** If the diff is narrow, name what was deliberately left alone. Scope discipline is a decision worth narrating.

## Output

Four layers when world model grounding is active, three without:
- One-liner (single sentence describing the change)
- Summary (one paragraph, enough for a PR description or Slack message)
- System Position (when world model is available - what system context this
  change sits in, what it connects to upstream/downstream, blast radius.
  See world-model-grounding.md)
- Full narrative (complete story of before/after/motivation/side effects)

## Audience Adjustment

- **Engineer:** Name the specific types, functions, and patterns affected. Call out migration concerns.
- **Team:** Explain what capability changed and what the trade-offs were. Good for design preview updates.
- **Stakeholder:** Describe what's different for the user. No file names, no types.
