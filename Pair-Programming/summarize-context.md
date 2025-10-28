# Session Context Summary Prompt

Please analyze our entire conversation and create a condensed summary that captures the crucial context for the next session. Focus on information that would be valuable for continuing this work efficiently.

## Instructions

Extract and organize the most important information from this session into the following sections. Be concise but complete - include enough detail that someone could pick up where we left off without re-reading the entire conversation.

**Prioritize:**

- Key decisions and their rationale
- Technical discoveries or insights
- Problems solved and their solutions
- Action items and next steps
- Important context that shouldn't be lost

**Exclude:**

- Exploratory discussions that didn't lead anywhere
- Superseded approaches or ideas
- Routine implementation details already documented elsewhere

---

## Output Format

<session_metadata>
**Date:** [Session date]
**Duration:** [Approximate length (tokens)]
**Primary Focus:** [Main topic or objective of session]
**Status:** [Where things stand - completed, in progress, blocked, etc.]
</session_metadata>

---

<key_decisions>
List major decisions made during this session:

- **Decision:** [What was decided]
  - **Rationale:** [Why this approach was chosen]
  - **Alternatives Considered:** [Other options discussed]
  - **Impact:** [What this affects going forward]
</key_decisions>

---

<technical_discoveries>
Document important technical findings or insights:

- **Discovery:** [What was learned]
  - **Context:** [Why this matters]
  - **Implications:** [How this affects the project]
  - **Documentation:** [Where this should be recorded permanently]
</technical_discoveries>

---

<problems_solved>
Record issues encountered and their resolutions:

- **Problem:** [Description of issue]
  - **Root Cause:** [What was actually wrong]
  - **Solution:** [How it was fixed]
  - **Prevention:** [How to avoid this in the future]
</problems_solved>

---

<work_completed>
Summarize what was accomplished:

- [Specific deliverable or milestone completed]
- [Feature implemented or story finished]
- [Documentation updated or created]
- [Tests written or verification completed]
</work_completed>

---

<current_state>
Describe the current state of the project/feature:

**What's Working:**

- [Functional components or features]

**What's In Progress:**

- [Partially completed work]
- [Current implementation status]

**What's Blocked:**

- [Issues preventing progress]
- [Dependencies waiting on resolution]
</current_state>

---

<next_steps>
Outline immediate next actions:

1. **[Action Item]**
   - Priority: [High/Medium/Low]
   - Estimated Effort: [Time estimate]
   - Dependencies: [What's needed first]
   - Context: [Any important notes for completing this]

2. **[Action Item]**
   - [Same structure as above]
</next_steps>

---

<open_questions>
List unresolved questions or decisions needed:

- **Question:** [What needs to be decided or clarified]
  - **Context:** [Why this matters]
  - **Options:** [Potential approaches if known]
  - **Decision Needed By:** [Any time constraints]
</open_questions>

---

<context_for_next_session>
Critical information that the next session will need:

**Files Modified:**

- [List of files changed with brief description of changes]

**Patterns Established:**

- [New conventions or approaches adopted]

**Testing Notes:**

- [Testing status, coverage, known issues]

**Performance Considerations:**

- [Any performance insights or concerns]

**Integration Points:**

- [How this work connects to other parts of the system]
</context_for_next_session>

---

<lessons_learned>
Capture insights for future work:

**What Worked Well:**

- [Effective approaches or techniques]

**What Didn't Work:**

- [Approaches to avoid]

**Process Improvements:**

- [Ways to work more effectively next time]

**Documentation Gaps:**

- [Areas needing better documentation]
</lessons_learned>

---

<warnings_and_gotchas>
Important cautions for future work:

- **⚠️ Warning:** [Critical issue to be aware of]
  - **Why:** [Explanation]
  - **How to Avoid:** [Preventive measures]
</warnings_and_gotchas>

---

## Formatting Guidelines

- Be concise but complete - each bullet should be self-contained
- Use specific technical details where relevant (file names, function names, etc.)
- Focus on "why" not just "what" - rationale is crucial context
- Highlight anything that would be non-obvious to someone reading later
- If a section doesn't apply to this session, write "None" rather than omitting it
- Use clear, precise language - avoid vague descriptions

Generate the summary now.
