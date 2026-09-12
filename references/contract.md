# Contract Mode

Extract the human-readable API contract from a module, class, or interface.

## Input

A module path, class name, interface, or package.

## Process

1. **Read the target.** Load the public surface area - public functions, exposed types, constructor parameters, configuration.

2. **What it accepts.** List every input this thing takes: parameters, configuration, injected dependencies, implicit context (like coroutine scope or lifecycle). Name the types in plain language. "A list of track IDs" not "List<String>."

3. **What it produces.** List every output: return values, emitted state, side effects (network calls, database writes, navigation events, analytics). Include async outputs (flows, callbacks, state emissions).

4. **What it promises.** Invariants and guarantees. Does it handle errors? Does it deduplicate? Does it cache? Does it retry? Does it guarantee ordering? These are the things a caller can rely on without reading the implementation.

5. **What it assumes.** Preconditions and dependencies. Must be called on main thread? Requires an active session? Assumes the list is non-empty? These are the things that break if violated.

6. **What it doesn't do.** Explicit scope boundaries. If this is a repository, it doesn't handle UI state. If this is a use case, it doesn't persist anything. Naming what's out of scope prevents callers from misusing it.

## Output

Not the three-layer format. Contract mode produces a structured contract:

```
[Name] - [one sentence purpose]

Accepts:
- [input 1]: [plain language description]
- [input 2]: [plain language description]

Produces:
- [output 1]: [plain language description]
- [side effect 1]: [plain language description]

Promises:
- [guarantee 1]
- [guarantee 2]

Assumes:
- [precondition 1]
- [precondition 2]

Does not:
- [explicit scope boundary 1]
- [explicit scope boundary 2]
```

## Audience Adjustment

- **Engineer:** Include type names alongside plain language. Note thread-safety and lifecycle concerns.
- **Team:** Focus on what the module does in the system. Skip threading details. Good for design docs.
- **Stakeholder:** Skip entirely. Contracts are engineer artifacts. If a stakeholder needs to understand a module, use `narrate` mode instead.
