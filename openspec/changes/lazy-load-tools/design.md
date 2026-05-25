## Context

`task.ts` inlines all non-primary agent names + descriptions into the tool description on every `init()`. `skill.ts` inlines all skill XML blocks into the skill tool description. This costs 2-3.5k tokens per turn regardless of whether the user needs subagents or skills in that turn. Issue #13188.

Current `task.ts` pattern:
```ts
const agents = await Agent.list().then(x => x.filter(a => a.mode !== "primary"))
const description = DESCRIPTION.replace("{agents}", accessibleAgents.map(a => `- ${a.name}: ${a.description}`).join("\n"))
```

Current `skill.ts` pattern:
```ts
accessibleSkills.flatMap((skill) => [
  `  <skill>`, `    <name>${skill.name}</name>`, ...
])
```

## Goals / Non-Goals

**Goals:**
- Task tool description becomes a fixed ~200 tokens with action="list" to see available agents
- Skill tool description becomes a fixed ~150 tokens with action="list" to see available skills
- Catalog result lives in conversation history, available for subsequent turns without re-sending

**Non-Goals:**
- Changing the subagent spawning mechanism itself
- Removing the ability to specify subagent_type directly (backward compatible)
- Caching or pre-computing agent/skill catalogs across sessions

## Decisions

**Decision 1: Two-step tool call pattern (Option A from #13188)**

Add an `action` parameter to both tools. `action="list"` returns the catalog. `action="run"` (or direct `subagent_type`) launches the agent. This is the cleanest pattern and matches how MCP tools already work.

Alternative considered: Summary in description + detail on demand (Option B) — rejected because it still inlines agent names, just shorter. The full saving requires no inline content.

**Decision 2: Backward compatibility via optional action parameter**

If `subagent_type` is provided directly, the tool works exactly as before (no list call needed). The `action` parameter is optional. This means existing prompts and agent configurations continue to work without changes.

**Decision 3: Task tool parameter schema change**

Add `action` as an optional enum parameter: `"list" | "run"`. Default is `"run"` when `subagent_type` is provided, `"list"` when no `subagent_type`. Skill tool follows the same pattern.

## Risks / Trade-offs

- [Extra round-trip] First time an agent needs a subagent, it must call list then call run. → Mitigation: This is 1 extra turn at most, and saves thousands of tokens per turn. Net positive.
- [Model confusion] Some models may not understand the two-step pattern. → Mitigation: Clear tool description explaining the pattern. Models that understand tool parameters handle this well.
- [Breaking change for custom agents] Any custom agent that relies on the inline list in tool description will need to call list first. → Mitigation: action="list" is documented in the tool description. The change is backward compatible for direct subagent_type usage.
