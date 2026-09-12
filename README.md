# Translator

A decompression instrument for code. Turns implementation into plain-language narrative that makes intent visible, verifiable, and portable.

## Why

The daily work of engineering is compressing intent into code. The translator runs that process in reverse - decompressing code back into the intent it encodes. The interesting question is whether the round-trip is lossless, and when it's not, where the information leaks.

Built on the Compression of Intent framework: as practitioners mature, their expressions get shorter but their intent density increases. The translator is compression-aware - it surfaces not just what code does, but how much decision-making is packed into its brevity.

## World Model Integration

When the code belongs to a codebase with a known world model, the
translator consults it to ground narrations in architectural context. Every
narration gets a System Position section (upstream, downstream, blast radius,
initiative context) and uses the world model's vocabulary consistently. This
builds shared language across the team - the translator teaches the world model's
terms through every narration it produces.

Without a world model, the translator still works - it just narrates locally.

## Modes

| Mode | Input | Output |
|---|---|---|
| `narrate` | File, function, or code block | Plain-language story with intent density read + system position |
| `diff` | Git diff or PR | Narrative of what changed, grouped by intent not by file |
| `contract` | Module, class, or interface | Human-readable API contract (accepts, produces, promises, assumes) |
| `verify` | Design doc + implementation | Alignment report (matched, drifted, missing, extra) |

## Usage

Say any of these to activate:
- "translate this" / "narrate this file" / "what does this do in plain language"
- "what changed" / "narrate this PR"
- "what does this promise" / "what's the contract"
- "does this match the design doc" / "verify alignment"

## Files

- `SKILL.md` - Full skill definition and behavioral instructions
- `references/narrate.md` - Narrate mode template
- `references/diff.md` - Diff mode template
- `references/contract.md` - Contract mode template
- `references/verify.md` - Verify mode template
