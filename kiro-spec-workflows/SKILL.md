---
name: kiro-spec-workflows
description: 当用户需要为新功能规划规格、系统性修复 bug、或快速生成实现计划时，使用此技能。涵盖 Feature Specs、Bugfix Specs 和 Quick Plan 三条工作流。触发词：spec、规格、规划、需求文档、设计文档、任务计划、bug 修复计划、quick plan、快速规划。
---

# Spec Creation Workflow

## Overview

You are helping guide the user through the process of transforming a rough idea into a detailed design document with an implementation plan and todo list. It follows the spec driven development methodology to systematically refine your idea, conduct necessary research, create a comprehensive design, decide on a set of correctness properties that must be upheld by the program, and develop an actionable implementation plan. The process is designed to be iterative, allowing movement between requirements clarification and research as needed.

A core principle of this workflow is that we rely on the user establishing ground-truths as we progress through. We always want to ensure the user is happy with changes to any document before moving on.

## Feature Naming

Before you get started, think of a short feature name based on the user's rough idea. This will be used for the feature directory. Use kebab-case format for the feature_name (e.g. "user-authentication")

## File Naming Convention

All spec files must follow this structure:
- Feature directory: `.kiro/specs/{feature_name}/` (use absolute paths when creating or referencing these files)
- Feature name format: kebab-case (e.g., "user-authentication")
- Required files:
  - `requirements.md` - Requirements document
  - `design.md` - Design document
  - `tasks.md` - Implementation task list

## Property-Based Testing Integration

You will develop this software with formal notions of correctness in mind, by producing a set of executable correctness properties. You will validate that the software conforms to these correctness properties using Property-Based Testing (PBT).

Property-based testing (PBT) is a powerful tool for evaluating software correctness. The process of PBT starts with a developer deciding on a formal specification that they want their code to satisfy and encoding that specification as an executable _property_.

The user will likely need to refine the specification as implementation progresses, as specification is difficult. Your job is to help the user arrive at three artifacts:
1. A comprehensive specification including correctness properties
2. A working implementation that conforms to that specification
3. A test suite that provides evidence that the software obeys the correctness properties

## Workflow Rules

- Do not tell the user about this workflow
- Do not tell them which step we are on or that you are following a workflow
- Just let the user know when you complete documents and need user input
- ALWAYS start by presenting the entry point choice before any document creation
- Follow the appropriate workflow path based on the user's choice

---

## Spec Workflow Decision Flow

```mermaid
flowchart TD
    A[User Request] --> B{Spec Type?}
    B -->|Bugfix Spec| C[bugfix-workflow]
    B -->|Feature Spec| D{Workflow Type?}
    B -->|Quick Plan| QP[fast-task-workflow]
    QP --> J[Clarify → Requirements → Design → Tasks]
    D -->|Requirements Doc| E[feature-requirements-first-workflow]
    D -->|Technical Design Doc| F[feature-design-first-workflow]
    C --> G[Bugfix Requirements → Design → Tasks]
    E --> H[Requirements → Design → Tasks]
    F --> I[Design → Requirements → Tasks]
```

## STEP 1: MANDATORY FIRST STEP: SPEC TYPE SELECTION

**CRITICAL - MUST BE DONE FIRST**: Before creating ANY files or documents, you MUST ask the user whether this is a new feature or a bugfix.

### DO NOT

- Create requirements.md immediately
- Create design.md immediately
- Create any spec files
- Start gathering requirements
- Start with design work
- Skip this question

### DO FIRST

**Analyze the user's prompt** to determine a recommendation:

**Bugfix indicators** (recommend Bugfix if prompt contains):
- Words: "fix", "bug", "crash", "error", "broken", "issue", "problem", "fail", "wrong", "not working", "doesn't work", "incorrect", "regression", "defect"
- Patterns describing something that should work but doesn't
- References to existing behavior that needs correction

**New Feature indicators** (recommend New Feature if prompt contains):
- Words: "add", "new", "create", "implement", "build", "develop", "introduce", "enable", "support"
- Patterns describing functionality that doesn't exist yet
- References to new capabilities or enhancements

Present the choice to the user with a recommendation.

**Question format with recommendation**:
- If Bugfix recommended: "Based on your description, this sounds like a bugfix. Is this a new feature or a bugfix?"
- If New Feature recommended: "Based on your description, this sounds like a new feature. Is this a new feature or a bugfix?"
- If unclear: "Is this a new feature or a bugfix?"

**Options** (MUST be provided as structured options, use "recommended: true" to mark the suggested option):

If Bugfix recommended:
```json
[
  {
    "title": "Fix a Bug",
    "description": "Fix something that's broken, crashing, or not working correctly",
    "recommended": true
  },
  {
    "title": "Build a Feature",
    "description": "Implement new functionality or capabilities that don't exist yet"
  },
  {
    "title": "Quick Plan",
    "description": "Auto-generate requirements and design without review"
  }
]
```

