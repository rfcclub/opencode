## Why

Task and skill tool descriptions inline the full agent/skill catalog on every turn, burning 2-3.5k tokens per turn regardless of need. Issue #13188: with 21+ agents and 7+ skills, fixed overhead consumes 15-20% of context before conversation starts. Lazy-loading on demand eliminates this waste and scales gracefully — 100 agents costs the same as 3 until the list is actually needed.

## What Changes

- Convert task tool to two-step call: action="list" returns catalog, subagent_type launches agent
- Convert skill tool to two-step call: action="list" returns available skills, name loads skill
- Remove inline agent/skill descriptions from static tool descriptions
- Catalog result lives in conversation history, naturally available for subsequent turns without re-sending

## Capabilities

### New Capabilities
- `lazy-tool-loading`: Tool catalogs loaded on demand via action=list instead of inlined statically in tool descriptions

### Modified Capabilities

## Impact

- `packages/opencode/src/tool/task.ts` — add action parameter, two-step flow
- `packages/opencode/src/tool/skill.ts` — add action parameter, two-step flow
- System prompt token cost reduced by ~2-3.5k/turn for multi-agent setups
