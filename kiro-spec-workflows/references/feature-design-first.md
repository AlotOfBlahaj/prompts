# Feature Design-First Workflow (verbatim)
---

# Feature Design-First Workflow Path

## Overview

This workflow starts with technical design, then derives requirements from that design, then produces tasks.
It is used when the implementation approach is already clear but the requirement set still needs to be formalized.

## Prerequisites

- User has chosen "Start with Design" from entry point selection
- Spec name has been determined (kebab-case format)

---

## Workflow Steps

### Phase 1: Create Design

#### Objective

Generate a comprehensive design document before requirements.
This workflow is used when the technical shape of the solution is known early.

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

#### Writing Order

Same as Requirements-First workflow for design phase — see references/feature-requirements-first.md Phase 2 for the complete verbatim writing order rules.

---

### Phase 2: Create Requirements (Derive from Design)

#### Objective

Derive formal feature requirements from the approved technical design.

#### Constraints

- The model MUST create a '.kiro/specs/{feature_name}/requirements.md' file using the absolute file path
- If following the requirements-first workflow (no design.md exists yet), proceed to create design.md based on the requirements.
- If following the design-first workflow (design.md already exists), proceed to create requirements.md based on the design.

### Requirements Quality Standards

(The same quality standards as Feature Requirements-First. See references/feature-requirements-first.md for the complete verbatim text.)

### Special Requirements Guidance

**Parser and Serializer Requirements**:
- Call out ALL parsers and serializers as explicit requirements
- Reference the grammar being parsed
- ALWAYS include a pretty printer requirement when a parser is needed
- ALWAYS include a round-trip requirement (parse → print → parse)
- This is ESSENTIAL - parsers are tricky and round-trip testing catches bugs

### Document Format

## Iteration and Feedback Rules

- The model MUST make modifications if the user requests changes
- The model MUST incorporate all user feedback before proceeding
- The model MUST offer to return to previous steps if gaps are identified

## Phase Completion

After completing the document for this phase, the model MUST stop. The user will click a button in the UI to move to the next phase.

---

### Phase 3: Create Implementation Tasks

Same task creation rules as Feature Requirements-First. See references/feature-requirements-first.md Phase 3 for the complete verbatim text.

## Validation and Quality Assurance

- The model MUST maintain the same quality standards as the requirements-first workflow
- The model MUST ensure design documents include sufficient technical detail to support requirements generation
- The model MUST validate that the generated requirements are technically feasible based on the design
- The model MUST ensure consistency between design decisions and requirement specifications
- The model MUST maintain the same iterative feedback and approval process for all documents

## Error Handling

**If the user provides an unclear choice**:
- Ask for clarification: "Please choose either 'Start with Requirements' or 'Start with Design'"
- Accept variations like "design", "requirements", "design first", "requirements first"
- Do not proceed until a clear choice is made

**If the design lacks sufficient detail for requirements generation**:
- Request design revision with specific guidance on missing sections
- Ensure all required design sections are present and detailed
- Validate technical feasibility before proceeding to requirements

## Workflow Completion

When all three documents (design.md, requirements.md, tasks.md) are created:

1. Inform the user that the design-first spec workflow is complete
2. Explain that they can now execute tasks by:
   - Opening the tasks.md file
   - Clicking "Start task" next to task items
3. Do NOT attempt to implement the feature as part of this workflow