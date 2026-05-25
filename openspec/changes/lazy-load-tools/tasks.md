## 1. Task Tool Refactor

- [ ] 1.1 Add `action` optional parameter to task tool schema: enum `["list", "run"]`, default `"run"` when `subagent_type` provided
- [ ] 1.2 Implement `action="list"` handler that returns agent catalog (names + descriptions) as tool result
- [ ] 1.3 Remove inline agent catalog from task tool static description
- [ ] 1.4 Update task tool description to explain two-step pattern: call with action="list" to see agents, or provide subagent_type directly

## 2. Skill Tool Refactor

- [ ] 2.1 Add `action` optional parameter to skill tool schema: enum `["list", "load"]`, default `"load"` when `name` provided
- [ ] 2.2 Implement `action="list"` handler that returns skill catalog (names + descriptions + locations) as tool result
- [ ] 2.3 Remove inline skill XML blocks from skill tool static description
- [ ] 2.4 Update skill tool description to explain two-step pattern: call with action="list" to see skills, or provide name directly

## 3. System Prompt Update

- [ ] 3.1 Update `system.ts` `skills()` function to use brief listing instead of verbose inline when generating skill section
- [ ] 3.2 Verify backward compatibility: existing agent configs that specify subagent_type directly still work

## 4. Integration & Verification

- [ ] 4.1 Run `bun typecheck` from `packages/opencode`
- [ ] 4.2 Verify token cost: tool descriptions for task + skill are under 400 tokens combined
- [ ] 4.3 Test two-step flow: call action="list" → receive catalog → call subagent_type → agent launches
