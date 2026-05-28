# Fast Task Workflow (verbatim)

---

# Fast Task Workflow

Five-phase pipeline: Clarify → Requirements (silent) → Design (silent) → Tasks → Review

All three spec artifacts (requirements.md, design.md, tasks.md) are produced, but the user only interacts during clarify and review phases.

## Prerequisites

- Feature name determined (kebab-case format)

---

## Phase 1: Clarify

### Objective

Scan the workspace to understand the existing codebase, detect the dominant programming language, and present 2-4 targeted questions that gather preferences, clarify ambiguities, resolve implementation choices, and offer directional decisions.

### Step 1: Workspace Analysis

Use `list_directory` and `read_code` to scan the workspace and understand:
- The project structure, frameworks, and conventions in use
- Existing patterns, naming conventions, and architectural decisions
- The tech stack and dependencies

### Step 2: Language Detection

Detect the dominant programming language by counting file extensions across workspace source files:
- Count occurrences of each recognized source file extension (.ts, .js, .py, .java, .go, .rs, .rb, .php, .cs, .cpp, .c, etc.)
- Select the language with the highest file count as the dominant language
- If the workspace contains no recognized source files (empty or non-code workspace), default to "Structured Pseudocode"
- Store the detected language for use in later phases
- Do NOT prompt the user to select a language at any point

### Step 3: Formulate Questions

Analyze the user's request against the workspace context to formulate 2-4 questions. Each question MUST fall into one of these four categories. You MUST cover at least two different categories across your questions:

**Category A — User Preferences & Requirements**
Gather the user's expectations about behavior, scope, or constraints that aren't stated in the request. These are things only the user can decide.
- Examples: "Should this support both logged-in and anonymous users?", "Do you want this to persist across sessions or be ephemeral?"

**Category B — Ambiguity Clarification (INCOSE-informed)**
Identify language in the user's request that violates INCOSE quality rules and ask the user to resolve it. Look for:
- **Vague terms** that lack measurable criteria: "fast", "simple", "flexible", "good", "user-friendly", "adequate", "reasonable", "quickly"
- **Escape clauses** that leave behavior undefined: "where possible", "if feasible", "as appropriate", "when needed"
- **Pronouns or unclear references** where the subject or object is ambiguous: "it should handle them", "they get processed"
- **Negative or absolute statements** that need concrete bounds: "never fail", "always available", "100% uptime"
- **Missing conditions** where the trigger or context is unspecified: "the data is validated" (when? by whom? against what?)
Frame the question in plain language — do NOT mention INCOSE, EARS, or any methodology names.
- Examples: "You mentioned it should be 'fast' — are we talking under 200ms, under 1 second, or just non-blocking?", "When you say 'handle errors gracefully', what should the user see — a toast notification, an inline message, or a retry prompt?"

**Category C — Implementation Choices**
Present a concrete technical decision where the codebase supports multiple valid approaches and the choice materially affects the task plan.
- Examples: "The project uses both REST and GraphQL — should this new endpoint follow REST or GraphQL?", "Should we add this as a new service or extend the existing UserService?"

**Category D — Directional Decisions**
Offer a fork in the road where the user's choice changes the overall shape of the feature. These are higher-level than implementation choices — they affect what gets built, not just how.
- Examples: "Should this be a full CRUD interface or read-only for now?", "Do you want to build the MVP first and iterate, or go for the complete feature set?"

### Question Selection Rules

1. Analyze the user's request and workspace context to identify candidate questions across all four categories
2. Rank candidates by impact on the task plan — prioritize questions where the answer would change which tasks get generated
3. Select 2-4 questions (inclusive), covering at least 2 different categories
4. If the user's request is already precise and the workspace context resolves most ambiguities, lean toward Category C and D questions (implementation choices and directional decisions)
5. If the user's request is vague, lean toward Category A and B questions (preferences and ambiguity clarification)

**CRITICAL**: Do NOT expose any engineering methodology terminology (such as INCOSE, EARS, pattern names, quality rule names, or formal analysis terms) to the user. Frame all questions in plain, conversational language.

### Step 4: Present Clarifying Questions

Present each clarifying question as a SEPARATE question so the user can answer one at a time. This gives a clean, paginated experience instead of a wall of options.

