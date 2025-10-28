# AI Collaboration Patterns

> **Last Updated:** [DATE]  
> **Purpose:** Proven prompting strategies, reasoning patterns, and collaboration approaches for effective AI-assisted software development.

## Core Principles

### Instruction Following Best Practices

Modern AI models follow instructions more literally than previous generations. This means:

- **Be explicit, not implicit** - State exactly what you want, don't rely on inference
- **Use structured formats** - XML, markdown sections, and clear delimiters work well
- **Specify what NOT to do** - Include explicit boundaries and constraints
- **One clear instruction per section** - Avoid mixing multiple directives

### Effective Communication Patterns

**Response Rules Format:**
```
# Instructions
- [High-level guidance]
- [Specific constraint]
- [Output format requirement]

# Context
[Relevant background information]

# Task
[Specific request with examples]
```

## Reasoning Strategies

### Chain of Thought (CoT) Templates

**Basic CoT Prompt:**
```
Think through this step by step:
1. [First, analyze the problem]
2. [Then, consider the constraints] 
3. [Finally, propose a solution]

Explain your reasoning for each step.
```

**Advanced Reasoning Strategy:**
```
# Reasoning Strategy
1. Query Analysis: Break down what you're being asked until you're confident about the requirements
2. Context Analysis: Review relevant documentation and codebase patterns
3. Solution Design: Plan the approach considering best practices and constraints
4. Implementation: Execute with clear explanations of choices made

Follow this strategy and show your work for each step.
```

**Self-Consistency for Critical Decisions:**
For important architectural or design decisions, use multiple reasoning paths:
```
Consider this decision from three different perspectives:
1. Performance/scalability angle
2. Maintainability/developer experience angle  
3. User experience angle

After analyzing all three, recommend the best approach and explain why.
```

### Tree of Thoughts for Complex Problems

When facing complex technical challenges:
```
Explore multiple solution approaches:

Branch A: [Traditional/safe approach]
- Pros: [list]
- Cons: [list]
- Implementation effort: [estimate]

Branch B: [Innovative/modern approach]  
- Pros: [list]
- Cons: [list]
- Implementation effort: [estimate]

Branch C: [Hybrid approach]
- Pros: [list] 
- Cons: [list]
- Implementation effort: [estimate]

Compare branches and recommend the best path forward with justification.
```

## Development Workflow Patterns

### Story-Driven Development Template

Based on successful patterns from production systems:

```
# Story Analysis
**User Story:** [As a X, I want Y, so that Z]
**Acceptance Criteria:** [Specific, testable requirements]

# Implementation Planning
1. **Brief Plan (for approval):**
   - [High-level approach in 2-3 bullets]
   - [Key technical decisions]
   - [Estimated complexity/time]

2. **Detailed Plan (after approval):**
   - [Step-by-step implementation]
   - [File changes needed]
   - [Testing approach]
   - [Integration points]

# Implementation Notes
- Follow established patterns in the codebase
- Write clean, well-structured code
- Include proper error handling
- Update documentation as needed

Show me the brief plan first, then wait for approval before proceeding.
```

### Code Review and Quality Patterns

**Code Review Prompt:**
```
Review this code for:
1. **Functionality:** Does it meet the requirements?
2. **Quality:** Is it clean, readable, and maintainable?
3. **Performance:** Are there any obvious bottlenecks?
4. **Security:** Any potential vulnerabilities?
5. **Testing:** Is it properly testable?
6. **Integration:** Does it fit well with existing patterns?

Provide specific feedback with suggestions for improvement.
```

**Refactoring Safety Pattern:**
```
Before refactoring this code:
1. Analyze current functionality and dependencies
2. Identify potential breaking changes
3. Propose incremental steps to minimize risk
4. Suggest testing strategy to verify behavior is preserved

Only proceed with refactoring after confirming the approach is safe.
```

## Agentic Workflow Patterns

### System Prompt Reminders

For multi-turn, autonomous problem-solving sessions:

```
# Agent Instructions
You are working autonomously on this task. Key reminders:

**Persistence:** Keep going until the problem is completely resolved. Only stop when you're certain the solution is complete and tested.

**Tool Usage:** Use all available tools (code execution, file reading, testing) rather than guessing or hallucinating solutions.

**Verification:** After implementing changes, verify they work as expected and don't break existing functionality.

**Documentation:** Update relevant documentation and leave clear notes about changes made.

Current task: [specific objective]
```

### ReAct (Reason + Act) Pattern

For problems requiring research and action:

```
# ReAct Loop
Follow this pattern for complex problem-solving:

**Thought:** [Reason about what needs to be done next]
**Action:** [Take a specific action - search docs, run code, read files]  
**Observation:** [Note what you learned from the action]

Repeat this cycle until the problem is solved. Always explain your reasoning before taking action.
```

