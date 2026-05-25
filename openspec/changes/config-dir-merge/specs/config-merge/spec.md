## ADDED Requirements

### Requirement: Global AGENTS.md always loads
The system SHALL always load the global AGENTS.md from the canonical `~/.config/opencode/AGENTS.md` path, regardless of whether OPENCODE_CONFIG_DIR is set.

#### Scenario: No OPENCODE_CONFIG_DIR set
- **WHEN** OPENCODE_CONFIG_DIR is not set
- **THEN** the system loads AGENTS.md from `~/.config/opencode/AGENTS.md`

#### Scenario: OPENCODE_CONFIG_DIR is set
- **WHEN** OPENCODE_CONFIG_DIR is set to `/custom/config`
- **THEN** the system loads AGENTS.md from both `~/.config/opencode/AGENTS.md` AND `/custom/config/AGENTS.md`

### Requirement: OPENCODE_CONFIG_DIR adds instructions, never replaces
The system SHALL treat OPENCODE_CONFIG_DIR as an additional source of instructions, not a replacement for the global config directory.

#### Scenario: Custom config with project-level instructions
- **WHEN** OPENCODE_CONFIG_DIR=/custom/config and the project has its own AGENTS.md
- **THEN** three instruction sources are loaded: global AGENTS.md, custom config AGENTS.md, and project AGENTS.md

### Requirement: Deduplicate by resolved file path
The system SHALL not load the same file twice. If the canonical global path and the OPENCODE_CONFIG_DIR path resolve to the same file, the system loads it once.

#### Scenario: OPENCODE_CONFIG_DIR points to default config
- **WHEN** OPENCODE_CONFIG_DIR=~/.config/opencode
- **THEN** the global AGENTS.md appears only once in the instruction set
