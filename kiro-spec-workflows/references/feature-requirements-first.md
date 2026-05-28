# Feature Requirements-First Workflow (verbatim)

---

# Feature Requirements-First Workflow Path

## Overview

This workflow starts with requirements gathering, then moves to design, then tasks.
This is the traditional approach where business needs drive technical design for new features.

## Prerequisites

- User has chosen "Start with Requirements" from entry point selection
- Spec name has been determined (kebab-case format)

---

## Workflow Steps

### Phase 1: Create Requirements

#### Objective

Generate an initial set of requirements using EARS patterns and INCOSE quality rules.
Iterate with the user until all requirements are both structurally and semantically compliant.

#### Process

1. **Initial Generation**: Create requirements.md based on user's feature idea
2. **User Review**: Present requirements for review
3. **Iteration**: Refine based on feedback

#### Constraints

- The model MUST create a '.kiro/specs/{feature_name}/requirements.md' file using the absolute file path
- The model MUST generate an initial version WITHOUT asking additional clarifying questions
- If the user responds with "Skip to Implementation Plan", the model MUST proceed to create the design document without stopping for design review, then continue to task creation
- The model MUST correct non-compliant requirements and explain corrections
- The model MUST suggest improvements for incomplete requirements

### Requirements Quality Standards

- EARS pattern compliance
- INCOSE quality rules
- No vague qualifiers
- Explicit ranges and bounds
- Testable acceptance criteria

### Special Requirements Guidance

**Parser and Serializer Requirements**:
- Call out ALL parsers and serializers as explicit requirements
- Reference the grammar being parsed
- ALWAYS include a pretty printer requirement when a parser is needed
- ALWAYS include a round-trip requirement (parse → print → parse)
- This is ESSENTIAL - parsers are tricky and round-trip testing catches bugs

**Example Parser Requirements**:
```markdown
### Requirement N: Parse Configuration Files

**User Story:** As a developer, I want to parse configuration files, so that I can load application settings.

#### Acceptance Criteria

1. WHEN a valid configuration file is provided, THE Parser SHALL parse it into a Configuration object
2. WHEN an invalid configuration file is provided, THE Parser SHALL return a descriptive error
3. THE Pretty_Printer SHALL format Configuration objects back into valid configuration files
4. FOR ALL valid Configuration objects, parsing then printing then parsing SHALL produce an equivalent object (round-trip property)
```

### Document Format

## Iteration and Feedback Rules

- The model MUST make modifications if the user requests changes
- The model MUST incorporate all user feedback before proceeding
- The model MUST offer to return to previous steps if gaps are identified

## Phase Completion

After completing the document for this phase, the model MUST stop. The user will click a button in the UI to move to the next phase.

### Template

```markdown
# Requirements Document

## Introduction

[Summary]

## Requirements

### Requirement N: [Title]

**User Story:** As a [role], I want [feature], so that [benefit]

#### Acceptance Criteria

1. WHEN [event], THE [system] SHALL [response]
2. IF [condition], THEN THE [system] SHALL [response]
```

---

### Phase 2: Create Design

#### Objective

Develop a comprehensive design document based on approved feature requirements.
Conduct necessary research during the design process.

#### Process

1. **Research**: Identify and research areas needed for design
2. **Design Writing**: Write design sections (stop before Correctness Properties)
3. **Prework**: Use prework tool to analyze acceptance criteria
4. **Properties**: Write correctness properties based on prework
5. **User Review**: Present design for review
6. **Iteration**: Refine based on feedback

#### Constraints

- The model MUST create a '.kiro/specs/{feature_name}/design.md' file using the absolute file path
- The model MUST identify areas where research is needed
- The model MUST conduct research and build up context in the conversation
- The model SHOULD NOT create separate research files
- The model MUST summarize key findings that inform the design
- The model SHOULD cite sources and include relevant links
- The model MUST incorporate research findings into the design

#### Design Document Structure

The model MUST include these sections:
- Overview
- Architecture
- Components and Interfaces
- Data Models
- Correctness Properties
- Error Handling
- Testing Strategy

The model SHOULD include diagrams or visual representations (use Mermaid for diagrams)
The model MUST ensure the design addresses all feature requirements
The model SHOULD highlight design decisions and their rationales
The model MAY ask the user for input on specific technical decisions

#### Writing Order (CRITICAL)

The writing order depends on which workflow you are following:

**For Requirements-First Workflow**:
1. **Write sections from Overview through Data Models**
2. **Assess PBT applicability**: Determine if the feature is suitable for property-based testing (see Testing Strategy Requirements below)
3. **If PBT IS applicable**:
   - **STOP before writing Correctness Properties section**
   - **Use the 'prework' tool to analyze acceptance criteria**
   - **Continue writing the Correctness Properties section based on prework analysis**
4. **If PBT is NOT applicable** (e.g., IaC, UI rendering, simple CRUD):
   - **Skip the Correctness Properties section entirely**
   - **Do NOT run the prework tool**
5. **Complete remaining sections (Error Handling, Testing Strategy)**

---

### Phase 3: Create Implementation Tasks

#### Objective

Generate an implementation task list that follows the requirements-to-design flow and references the design specifications.

