## 1. Instruction Classification

- [x] 1.1 Add `classify()` function to `instruction.ts` that parses instruction content and returns `{ declarative: string[], imperative: string[] }`
- [x] 1.2 Add regex patterns for imperative detection: modal verbs (MUST, ALWAYS, NEVER, SHALL, REQUIRED) combined with path references or tool-action verbs (Read, Execute, Run, Check, Load, Fetch)
- [ ] 1.3 Add unit tests for classification: imperative with file path, imperative with tool action, declarative style, plain instruction, mixed content

## 2. Mandatory Framing in System Prompt

- [x] 2.1 Modify `instruction.ts` `system()` function to call `classify()` on loaded instruction content
- [x] 2.2 Modify `system.ts` `environment()` to accept classified instructions and wrap imperative blocks in `<mandatory>` tags
- [x] 2.3 Ensure `<mandatory>` blocks appear before declarative instruction blocks in the system prompt array
- [x] 2.4 Format imperative instructions with file references as explicit action directives in the mandatory block

## 3. System Prompt Templates

- [x] 3.1 Add mandatory instruction section to `default.txt` explaining `<mandatory>` tag convention
- [x] 3.2 Add mandatory instruction section to `anthropic.txt`
- [x] 3.3 Add mandatory instruction section to other provider-specific templates (gpt.txt, gemini.txt, beast.txt, kimi.txt, trinity.txt, codex.txt)

## 4. Integration & Verification

- [x] 4.1 Run `bun typecheck` from `packages/opencode`
- [x] 4.2 Verify classification function works with existing AGENTS.md content in the repo
- [x] 4.3 Test with a custom AGENTS.md containing imperative instructions and confirm `<mandatory>` tags appear in system prompt
