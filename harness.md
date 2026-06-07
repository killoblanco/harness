# AI Harness Definition Manual

> **What is this file?** This is a prescriptive instruction set. When an AI agent reads this file, it generates a complete project harness — an externalized state infrastructure that enables agents to work effectively across multiple sessions despite having no memory between context windows.

## Preamble

### Instructions for the Agent

1. **Read this entire file before taking any action.** Understand the full scope before generating anything.
2. **Run Phase 0 first.** If a `.harness/` directory already exists at the project root, abort immediately — do not generate or modify anything.
3. **Language rules:**
   - All generated files, code, and documentation must be written in English.
   - Always respond to the user in the same language they use to communicate with you.
4. **Ask questions freely.** There is no limit on clarifying questions. Ask as many as needed to fully understand the project before generating the harness.
5. **Required information before starting:**
   - Ticket key prefix (e.g., "DS", "AUTH", "PLAT") — used for naming ticket files as `{PREFIX}-001.md`
   - Any existing conventions the user wants preserved
   - Team size and workflow preferences (solo dev, small team, large team)

### What You Will Produce

A `.harness/` directory at the project root with the following structure:

```
.harness/
├── agents/                  → Agent definitions
│   ├── developer.md         → Primary coding agent
│   ├── reviewer.md          → Code review agent
│   ├── architect.md         → Planning/design agent (medium+ complexity)
│   └── orchestrator.md      → Lead agent for multi-agent mode (large/monorepo only)
├── memory/                  → Long-term memory between sessions
│   ├── sessions.md          → Session log
│   ├── decisions.md         → Architectural decisions
│   └── progress.md          → Feature/task progress tracking
├── skills/                  → Stack-specific skills (generated based on project)
├── templates/
│   ├── tickets/
│   │   ├── story.md         → Story template
│   │   ├── bug.md           → Bug report template
│   │   ├── task.md          → Task template
│   │   └── spike.md         → Research spike template
│   └── docs/
│       ├── adr.md           → Architecture Decision Record template
│       ├── runbook.md       → Runbook template
│       ├── technical-doc.md → Technical documentation template
│       └── meeting-notes.md → Meeting notes template
├── tickets/                 → Individual tickets ({PREFIX}-001.md, {PREFIX}-002.md, ...)
├── docs/                    → Individual documentation pages (slug-name.md)
├── AGENTS.md                → Operational rules for running agents
├── CLAUDE.md                → Project-specific Claude behavior
└── USER.md                  → User preferences
```

### Definition: AI Harness

An AI harness is externalized state infrastructure that solves the "shift engineer" problem: each new agent session arrives with no memory of previous sessions. The harness creates persistent, machine-readable artifacts that allow each new session to:

