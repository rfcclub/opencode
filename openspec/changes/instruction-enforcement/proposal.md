## Why

OpenCode agents routinely ignore instructions in AGENTS.md and opencode.json instruction paths. Issue #29206 documents this: users write instructions but agents start tasks without reading referenced documents. The root cause is instructions are injected as passive text into the system prompt — the model treats them as reference material, not executable commands. There is no mechanism to enforce that action-oriented instructions (e.g. "Read X before starting", "MUST execute Y at boot") are performed as tool calls before the agent proceeds.

## What Changes

- Add instruction classification: distinguish declarative instructions (style guides, conventions) from imperative instructions (action verbs like "Read", "Execute", "Must", "Always")
- Add a pre-task enforcement step: before the agent processes the first user message, scan imperative instructions and surface them as mandatory pending actions
- Add instruction priority signal in the system prompt: imperative instructions get stronger framing (wrapped in `<mandatory>` tags or injected as the first user message) so models treat them as actions, not reference
- Modify the instruction loading pipeline (`instruction.ts`) to parse and classify instructions before injecting them into the system prompt

## Capabilities

### New Capabilities
- `instruction-classification`: Parse AGENTS.md and instructions content to classify lines as declarative vs imperative based on action verbs and modal markers (MUST, ALWAYS, NEVER, Read, Execute, etc.)
- `instruction-enforcement`: Enforce imperative instructions are executed before the agent proceeds with user tasks, via pre-task action injection or mandatory framing in system prompt

### Modified Capabilities

## Impact

- `packages/opencode/src/session/instruction.ts` — add classification and enforcement logic
- `packages/opencode/src/session/system.ts` — integrate enforcement into system prompt assembly
- `packages/opencode/src/session/prompt.ts` — pre-task enforcement step before first LLM call
- System prompt templates (`default.txt`, `anthropic.txt`, etc.) — add mandatory instruction framing section
