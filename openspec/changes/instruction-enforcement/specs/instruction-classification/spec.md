## ADDED Requirements

### Requirement: Classify instruction lines as declarative or imperative
The system SHALL classify each instruction line as either declarative or imperative at load time. A line is imperative if it contains BOTH a modal verb (MUST, ALWAYS, NEVER, SHALL, REQUIRED) AND either a file path reference or a tool-action verb (Read, Execute, Run, Check, Load, Fetch). All other lines are declarative.

#### Scenario: Imperative instruction with file path
- **WHEN** an instruction line reads "MUST Read /path/to/file.md before starting"
- **THEN** the system classifies it as imperative

#### Scenario: Imperative instruction with tool action
- **WHEN** an instruction line reads "Always execute tests before committing"
- **THEN** the system classifies it as imperative

#### Scenario: Declarative style instruction
- **WHEN** an instruction line reads "MUST use snake_case for variable names"
- **THEN** the system classifies it as declarative

#### Scenario: Plain instruction
- **WHEN** an instruction line reads "Keep functions short"
- **THEN** the system classifies it as declarative

### Requirement: Classification returns structured result
The classification function SHALL return a structured object containing `declarative: string[]` and `imperative: string[]` arrays, preserving original line content.

#### Scenario: Mixed content AGENTS.md
- **WHEN** an AGENTS.md contains both declarative and imperative lines
- **THEN** the function returns both arrays populated with their respective lines

### Requirement: Classification is model-agnostic
The classification logic SHALL be implemented as a pure synchronous function with no LLM calls, using only regex pattern matching.

#### Scenario: Classification performance
- **WHEN** classification runs on a 100-line AGENTS.md
- **THEN** it completes in under 1ms with no network calls