1. Rapidly acquire context (what was done, what's next, what decisions were made)
2. Continue productive work without repeating discoveries
3. Coordinate with other agents when needed
4. Maintain quality standards consistently across sessions

---

## Phase 0: Pre-flight Check

Before anything else, check whether the harness already exists.

- If a `.harness/` directory exists at the project root: **STOP immediately.** Inform the user that the harness is already defined for this project and exit without generating or modifying any files.
- If `.harness/` does not exist: proceed to Phase 1.

---

## Phase 1: Project Analysis

Before generating any files, analyze the target project to determine what kind of harness to produce.

### Detection Checklist

Execute these checks in order:

1. **Package manager / language detection:**
   - Look for: `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `build.gradle`, `Gemfile`, `*.csproj`, `Makefile`, `CMakeLists.txt`
   - Determine: primary language, framework, package manager

2. **Complexity assessment:**
   - Count total files and directories (exclude `node_modules`, `.git`, `vendor`, `dist`, `build`)
   - Estimate lines of code
   - Classify:
     - **Small**: <5,000 LOC or <50 files
     - **Medium**: 5,000–50,000 LOC or 50–500 files
     - **Large**: >50,000 LOC or >500 files

3. **Architecture pattern detection:**
   - Check for monorepo indicators: `workspaces` in package.json, `lerna.json`, `/packages/`, `/apps/`, `pnpm-workspace.yaml`
   - Check for microservices: multiple `Dockerfile`s, `docker-compose.yml` with multiple services
   - Check for library patterns: `src/index.ts` or `src/lib/` as sole entry point
   - Classify: `monolith` | `monorepo` | `microservices` | `library`

4. **Test framework detection:**
   - Look for: `jest.config`, `vitest.config`, `pytest.ini`, `conftest.py`, `.rspec`, `*_test.go`, `Cargo.toml [dev-dependencies]`
   - Note: which test runner, where tests live, how to run them

5. **CI/CD detection:**
   - Look for: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/`, `bitbucket-pipelines.yml`

6. **Existing agent configuration:**
   - Check for: `CLAUDE.md`, `.claude/`, `.cursor/`, `.github/copilot-instructions.md`
   - If found: read and incorporate relevant rules into the generated harness

7. **Git history analysis:**
   - Check branch strategy (main/develop, trunk-based, gitflow)
   - Check commit message conventions (conventional commits, etc.)
   - Check recent activity patterns

### Analysis Output

Form a mental model with these attributes (do not write this anywhere — use it to guide generation):

| Attribute | Values |
|-----------|--------|
| `stack` | Primary language + framework (e.g., "TypeScript + Next.js") |
| `complexity_tier` | `small` \| `medium` \| `large` |
| `architecture_pattern` | `monolith` \| `monorepo` \| `microservices` \| `library` |
| `test_framework` | Detected testing setup |
| `agent_mode` | `single-agent` (small/medium) \| `multi-agent` (large/monorepo) |

### Agent Mode Decision

- **Single-agent mode**: For small and medium projects. One agent works incrementally across sessions. Simpler harness with no orchestrator.
- **Multi-agent mode**: For large projects and monorepos. An orchestrator agent coordinates specialized sub-agents working in parallel. Includes orchestrator definition and coordination rules.

---

## Phase 2: Scaffold Generation

Create the directory structure and all files. Execute in this exact order.

### Step 2.1: Create Directories

```
.harness/
.harness/agents/
.harness/memory/
.harness/skills/
.harness/templates/tickets/
.harness/templates/docs/
.harness/tickets/
.harness/docs/
```

### Step 2.2: Create Template Files

Generate each template file using the specifications in **Appendix C**.

**Ticket templates** (always created):
- `.harness/templates/tickets/story.md`
- `.harness/templates/tickets/bug.md`
- `.harness/templates/tickets/task.md`
- `.harness/templates/tickets/spike.md`

**Documentation templates** (always created):
- `.harness/templates/docs/adr.md`
- `.harness/templates/docs/runbook.md`
- `.harness/templates/docs/technical-doc.md`
- `.harness/templates/docs/meeting-notes.md`

### Step 2.3: Create Agent Definitions

Generate agent files using the structure defined in **Appendix E**.

**Always created:**
- `.harness/agents/developer.md` — Primary coding agent
- `.harness/agents/reviewer.md` — Code review agent

**Created for medium+ complexity:**
- `.harness/agents/architect.md` — Planning and design agent

**Created only for multi-agent mode (large/monorepo):**
- `.harness/agents/orchestrator.md` — Lead coordination agent

### Step 2.4: Create Memory Files

- `.harness/memory/sessions.md` — Initialize with header and format instructions
- `.harness/memory/decisions.md` — Initialize with header and ADR-lite format
- `.harness/memory/progress.md` — Initialize with empty feature tracking structure

### Step 2.5: Create Skills

Based on the detected stack, generate relevant skill files in `.harness/skills/`. Each skill describes a repeatable operation the agent can perform.

**Common skills** (generate for all projects):
- `run-tests.md` — How to run the test suite
- `build.md` — How to build the project
- `lint.md` — How to lint/format code

**Stack-specific skills** (generate when applicable):
- `deploy.md` — Deployment procedure (if CI/CD detected)
- `db-migrate.md` — Database migration (if ORM/migration tool detected)
- `dev-server.md` — Start development server (if web project)
- `docker.md` — Container operations (if Docker detected)

### Step 2.6: Create Root Configuration Files

- `.harness/CLAUDE.md` — See Phase 3 for content generation rules
- `.harness/AGENTS.md` — See Phase 3 for content generation rules
- `.harness/USER.md` — Create with placeholder structure

---

## Phase 3: Configuration & Content Generation

Fill each generated file with project-specific content.

### 3.1: CLAUDE.md Generation

This file defines Claude's behavior when working on this specific project. Generate based on analysis:

```markdown
# Project: {project_name}

## Overview
{One paragraph describing what this project is and does}

## Stack
- Language: {detected language}
- Framework: {detected framework}
- Package Manager: {detected package manager}
- Test Framework: {detected test framework}

## Commands
- Build: `{detected build command}`
- Test: `{detected test command}`
- Lint: `{detected lint command}`
- Dev: `{detected dev server command}`

## Code Style
{Derived from linter configs, editorconfig, or observed patterns}

## Project Structure
{Key directories and their purposes}

## Rules
- {Project-specific rules derived from analysis}
```

### 3.2: AGENTS.md Generation

This file contains the operational rules all agents follow. Use the full specification from **Appendix D** to generate this file, adapting it to the project's specifics.

### 3.3: USER.md Generation

Create with this structure (user fills in their preferences):

```markdown
# User Preferences

## Communication
- Preferred language: {detected from conversation}
- Detail level: {concise | detailed | verbose}

## Workflow
- Branch strategy: {detected or ask}
- Commit style: {detected or ask}
- PR preferences: {single large | many small}

## Conventions
- {Any additional preferences the user specified}
```

### 3.4: Agent Definition Content

For each agent file, generate content following the structure in **Appendix E**, customized to the project's stack and tools.

### 3.5: Memory System Initialization

**sessions.md:**
```markdown
# Session Log

## Format
Each session entry follows this structure:

### {DATE} — Session {N}
- **Agent**: {agent type}
- **Objective**: {what was planned}
- **Outcome**: {what was achieved}
- **Next steps**: {what the next session should pick up}
- **Commits**: {list of commit hashes}

---

{Sessions will be logged here}
```

**decisions.md:**
```markdown
# Architectural Decisions

## Format
Each decision follows ADR-lite format:

### ADR-{N}: {Title}
- **Date**: {date}
- **Status**: {proposed | accepted | deprecated | superseded}
- **Context**: {what prompted this decision}
- **Decision**: {what was decided}
- **Consequences**: {what follows from this decision}

---

{Decisions will be logged here}
```

**progress.md:**
```markdown
# Progress Tracking

## Current Sprint
{Sprint name or "No sprints configured"}

## Features
| Key | Title | Status | Sessions | Notes |
|-----|-------|--------|----------|-------|
| | | | | |

## Status Legend
- `not_started` — Not yet begun
- `in_progress` — Actively being worked on
- `blocked` — Cannot proceed (see notes)
- `in_review` — Code complete, awaiting review
- `complete` — Done and verified
```

### 3.6: Skills Content

Each skill file follows this format:

```markdown
# Skill: {name}

## Description
{What this skill does}

## Trigger
{When to use this skill}

## Steps
1. {Step 1}
2. {Step 2}
...

## Verification
{How to confirm the skill executed successfully}

## Common Issues
- {Issue}: {Resolution}
```

Generate actual commands and steps based on the detected stack.

---

## Phase 4: Verification

After generating all files, perform these checks:

### 4.1: Structure Verification

Confirm all expected files exist:
- [ ] `.harness/` directory exists
- [ ] All agent files present (conditional ones only if applicable)
- [ ] All memory files present
- [ ] All template files present
- [ ] CLAUDE.md, AGENTS.md, USER.md present
- [ ] At least 3 skill files generated

### 4.2: Content Verification

- [ ] CLAUDE.md contains accurate build/test/lint commands (verify they work)
- [ ] AGENTS.md contains complete session lifecycle rules
- [ ] All templates have valid YAML frontmatter
- [ ] No placeholder text remains unfilled (except USER.md which the user fills)

### 4.3: Consistency Verification

- [ ] Ticket key prefix is consistent across all references
- [ ] Agent names referenced in AGENTS.md match actual agent files
- [ ] Skills referenced in agent definitions exist in the skills directory

### 4.4: Final Report

Present to the user:
- Summary of what was generated
- The detected stack and complexity tier
- Which agent mode was selected and why
- Any recommendations for next steps (e.g., "Fill in USER.md with your preferences")

---

## Appendix A: Ticket Frontmatter Schema

All valid fields for ticket files in `.harness/tickets/`:

| Field | Type | Required | Values / Description |
|-------|------|----------|---------------------|
| `key` | string | yes | `{PREFIX}-{NUMBER}` format |
| `title` | string | yes | Short descriptive title |
| `type` | enum | yes | `story` \| `bug` \| `task` \| `spike` \| `epic` \| `improvement` |
| `status` | enum | yes | `backlog` \| `todo` \| `in_progress` \| `in_review` \| `done` \| `blocked` \| `cancelled` |
| `priority` | enum | yes | `critical` \| `high` \| `medium` \| `low` \| `trivial` |
| `epic` | string | no | Epic identifier this ticket belongs to |
| `sprint` | string | no | Sprint name this ticket is assigned to |
| `assignee` | string | no | Who is working on this |
| `reporter` | string | no | Who created this ticket |
| `story_points` | integer | no | Effort estimate |
| `labels` | list | no | Tags for categorization |
| `components` | list | no | System components affected |
| `blocked_by` | list | no | List of ticket keys that block this |
| `blocks` | list | no | List of ticket keys this blocks |
| `related_to` | list | no | List of related ticket keys |
| `due_date` | date | no | Deadline (YYYY-MM-DD) |
| `time_box` | string | no | For spikes: "{N}h" (e.g., "4h", "8h") |
| `created` | date | yes | Creation date (YYYY-MM-DD) |
| `updated` | date | yes | Last update date (YYYY-MM-DD) |
| `resolved` | date | no | Resolution date (YYYY-MM-DD) |
| `resolution` | enum | no | `fixed` \| `wont_fix` \| `duplicate` \| `cannot_reproduce` |

### Body Sections (in order)

1. `## Description` — Full description of the work
2. `## Acceptance Criteria` — What must be true for this to be done (optional)
3. `## Technical Notes` — Implementation details, gotchas, architecture notes
4. `## Comments` — Timestamped comments (format: `### {DATE} - {author}`)

---

## Appendix B: Documentation Frontmatter Schema

All valid fields for documentation files in `.harness/docs/`:

| Field | Type | Required | Values / Description |
|-------|------|----------|---------------------|
| `title` | string | yes | Page title |
| `type` | enum | yes | `adr` \| `technical-doc` \| `runbook` \| `onboarding` \| `meeting-notes` \| `postmortem` \| `rfc` \| `how-to` \| `reference` \| `changelog` |
| `space` | string | yes | Organizational space (e.g., "project", "team", "infrastructure") |
| `status` | enum | yes | `draft` \| `published` \| `archived` \| `deprecated` |
| `author` | string | yes | Who wrote this |
| `labels` | list | no | Tags for categorization |
| `related_pages` | list | no | Slugs of related documentation pages |
| `related_tickets` | list | no | Ticket keys related to this doc |
| `created` | date | yes | Creation date (YYYY-MM-DD) |
| `updated` | date | yes | Last update date (YYYY-MM-DD) |
| `published` | date | no | Publication date (YYYY-MM-DD) |
| `superseded_by` | string | no | Slug of the page that replaces this one |

### Body Structure

The body structure varies by `type`. See Appendix C for the template of each type.

---

## Appendix C: Templates

### C.1: Ticket Templates

#### story.md
```markdown
---
key: {PREFIX}-{NUMBER}
title: 
type: story
status: backlog
priority: medium
epic: 
sprint: 
assignee: 
reporter: 
story_points: 
labels: []
blocked_by: []
blocks: []
created: {TODAY}
updated: {TODAY}
---

## Description


## Acceptance Criteria
- [ ] 

## Technical Notes


## Comments
```

#### bug.md
```markdown
---
key: {PREFIX}-{NUMBER}
title: 
type: bug
status: backlog
priority: high
epic: 
sprint: 
assignee: 
reporter: 
labels: [bug]
blocked_by: []
blocks: []
created: {TODAY}
updated: {TODAY}
---

## Description


## Steps to Reproduce
1. 

## Expected Behavior


## Actual Behavior


## Environment
- OS: 
- Version: 
- Browser/Runtime: 

## Technical Notes


## Comments
```

#### task.md
```markdown
---
key: {PREFIX}-{NUMBER}
title: 
type: task
status: backlog
priority: medium
epic: 
sprint: 
assignee: 
reporter: 
labels: []
blocked_by: []
blocks: []
created: {TODAY}
updated: {TODAY}
---

## Description


## Definition of Done
- [ ] 

## Technical Notes


## Comments
```

#### spike.md
```markdown
---
key: {PREFIX}-{NUMBER}
title: 
type: spike
status: backlog
priority: medium
epic: 
sprint: 
assignee: 
reporter: 
story_points: 
labels: [spike, research]
time_box: 
blocked_by: []
blocks: []
created: {TODAY}
updated: {TODAY}
---

## Research Question


## Context
Why this spike is needed and what decision it will inform.

## Scope
- What to investigate:
- What is out of scope:

## Findings


## Recommendation


## Technical Notes


## Comments
```

### C.2: Documentation Templates

#### adr.md
```markdown
---
title: 
type: adr
space: project
status: draft
author: 
labels: [architecture]
related_pages: []
related_tickets: []
created: {TODAY}
updated: {TODAY}
---

## Context
What is the issue that we're seeing that is motivating this decision or change?

## Decision
What is the change that we're proposing and/or doing?

## Alternatives Considered
### Alternative 1: {name}
- Pros:
- Cons:

### Alternative 2: {name}
- Pros:
- Cons:

## Consequences
What becomes easier or more difficult to do because of this change?

## Comments
```

#### runbook.md
```markdown
---
title: 
type: runbook
space: infrastructure
status: draft
author: 
labels: [operations]
related_pages: []
related_tickets: []
created: {TODAY}
updated: {TODAY}
---

## Overview
What this runbook covers and when to use it.

## Prerequisites
- [ ] Access to {system}
- [ ] Permissions: {required permissions}

## Procedure
### Step 1: {title}
```
{command or action}
```

### Step 2: {title}
```
{command or action}
```

## Verification
How to confirm the procedure was successful.

## Rollback
How to undo this procedure if something goes wrong.

## Escalation
Who to contact if this runbook doesn't resolve the issue.

## Comments
```

#### technical-doc.md
```markdown
---
title: 
type: technical-doc
space: project
status: draft
author: 
labels: []
related_pages: []
related_tickets: []
created: {TODAY}
updated: {TODAY}
---

## Overview
What this document covers.

## Architecture


## API / Interface


## Data Flow


## Dependencies


## Configuration


## Known Limitations


## Comments
```

#### meeting-notes.md
```markdown
---
title: 
type: meeting-notes
space: team
status: published
author: 
labels: [meeting]
related_pages: []
related_tickets: []
created: {TODAY}
updated: {TODAY}
---

## Attendees
- 

## Agenda
1. 

## Discussion


## Decisions Made
- 

## Action Items
- [ ] {action} — {owner} — {due date}

## Next Meeting
- Date: 
- Topics: 

## Comments
```

---

## Appendix D: Operational Rules (AGENTS.md Content)

Generate the AGENTS.md file with these sections, adapted to the project:

### Session Lifecycle

```markdown
## Session Lifecycle

### Starting a Session
1. Pull latest changes: `git pull`
2. Read `.harness/memory/progress.md` — understand current state
3. Read `.harness/memory/sessions.md` — understand recent history
4. Read `.harness/memory/decisions.md` — understand architectural context
5. Identify the next task to work on
6. Run existing tests to verify clean baseline

### During a Session
- Work on ONE feature or fix per session
- Make atomic, descriptive commits as you progress
- Update technical notes on tickets as you discover implementation details
- If blocked, document the blocker and move to the next task

### Ending a Session
1. Run the full test suite — do not end with failing tests
2. Commit all work with a descriptive message
3. Update `.harness/memory/progress.md` with current status
4. Add a session entry to `.harness/memory/sessions.md`
5. If any architectural decisions were made, add to `.harness/memory/decisions.md`
```

### Commit Discipline

```markdown
## Commit Discipline

- Use {detected commit convention or "conventional commits"} format
- Every commit must pass tests
- Each commit should be atomic and reviewable in isolation
- Include ticket key in commit message when working from a ticket: `feat(auth): implement JWT refresh [{PREFIX}-001]`
- Never commit secrets, credentials, or environment files
```

### Testing Protocol

```markdown
## Testing Protocol

- Run existing tests BEFORE starting work (verify clean baseline)
- If tests fail at session start: document and fix before proceeding
- Write/update tests for any changed behavior
- Run full test suite before final commit
- Never mark a ticket as done without passing tests
```

### Multi-Agent Coordination (include only for multi-agent mode)

```markdown
## Multi-Agent Coordination

### Orchestrator Responsibilities
- Analyze the task and decompose into parallel sub-tasks
- Assign clear boundaries to each sub-agent (file scope, module scope)
- Define output format expectations for each sub-agent
- Synthesize results and resolve conflicts
- Make final commit decisions

### Sub-Agent Rules
- Only modify files within your assigned scope
- Return structured results, not free-form text
- Report blockers immediately — do not work around scope boundaries
- Each sub-agent reads only the memory/context relevant to their task

### Handoff Protocol
- Orchestrator provides: objective, file boundaries, output format, relevant context
- Sub-agent returns: changes made, decisions taken, blockers encountered, test results
```

### Progress Tracking

```markdown
## Progress Tracking

### Updating progress.md
- Mark features as `in_progress` when you start working on them
- Mark as `blocked` with notes explaining why
- Mark as `in_review` when code is complete but needs review
- Mark as `complete` only after tests pass and code is merged/committed
- Never skip a status (don't go from `not_started` to `complete`)

### Updating sessions.md
- Log every session, even short ones
- Include commit hashes for traceability
- "Next steps" should be specific enough for the next agent to start immediately
```

### Error Recovery

```markdown
## Error Recovery

- **If blocked**: Document in progress.md with details, move to next task
- **If context window fills**: Commit current state, update all memory files, end session cleanly
- **If tests break unexpectedly**: `git stash`, investigate on clean state, then decide whether to fix or revert
- **If conflicting with another agent's work**: Stop, document the conflict, escalate to orchestrator (or user in single-agent mode)
- **If stuck in a loop**: After 3 failed attempts at the same approach, document what was tried, mark as blocked, move on
```

### Memory Update Rules

```markdown
## Memory Update Rules

| File | When to Update |
|------|---------------|
| `sessions.md` | At the end of every session |
| `decisions.md` | When any architectural or design choice is made |
| `progress.md` | When any ticket status changes |

- Memory files are append-only (except for status updates in progress.md)
- Use precise dates (YYYY-MM-DD format)
- Keep entries concise — future agents need to scan quickly
```

---

## Appendix E: Agent Definition Template

Each agent file in `.harness/agents/` follows this structure:

```markdown
# Agent: {Name}

## Role
{One paragraph describing this agent's purpose and expertise}

## When to Use
{Conditions that trigger this agent's involvement}
- {Trigger condition 1}
- {Trigger condition 2}

## Capabilities
- {What this agent can do}
- {Tools and skills available}

## Constraints
- {What this agent must NOT do}
- {Scope boundaries}
- {File/directory limitations}

## Input
What this agent expects to receive:
- {Input 1: description}
- {Input 2: description}

## Output
What this agent produces:
- {Output 1: description}
- {Output 2: description}

## Handoff Protocol
### Receiving Work
{How this agent picks up a task}

### Completing Work
{How this agent signals completion and what it delivers}

## Quality Checklist
Before considering work complete:
- [ ] {Quality check 1}
- [ ] {Quality check 2}
- [ ] {Quality check 3}
```

### Example: Developer Agent

```markdown
# Agent: Developer

## Role
Primary coding agent responsible for implementing features, fixing bugs, and maintaining code quality. Works incrementally — one feature or fix per session.

## When to Use
- Implementing a new feature from a ticket
- Fixing a reported bug
- Refactoring code for clarity or performance
- Adding or updating tests

## Capabilities
- Read and modify source code
- Run tests and build commands
- Create and update tickets
- Update memory files
- Use all skills in .harness/skills/

## Constraints
- Work on ONE ticket per session
- Do not modify files outside the scope of the current ticket
- Do not make architectural decisions — escalate to architect agent
- Do not merge to main — leave for review

## Input
- Current ticket (from .harness/tickets/)
- Project context (from .harness/CLAUDE.md)
- Recent history (from .harness/memory/)

## Output
- Code changes committed to a feature branch
- Updated ticket status and technical notes
- Updated session log in memory/sessions.md
- Test results (all passing)

## Handoff Protocol
### Receiving Work
1. Read the assigned ticket
2. Read progress.md for context on related work
3. Check if there are blocking tickets

### Completing Work
1. All tests pass
2. Ticket updated to `in_review`
3. Session logged in memory/sessions.md
4. Progress.md updated
5. Clear commit message with ticket reference

## Quality Checklist
- [ ] Tests pass
- [ ] No linting errors
- [ ] Ticket description matches implementation
- [ ] Technical notes updated with any discoveries
- [ ] Session logged
```

---

## Summary

This manual enables any AI agent to:
1. **Analyze** a software project's stack, complexity, and architecture
2. **Generate** a complete `.harness/` directory with all supporting files
3. **Configure** each file with project-specific content
4. **Verify** the harness is correct and functional

The harness then serves as the persistent infrastructure that makes long-running, multi-session AI development possible — solving the fundamental problem of agents with no memory between context windows.
