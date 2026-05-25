## Context

When `OPENCODE_CONFIG_DIR` is set, `instruction.ts` uses it as the config directory instead of `~/.config/opencode/`. This means the global `AGENTS.md` at `~/.config/opencode/AGENTS.md` is never loaded. Issue #28658 reports this as a bug — users expect global instructions to persist regardless of custom config directory.

Current code in `instruction.ts:63-66`:
```ts
const globalFiles = [
  path.join(global.config, "AGENTS.md"),
  ...(!flags.disableClaudeCodePrompt ? [path.join(global.home, ".claude", "CLAUDE.md")] : []),
]
```

`global.config` is overridden by `OPENCODE_CONFIG_DIR`, so global AGENTS.md path changes entirely.

## Goals / Non-Goals

**Goals:**
- Global AGENTS.md always loads from `~/.config/opencode/AGENTS.md`
- OPENCODE_CONFIG_DIR adds additional instruction sources, never replaces global
- Both global and custom config instructions are present in the system prompt

**Non-Goals:**
- Deduplication of overlapping instructions between global and config dir
- Changing any other OPENCODE_CONFIG_DIR behavior (e.g. database paths)

## Decisions

**Decision 1: Always prepend the canonical global AGENTS.md path**

Instead of using `global.config` (which is overridden), hardcode `~/.config/opencode/AGENTS.md` as the first global file. Then also load AGENTS.md from `global.config` (the possibly-overridden path). This ensures both are loaded.

Alternative considered: Add a separate `globalHome` vs `globalConfig` distinction — rejected because it adds conceptual complexity for a single-file fix.

**Decision 2: Deduplicate by file path**

If both paths resolve to the same file (no OPENCODE_CONFIG_DIR set), only include it once. Use the existing `Set<string>` in `systemPaths()` to deduplicate.

## Risks / Trade-offs

- [Duplicate instructions] If both global and config-dir AGENTS.md contain similar content, the system prompt gets redundant text. → Mitigation: Acceptable — redundancy is better than losing instructions. Users control both files.
- [Path resolution on non-standard platforms] `~/.config/opencode/` may not exist on all platforms. → Mitigation: The existing `fs.existsSafe()` check handles missing files gracefully.