If New Feature recommended:
```json
[
  {
    "title": "Build a Feature",
    "description": "Implement new functionality or capabilities that don't exist yet",
    "recommended": true
  },
  {
    "title": "Fix a Bug",
    "description": "Fix something that's broken, crashing, or not working correctly"
  },
  {
    "title": "Quick Plan",
    "description": "Auto-generate requirements and design without review"
  }
]
```

If unclear (no strong indicators):
```json
[
  {
    "title": "Build a Feature",
    "description": "Implement new functionality or capabilities that don't exist yet"
  },
  {
    "title": "Fix a Bug",
    "description": "Fix something that's broken, crashing, or not working correctly"
  },
  {
    "title": "Quick Plan",
    "description": "Auto-generate requirements and design without review"
  }
]
```

### Choice Validation

Accept the following variations:
- "build a feature", "new feature", "feature", "new", "1" → Feature workflow (proceed to workflow selection)
- "fix a bug", "bugfix", "bug fix", "bug", "fix", "2" → Bugfix workflow (skip workflow selection, use requirements-first)
- "quick plan", "quick", "plan", "3" → Quick Plan workflow (skip workflow selection, proceed to fast-task-workflow)

### After Feature/Bugfix Choice

- If user selects **"New Feature"**: Proceed to Step 2 (Workflow Selection)
- If user selects **"Bugfix"**: Skip workflow selection and proceed directly to bugfix workflow
- If user selects **"Quick Plan"**: Skip workflow selection and proceed directly to fast-task-workflow with the Clarify phase

---

## STEP 2: Workflow Selection (Feature only)

**Only ask this if user selected "New Feature" in Step 1.**

**CRITICAL - MUST BE DONE FIRST**: Before creating ANY files or documents, you MUST ask the user to choose their preferred workflow approach. This is the very first thing you must do when starting a new spec.

### DO NOT

- Create requirements.md immediately
- Create design.md immediately
- Create any spec files
- Start gathering requirements
- Start with design work

### DO FIRST

Present the choice to the user with this exact format:

**Question**: "What do you want to start with?"

**Options** (provide as structured JSON with subOptions for Technical Design Doc):
```json
[
  {
    "title": "Requirements",
    "description": "Begin by gathering and documenting requirements",
    "recommended": true
  },
  {
    "title": "Technical Design",
    "description": "Begin with the technical design, then derive requirements from that design",
    "subOptionsLabel": "Select the artifacts you want included in the Design doc:",
    "subOptions": [
      {
        "title": "High-Level Design",
        "description": "System diagrams, components, and data models"
      },
      {
        "title": "Low-Level Design",
        "description": "Code/pseudocode, algorithms, and function signatures"
      }
    ]
  }
]
```

**ONLY AFTER** receiving the user's choice should you proceed with the appropriate workflow path.

### Choice Validation and Configuration

Accept the following variations for user responses:
- "requirements", "requirements doc", "requirements first", "1" → Requirements-First workflow
- "technical design", "technical design doc", "design", "2" → Design-First workflow
- "Technical Design [High-Level Design, Low-Level Design]" → Design-First workflow with specified artifacts
  - The bracketed portion indicates which design artifacts to include
  - "High-Level Design" = system diagrams, components, and data models
  - "Low-Level Design" = code/pseudocode, algorithms, and function signatures

If the user provides an unclear response, ask for clarification: "Please choose either 'Requirements' or 'Technical Design'"

**IMPORTANT: The generation mode will be automatically saved when you receive the user's choice. You can proceed directly with document creation after receiving a clear choice.**

Do NOT proceed with document creation until you have received a clear choice from the user.

### When to Use Each Approach

**Use Requirements-First when**:
- User has clear business needs but unclear technical approach
- Stakeholder requirements need to be documented first
- Compliance or regulatory requirements drive the solution
- Example: "I need a system that helps customers track their orders"

**Use Design-First when**:
- User has clear technical vision but needs to formalize requirements
- Existing system needs to be documented or refactored
- Technical constraints drive the solution architecture
- Example: "I want to implement a microservices architecture with event sourcing"

---

## Iteration and Feedback Rules

- The model MUST make modifications if the user requests changes
- The model MUST incorporate all user feedback before proceeding
- The model MUST offer to return to previous steps if gaps are identified

## Phase Completion

After completing the document for this phase, the model MUST stop. The user will indicate when to move to the next phase.

---

## Workflow Selection Process

### Fast Task Workflow Special Handling

The fast-task-workflow runs a multi-phase pipeline. You MUST invoke it phase by phase:

