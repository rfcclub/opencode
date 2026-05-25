## ADDED Requirements

### Requirement: Task tool supports action parameter
The task tool SHALL accept an optional `action` parameter with values `"list"` or `"run"`. When `action="list"`, the tool returns the catalog of available agent types. When `action="run"` or when `subagent_type` is provided directly, the tool launches an agent as before.

#### Scenario: List available agents
- **WHEN** the task tool is called with action="list"
- **THEN** the tool returns a list of available agent types with names and descriptions

#### Scenario: Launch agent directly
- **WHEN** the task tool is called with subagent_type="explore"
- **THEN** the tool launches the explore agent without requiring a prior list call

#### Scenario: Backward compatibility
- **WHEN** the task tool is called with only subagent_type and no action parameter
- **THEN** the tool behaves identically to the current implementation

### Requirement: Skill tool supports action parameter
The skill tool SHALL accept an optional `action` parameter with values `"list"` or `"load"`. When `action="list"`, the tool returns the catalog of available skills. When `action="load"` or when a skill `name` is provided directly, the tool loads the skill as before.

#### Scenario: List available skills
- **WHEN** the skill tool is called with action="list"
- **THEN** the tool returns a list of available skills with names, descriptions, and locations

#### Scenario: Load skill directly
- **WHEN** the skill tool is called with name="effect"
- **THEN** the tool loads the effect skill without requiring a prior list call

### Requirement: Tool descriptions are fixed-length
The static tool description for task and skill tools SHALL NOT contain the full agent/skill catalog. The description SHALL only contain instructions on how to use the action parameter.

#### Scenario: Token cost before first list call
- **WHEN** a session starts with 21 agents and 7 skills
- **THEN** the task and skill tool descriptions consume under 400 tokens combined (vs ~3500 currently)

#### Scenario: Token cost after list call
- **WHEN** an agent calls action="list" for tasks
- **THEN** the catalog content lives in conversation history and is available for subsequent turns without re-injection
