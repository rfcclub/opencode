## Context

OpenCode's instruction pipeline (`instruction.ts`) loads AGENTS.md files and `opencode.json` instruction paths, reads their content, and injects it into the system prompt as passive text. The model sees instructions as reference material, not commands. This is the root cause of #29206 — agents ignore imperative instructions like "Read X before starting" because nothing in the pipeline distinguishes action directives from style guides.

Current flow: `systemPaths()` → `read()` → `system()` → inject as `Instructions from: <path>\n<content>` into system prompt array. No classification, no enforcement.

## Goals / Non-Goals

**Goals:**
- Classify instruction lines as declarative vs imperative at load time
- Imperative instructions get mandatory framing in the system prompt so models treat them as actions
- Imperative instructions with file references (e.g. "Read /path/to/file") are surfaced before the first user task

**Non-Goals:**
- Auto-executing tool calls on behalf of the agent (the agent still decides how to comply)
- Validating that instructions were followed after the fact
- Modifying the LLM inference parameters or model behavior directly

## Decisions

**Decision 1: Classification via regex pattern matching at load time**

Classify instructions using action verb patterns: lines containing MUST, ALWAYS, NEVER, SHALL, Read, Execute, Run, Check followed by a path-like string are imperative. Everything else is declarative.

Alternative considered: LLM-based classification at load time — rejected because it adds latency and an extra API call per session boot. Regex is sufficient for the common patterns.

**Decision 2: Mandatory framing via `<mandatory>` XML tags in system prompt**

Imperative instructions are wrapped in `<mandatory>` tags within the system prompt. This is a convention already used by other prompt engineering tools (e.g. Claude's system prompt conventions). Models treat XML-tagged sections with higher compliance.

Alternative considered: Inject imperative instructions as the first user message — rejected because it breaks the system/user message boundary and could confuse multi-turn conversation logic.

Alternative considered: Pre-task tool call injection — rejected because it modifies the tool call protocol and could interfere with agent autonomy.

**Decision 3: Classification lives in instruction.ts, enforcement in system.ts**

Classification is a pure parsing function that belongs in the instruction module. Enforcement (mandatory framing) is a presentation concern that belongs in the system prompt module. This keeps concerns separated and testable.

## Risks / Trade-offs

- [Over-classification] Lines like "MUST use snake_case" are declarative style rules, not imperative actions. → Mitigation: Only classify as imperative if the line contains BOTH a modal verb AND a path-like reference or a tool-action verb (Read, Execute, Run, Check). Pure style directives stay declarative.
- [Model non-compliance] Some models may still ignore `<mandatory>` tags. → Mitigation: This is the same risk as current behavior — at worst, no improvement. At best, significant improvement for models that respect XML framing.
- [Instruction authors must write explicit instructions] Users need to know the convention. → Mitigation: Document in opencode docs. The classification heuristics are lenient enough to catch common patterns.