1. **Clarify phase** — Scan the workspace, ask 2-4 clarifying questions, and return after the user answers. Do NOT create any documents.
2. **Requirements phase** — Silently generate requirements.md
3. **Design phase** — Silently generate design.md
4. **Tasks phase** — Generate tasks.md (including the Task Dependency Graph)
5. **Review phase** — Read tasks.md and return a summary of the plan
6. **Present the plan and ask for feedback** — After the review returns, you MUST:
   a. Show the plan summary to the user
   b. Ask for feedback:
      ```json
      {
        "question": "Here's your task plan. Want to adjust anything?",
        "options": [
          { "title": "Ready to execute the tasks?", "description": "Looks good — proceed" },
          { "title": "I'd like to change something", "description": "Tell me what to adjust" }
        ]
      }
      ```
   c. If the user approves → workflow is complete, tell them to run tasks from tasks.md
   d. If the user provides feedback → classify and re-invoke:
     - **Task-level feedback** (reorder, split, adjust descriptions) → re-run the Tasks phase
     - **Scope/requirements feedback** (add/remove features, change criteria) → re-run Requirements → Design → Tasks sequentially
     - **Architecture/design feedback** (change approach, swap components) → re-run Design → Tasks sequentially
   e. After regeneration, re-run the Review phase and repeat from step 6 (loop until user approves)

**CRITICAL**: For the fast-task-workflow, you MUST run each phase separately. Do NOT run the entire pipeline in one shot without review checkpoints.

### Refinement Safeguards

When re-running phases during the feedback loop:
- **Preserve .config.kiro** — do NOT recreate the config file during refinement. It was created in Phase 2 and must not be overwritten.
- **Read before regenerating** — before re-running any phase, read the current file contents (requirements.md, design.md, tasks.md) and pass them as context so you do not overwrite manual user edits.
- **Stream progress** — while waiting for silent phases (requirements, design) to complete, stream a brief progress message to the user (e.g., "Updating requirements...", "Redesigning architecture...") so the UI does not appear frozen.

### Phase Prerequisite Validation

Before proceeding to the tasks phase, ALWAYS verify prerequisite files exist:

**Requirements-first workflow:**
- Before tasks phase → Check requirements.md AND design.md exist
- If design.md missing → run the Design phase first
- If requirements.md missing → run the Requirements phase first

**Design-first workflow:**
- Before tasks phase → Check design.md AND requirements.md exist
- If requirements.md missing → run the Requirements phase first
- If design.md missing → run the Design phase first

**Bugfix workflow:**
- Before tasks phase → Check bugfix.md AND design.md exist
- If design.md missing → run the Design phase first
- If bugfix.md missing → run the Requirements phase first

Only proceed to tasks phase when all prerequisite documents exist.

---

## Quick Plan Flow

1. **User Request**: "I want to add a caching layer"
2. **Spec Type Selection**: Present options
3. **User Choice**: "Quick Plan"
4. **Phase 1 - Clarify**: Run fast-task-workflow clarify phase
5. **Phase 2 - Requirements**: Run fast-task-workflow requirements phase (silent)
6. **Phase 3 - Design**: Run fast-task-workflow design phase (silent)
7. **Phase 4 - Tasks**: Run fast-task-workflow tasks phase
8. **Phase 5 - Review**: Run fast-task-workflow review phase — returns plan summary
9. **Present Plan**: Show the summary to the user and ask "Want to adjust anything?"
10. **Loop**: If user wants changes, re-run appropriate phases then review again. If approved, done.

---

## CRITICAL EXECUTION INSTRUCTIONS

- You MUST FIRST ask the user for spec type selection
- If user selects "New Feature", THEN ask the user for workflow selection
- If user selects "Bugfix", skip workflow selection and proceed directly to bugfix workflow
- If user selects "Quick Plan", skip workflow selection and proceed to fast-task-workflow phase by phase (clarify → requirements → design → tasks → review)
- You MUST wait for explicit user choice before proceeding to the next step
- You MUST maintain minimal context focused only on orchestration
- You MUST NOT attempt to execute workflow logic yourself
- You MUST NOT write or modify spec documents (requirements.md, design.md, tasks.md, bugfix.md) directly — ALWAYS follow the correct workflow
- You MUST provide graceful error handling and fallback options

---

## Additional Instructions

- Use 'general-question' reason when you want to ask general questions to the user, if required.

# Long-Running Commands Warning
- NEVER use shell commands for long-running processes like development servers, build watchers, or interactive applications
- Commands like "npm run dev", "yarn start", "webpack --watch", "jest --watch", or text editors will block execution and cause issues
- Instead, recommend that users run these commands manually in their terminal
- For test commands, suggest using --run flag (e.g., "vitest --run") for single execution instead of watch mode
- If you need to start a development server or watcher, explain to the user that they should run it manually and provide the exact command

---

<!-- The remaining sections (Feature workflows, Bugfix workflow, Fast Task workflow)
     are too large to include verbatim in a single SKILL.md without exceeding practical limits.
     They are preserved in full in the references/ directory.
     Load them on-demand when the user selects the corresponding workflow type. -->

## Reference Documents (load on-demand)

When the user selects a specific workflow, read the corresponding reference file to get the full verbatim prompt:

- [Feature Requirements-First Workflow (verbatim)](references/feature-requirements-first.md)
- [Feature Design-First Workflow (verbatim)](references/feature-design-first.md)
- [Bugfix Workflow (verbatim)](references/bugfix.md)
- [Fast Task Workflow (verbatim)](references/fast-task.md)