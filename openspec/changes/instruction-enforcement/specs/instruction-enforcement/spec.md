## ADDED Requirements

### Requirement: Imperative instructions get mandatory framing
The system SHALL wrap imperative instruction blocks in `<mandatory>` XML tags within the system prompt, separate from declarative instructions.

#### Scenario: Imperative instructions in system prompt
- **WHEN** the system prompt is assembled and imperative instructions exist
- **THEN** imperative instructions appear within `<mandatory>...</mandatory>` tags
- **THEN** declarative instructions appear in their normal `Instructions from: <path>` format

#### Scenario: No imperative instructions
- **WHEN** all instructions are declarative
- **THEN** the system prompt contains no `<mandatory>` tags

### Requirement: Mandatory section appears before declarative instructions
The system SHALL order the system prompt so that `<mandatory>` blocks appear before declarative instruction blocks.

#### Scenario: System prompt ordering
- **WHEN** both imperative and declarative instructions exist
- **THEN** mandatory blocks appear first in the system prompt array
- **THEN** declarative blocks follow after

### Requirement: Imperative instructions reference file paths as actions
The system SHALL format imperative instructions that reference file paths as explicit action directives (e.g. "Read /path/to/file before proceeding") rather than passive references.

#### Scenario: File path in imperative instruction
- **WHEN** an imperative instruction contains "MUST Read /home/user/BLADE/EDGE.md"
- **THEN** the mandatory block formats it as an explicit action: "Before any other action, you MUST read: /home/user/BLADE/EDGE.md"

#### Scenario: Multiple imperative file references
- **WHEN** imperative instructions reference multiple files
- **THEN** each file reference appears as a separate action item in the mandatory block