## Context Management Patterns

### Long Context Organization

For sessions with extensive documentation:

```
# Context Structure
Place instructions both at the beginning and end of long context for best performance.

**Above context:** High-level instructions and objectives
**Below context:** Specific formatting requirements and constraints

Use XML tags for clear section separation:
<requirements>
[Specific requirements]
</requirements>

<context>
[Relevant documentation/code]
</context>
```

### Context Relevance Filtering

When working with large codebases:

```
# Context Analysis
Before implementing changes:

1. **Relevance Assessment:** Which files/modules are directly relevant?
2. **Dependency Mapping:** What other components might be affected?
3. **Impact Analysis:** What are the potential side effects?

Focus only on high-relevance context to avoid information overload.
```

## Task-Specific Patterns

### Architecture Decision Template

```
# Architecture Decision Record

**Status:** [Proposed/Accepted/Deprecated]
**Context:** [What situation led to this decision?]
**Decision:** [What we decided to do]
**Consequences:** [Trade-offs and implications]

**Alternatives Considered:**
- Option A: [Brief description] - [Why not chosen]
- Option B: [Brief description] - [Why not chosen]

**Success Criteria:** [How will we know this was the right choice?]
```

### Bug Investigation Pattern

```
# Bug Investigation Process

1. **Reproduction:** Can you consistently reproduce the issue?
2. **Isolation:** What is the minimal case that demonstrates the problem?
3. **Root Cause Analysis:** What is actually causing this behavior?
4. **Impact Assessment:** What else might be affected by this bug?
5. **Solution Strategy:** What are the safest ways to fix this?

Work through each step systematically before proposing a fix.
```

### Feature Planning Template

```
# Feature Development Plan

**Objective:** [Clear, specific goal]
**Success Metrics:** [How will we measure success?]

**Phase 1: MVP**
- [Essential functionality only]
- [Clear acceptance criteria]
- [Testing strategy]

**Phase 2: Enhancement** 
- [Nice-to-have features]
- [Performance optimizations]
- [UX improvements]

**Risks & Mitigation:**
- Risk: [potential issue] → Mitigation: [how to handle it]

Break down into small, testable increments that build on each other.
```

## Anti-Patterns & Failure Modes

### Common Mistakes to Avoid

**Over-Engineering:**
```
❌ Don't: Build complex, flexible solutions for simple problems
✅ Do: Start with the simplest solution that works, then iterate
```

**Context Confusion:**
```
❌ Don't: Mix multiple unrelated requests in one prompt  
✅ Do: Focus on one clear objective per interaction
```

**Assumption Making:**
```
❌ Don't: Assume requirements or make undocumented decisions
✅ Do: Ask clarifying questions and document choices
```

**Testing Neglect:**
```
❌ Don't: Skip testing because "it's just a small change"
✅ Do: Always verify changes work and don't break existing functionality
```

### Recovery Strategies

When things go wrong:

1. **Stop and Assess:** Don't compound problems by rushing
2. **Isolate the Issue:** What specifically isn't working?
3. **Check Recent Changes:** What was different about this session?
4. **Return to Known Good State:** Can we revert and try a different approach?
5. **Learn and Document:** What can we do differently next time?

## Collaboration Preferences

### Session Management

**Start of Session Checklist:**

- [ ] Review project context document
- [ ] Check current status/milestone  
- [ ] Understand immediate objectives
- [ ] Confirm any constraints or preferences

**End of Session Checklist:**

- [ ] Document key decisions made
- [ ] Update progress tracking
- [ ] Note any lessons learned
- [ ] Prepare context for next session

### Communication Style

**Preferred Interaction Patterns:**

- Ask clarifying questions early rather than making assumptions
- Provide rationale for technical decisions
- Break complex changes into reviewable chunks
- Use consistent terminology and naming conventions

**Progress Updates:**

- Show incremental progress rather than working in isolation
- Explain trade-offs when multiple solutions exist
- Highlight potential risks or concerns proactively

---

## Template Library

### Quick Reference Prompts

**Planning Phase:**
```
Draft a step-by-step implementation plan for [objective]. Break it into small, safe increments that build on each other. Include testing strategy and integration points.
```

**Implementation Phase:**  
```
Implement [specific feature] following established patterns in the codebase. Include proper error handling and maintain code quality standards.
```

**Review Phase:**
```
Review this implementation for correctness, performance, and maintainability. Suggest specific improvements if needed.
```

**Debugging Phase:**
```
Investigate this issue systematically: reproduce, isolate, analyze root cause, assess impact, then propose the safest fix.
```

---

*This document should be updated based on what works well in practice. Track successful patterns and refine approaches that don't work as expected.*