Convert the feature design into a series of prompts for a code-generation LLM that will implement each step with incremental progress. Make sure that each prompt builds on the previous prompts, and ends with wiring things together. There should be no hanging or orphaned code that isn't integrated into a previous step. Focus ONLY on tasks that involve writing, modifying, or testing code.

#### Constraints

- The model MUST create a '.kiro/specs/{feature_name}/tasks.md' file using the absolute file path
- The model MUST return to design if user indicates design changes needed
- The model MUST return to requirements if user indicates additional requirements needed

#### Task List Format

**Structure**:
- Maximum two levels of hierarchy
- Top-level items (epics) only when needed
- Sub-tasks numbered with decimal notation (1.1, 1.2, 2.1)
- Each item must be a checkbox
- Simple structure is preferred

**Task Item Requirements**:
- Clear objective involving writing, modifying, or testing code
- Additional information as sub-bullets under the task
- Specific references to requirements (granular sub-requirements, not just user stories)

#### Task Content Requirements

**Incremental Steps**:
- Each task builds on previous steps
- Discrete, manageable coding steps
- Each step validates core functionality early through code

**Requirements Coverage**:
- Each task references specific requirements
- All requirements covered by implementation tasks
- No excessive implementation details (already in design)
- Assume all context documents available during implementation

**Checkpoints**:
- Include checkpoint tasks at reasonable breaks
- Checkpoint format: "Ensure all tests pass, ask the user if questions arise."
- Multiple checkpoints are okay

**Property-Based Test Tasks**:
- Include tasks for turning correctness properties into property-based tests
- Each property MUST be its own separate sub-task
- Place property sub-tasks close to implementation (catch errors early)
- Annotate each property with its property number
- Annotate each property with the requirements clause number it checks
- Each task MUST explicitly reference a property from the design document

### Coding Tasks Only

The model MUST ONLY include tasks that can be performed by a coding agent.

**Allowed tasks**:
- Writing, modifying, or testing specific code components
- Creating or modifying files
- Implementing functions, classes, interfaces
- Writing automated tests
- Concrete tasks specifying what files/components to create/modify

**Explicitly FORBIDDEN tasks**:
- User acceptance testing or user feedback gathering
- Deployment to production or staging environments
- Performance metrics gathering or analysis
- Running the application to test end-to-end flows (use automated tests instead)
- User training or documentation creation
- Business process or organizational changes
- Marketing or communication activities
- Any task that cannot be completed through code

### Completion

The model MUST stop once the task document has been created.

### Workflow Completion

**This workflow is ONLY for creating design and planning artifacts.**

- The model MUST NOT attempt to implement the feature as part of this workflow
- The model MUST clearly communicate that this workflow is complete once artifacts are created
- The model MUST inform the user they can begin executing tasks by:
  - Opening the tasks.md file
  - Clicking "Start task" next to task items

### Example Format

```markdown
# Implementation Plan: [Feature Name]

## Overview

[Brief description of the implementation approach]

## Tasks

- [ ] 1. Set up project structure and core interfaces
  - Create directory structure
  - Define core interfaces and types
  - Set up testing framework
  - _Requirements: X.Y_

- [ ] 2. Implement core functionality
  - [ ] 2.1 Create core data model interfaces and types
    - Write TypeScript interfaces for all data models
    - Implement validation functions for data integrity
    - _Requirements: 2.1, 3.3, 1.2_

  - [ ]* 2.2 Write property test for core data model
    - **Property 2: Round trip consistency**
    - **Validates: Requirements 2.5**

  - [ ] 2.3 Implement User model with validation
    - Write User class with validation methods
    - _Requirements: 1.2_

  - [ ]* 2.4 Write unit tests for User model
    - Test validation edge cases
    - Test error conditions
    - _Requirements: 1.2_
```

### Task Dependency Graph

After the task list and notes section, the model MUST append a `## Task Dependency Graph` section containing a JSON code block that defines execution waves for parallel task scheduling.

**Rules for generating the dependency graph**:
1. Every incomplete leaf task (sub-tasks with decimal notation like 1.1, 2.1, 2.2) must appear in exactly one wave
2. Tasks that write to the same file MUST be placed in different waves to avoid conflicts
3. Setup and infrastructure tasks (project structure, interfaces, types) go in early waves (lower wave IDs)
4. Test tasks go in later waves, after the code they test has been implemented
5. The dependency graph MUST be valid JSON inside a fenced code block (```json ... ```)
6. Wave IDs MUST be contiguous integers starting from 0
7. Tasks within the same wave are independent and can run in parallel
8. Tasks in wave N can only execute after all tasks in waves 0..N-1 complete
9. Checkpoint tasks and top-level parent tasks (without decimal notation) are NOT included in the graph — only leaf sub-tasks
10. Optional test sub-tasks (marked with *) MUST still be included in the dependency graph

**Format**:
```markdown
## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1", "3.1"] },
    { "id": 1, "tasks": ["1.2", "2.1"] },
    { "id": 2, "tasks": ["2.2", "3.2"] },
    { "id": 3, "tasks": ["2.3"] }
  ]
}
```
```

## Navigation Between Steps

- User can request to return to requirements from design phase
- User can request to return to design from tasks phase
- User can request to return to requirements from tasks phase