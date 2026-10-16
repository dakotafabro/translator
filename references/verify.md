# Verify Mode

Compare a design document against its implementation to surface alignment, drift, gaps, and extras.

## Input

Two artifacts:
1. **Design doc** - a `.md` file from the project's design doc directory or a provided path
2. **Implementation** - a file, module, PR diff, or directory

Optionally:
3. **Review feedback file** - a JSON file of resolved review feedback kept next to the design doc (for example `<design-doc>.feedback.json`, with entries typed as decisions, questions, risks or alternatives). If it exists, load it automatically.

## Process

1. **Load the design doc.** Read the full document. Extract: stated goals, architectural decisions, data flow description, risk assessment, PR stack plan.

2. **Load the feedback (if present).** Read the feedback file. Extract: resolved decisions (type: "decision"), answered questions, identified risks, accepted alternatives. These are team-level constraints that the implementation should reflect.

3. **Load the implementation.** Read the code. If it's a PR diff, read the diff. If it's a directory, read the key files (entry points, models, use cases).

4. **Narrate each independently.** Using the narrate mode process, produce a plain-language narrative of:
   - What the design doc says should exist
   - What the feedback says should have changed from the original plan
   - What the code actually does

5. **Compare the narratives.** Produce a match report with four categories:

   **Aligned** - Things the design doc (+ feedback) said that the code does. These confirm the implementation matches intent.

   **Drifted** - Things the design doc said that the code does differently. Not wrong, but different. Name both what was planned and what was built. The drift may be intentional (discovered a better approach during build) or unintentional (forgot a constraint).

   **Missing** - Things the design doc (+ feedback decisions) said that aren't in the code. Either not yet implemented, deliberately deferred, or dropped.

   **Extra** - Things in the code that the design doc didn't mention. New capabilities, additional error handling, edge cases discovered during build. These are fine but should be documented.

6. **Check feedback incorporation.** For each resolved decision in the feedback file, verify it appears in the implementation. If a team decision was not incorporated, flag it explicitly. This is the highest-priority finding.

## Output

```
Verify: [design doc name] vs [implementation target]

Feedback incorporated: [N of M decisions reflected in code]

Aligned:
- [what matches]

Drifted:
- [plan said X, code does Y]

Missing:
- [plan said X, not found in code]

Extra:
- [code does X, not in plan]

Unincorporated feedback:
- [decision from the feedback file not reflected in code]
```

No judgment in the output. The verify report is a delta, not a grade. The practitioner decides what to do with it.

## When to Run

- **Implementation complete, before the PR is pushed:** Catch drift while it's cheap to fix
- **After the change ships:** Generate the plan-vs-reality section for the design doc's post-build appendix
- **After addressing PR review feedback:** Verify that review comments were incorporated
- **After design review feedback:** Verify that team decisions were implemented

## Compression Signal

If the design doc is long and the implementation is short (or vice versa), note the compression ratio. A short implementation that satisfies a detailed design doc is evidence of high intent density. A long implementation against a short design doc may indicate the design was underspecified or the implementation discovered complexity the design didn't anticipate. Both are worth naming.