For the FIRST question only, include a "Skip questions" option so the user can opt out early. If the user selects "Skip questions", do NOT ask any remaining questions — proceed directly to the silent phases.

Here is the EXACT structure for each question:

```json
{
  "question": "**[Your clarifying question in plain language]**",
  "options": [
    {
      "title": "[Answer choice A]",
      "description": "[Brief explanation of what this choice means]"
    },
    {
      "title": "[Answer choice B]",
      "description": "[Brief explanation of what this choice means]"
    },
    {
      "title": "Skip questions",
      "description": "Skip remaining questions and go straight to generating the task list"
    }
  ],
  "reason": "general-question"
}
```

Rules:
- Frame questions in plain language that any developer would understand
- Each question should address a distinct concern — no overlapping questions
- Present between 2 and 4 questions (inclusive) — never 0, 1, or more than 4
- Each question contains exactly ONE question with 2-3 answer options
- The first question MUST include a "Skip questions" option as the LAST item
- Subsequent questions MAY include a "Skip remaining questions" option as the LAST item
- If the user selects any skip option, stop asking questions immediately
- The user can either click an answer option or type a free-text response
- Wait for the user's answer to each question before presenting the next one

### Step 5: Complete Clarify Phase

After all questions are answered (or the user skips):
- If the user selected "Skip questions" at any point, note that only the original description and workspace context should be used for subsequent phases
- Otherwise, compile the user's original description with all their clarifying answers into a summary
- **STOP HERE** — this phase is complete. Do NOT generate requirements, design, or tasks documents. Do NOT proceed to any other phase.
- Return a summary of the user's answers and the detected programming language

---

## Phase 2: Requirements Generation (Silent)

**Do NOT invoke `user_input` during this phase.**

Stream a brief message like "Generating requirements..." then:

1. Combine the user's description, their clarifying answers, and workspace context
2. Generate `requirements.md` following EARS patterns and INCOSE quality rules
3. Write `.config.kiro` and `requirements.md` to `.kiro/specs/{feature_name}/`
4. **STOP HERE** — this phase is complete. Do NOT generate design.md or tasks.md. Do NOT proceed to any other phase.

---

## Phase 3: Design Generation (Silent)

**Do NOT invoke `user_input` during this phase.**

Stream a brief message like "Designing the architecture..." then:

1. Read `requirements.md` from Phase 2
2. Generate `design.md` covering architecture, components, interfaces, data models, and error handling
3. Include a "Correctness Properties" section with properties using universal quantification ("For all"/"For any") and `**Validates: Requirements X.Y**` annotations
4. Use the programming language detected in the Clarify phase for code examples (or Structured Pseudocode for empty workspaces)
5. Write `design.md` to `.kiro/specs/{feature_name}/`
6. **STOP HERE** — this phase is complete. Do NOT generate tasks.md. Do NOT proceed to any other phase.

---

## Phase 4: Task Creation (Output)

### Objective

Generate an actionable task plan from the requirements and design, and present it to the user as the visible output of the workflow.

### CRITICAL: Skip Language Selection

The programming language was already detected during the Clarify phase. Do NOT use the `userInput` tool to ask the user which language to use. Use the language that was detected (or Structured Pseudocode if the workspace was empty).

(The full task creation rules — task format, dependency graph, forbidden tasks, etc. — are the same as in the Feature Requirements-First workflow. See references/feature-requirements-first.md for the complete verbatim text.)

### Additional Constraints for Fast Task Workflow

- The model MUST skip the "Programming Language Selection" step in Task Creation — the language is already known from the Clarify phase
- The model MUST present the generated task plan to the user as the visible output
- After presenting the task plan, the model MUST proceed to the Review Checkpoint (Phase 5)

---

## Phase 5: Review Checkpoint

Your job is to read the task plan and return a summary. Do NOT call `user_input`. The orchestrator will handle user interaction.

### Instructions

1. Read `.kiro/specs/{feature_name}/tasks.md` using `read_file`
2. Write a concise summary listing each top-level task with a one-line description
3. Return the summary via `subagent_response` — the orchestrator will present it to the user

Do NOT call `user_input`. Do NOT ask the user anything. Just read, summarize, and return.

---

## Workflow Completion

When the user approves the plan in Phase 5, tell them the spec is complete and they can run tasks from tasks.md.