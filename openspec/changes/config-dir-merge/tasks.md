## 1. Fix Global AGENTS.md Loading

- [x] 1.1 In `instruction.ts`, add the canonical global AGENTS.md path (`~/.config/opencode/AGENTS.md`) as a separate constant, independent of `global.config`
- [x] 1.2 Modify `globalFiles` array to include both canonical global path and `global.config` AGENTS.md path
- [x] 1.3 Modify `systemPaths()` to always include canonical global path, then append OPENCODE_CONFIG_DIR path

## 2. Deduplication

- [x] 2.1 Verify existing `Set<string>` in `systemPaths()` handles deduplication when both paths resolve to the same file
- [x] 2.2 Add test: OPENCODE_CONFIG_DIR pointing to default config dir loads AGENTS.md only once

## 3. Integration & Verification

- [x] 3.1 Run `bun typecheck` from `packages/opencode`
- [x] 3.2 Test with OPENCODE_CONFIG_DIR set and verify both global and custom AGENTS.md appear in instructions
