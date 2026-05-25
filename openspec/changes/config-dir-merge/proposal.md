## Why

OPENCODE_CONFIG_DIR currently overrides the global AGENTS.md path instead of adding to it. Issue #28658: when OPENCODE_CONFIG_DIR is set, the global ~/.config/opencode/AGENTS.md is no longer loaded. This breaks multi-layer instruction setups where users need both global and project-level instructions active simultaneously. Users with custom config dirs lose all global instructions, which is never the intent.

## What Changes

- Change OPENCODE_CONFIG_DIR behavior from override to merge
- Global AGENTS.md (~/.config/opencode/AGENTS.md) always loads regardless of OPENCODE_CONFIG_DIR
- OPENCODE_CONFIG_DIR adds additional instruction sources on top of global, never replaces
- instruction.ts: systemPaths() always includes global AGENTS.md path, then appends OPENCODE_CONFIG_DIR paths

## Capabilities

### New Capabilities
- `config-merge`: Global + OPENCODE_CONFIG_DIR instructions merge instead of override

### Modified Capabilities

## Impact

- `packages/opencode/src/session/instruction.ts` — fix systemPaths() to always include global AGENTS.md
- `packages/opencode/src/config/config.ts` — merge OPENCODE_CONFIG_DIR paths instead of replacing global config path
