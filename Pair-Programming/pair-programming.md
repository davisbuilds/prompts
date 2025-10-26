# Development Workflow

> **Purpose:** This document outlines our structured approach to AI-assisted development. It ensures consistent process, proper testing, knowledge capture, and maintainable progress as we work through project goals.

## Workflow Philosophy

### Core Principles
- **Story-Driven Development:** Break work into small, atomic stories (15-30 minutes each)
- **Incremental Progress:** Each story builds on previous work and leaves the system in a working state
- **Quality First:** Every change is tested and verified before moving forward
- **Knowledge Capture:** Document decisions, lessons learned, and important discoveries
- **Human-AI Collaboration:** AI handles implementation, humans handle strategic decisions and verification

### Story Structure
Stories follow this format:
```
**Story ID:** [Number/ID]
**Title:** [Brief descriptive title]
**User Story:** As a [user type], I want [goal] so that [benefit]
**Acceptance Criteria:** 
- [ ] [Specific, testable requirement]
- [ ] [Specific, testable requirement]
**Testing Strategy:** [How to verify this works]
**Status:** [Pending/In Progress/Testing/Completed]
```

## Development Process

### Phase 1: Story Selection & Planning

**AI Actions:**
- Review project plan/backlog to identify next pending story
- Analyze story's acceptance criteria and testing requirements
- Consider dependencies and current system state
- Create brief implementation approach (2-3 bullet points)
- **Confirm plan with human before proceeding**

**Communication Pattern:**
```
Ready for [Story ID]: [Title]

My approach:
- [High-level strategy point 1]
- [Key technical decision or method]
- [Integration/testing approach]

Does this approach sound good? Any concerns or alternative suggestions?
```

**Human Actions:**
- Review proposed approach for alignment with project goals
- Consider impact on architecture and other components
- Approve, request modifications, or suggest alternatives
- Provide any additional context or constraints

---

### Phase 2: Implementation Preparation

**AI Actions (after human approval):**
- Create comprehensive implementation plan
- Review relevant documentation and codebase patterns
- Set up task tracking (todo list, progress markers)
- Update story status to "In Progress"
- Identify files that need to be read or modified

**Preparation Checklist:**
- [ ] Detailed step-by-step plan created
- [ ] Current codebase patterns understood
- [ ] Dependencies and integration points identified
- [ ] Testing approach clarified
- [ ] Story marked as "In Progress"

---

### Phase 3: Implementation

**AI Actions:**
- Implement according to acceptance criteria
- Follow established code patterns and standards
- Include proper error handling and edge cases
- Write clean, well-structured, maintainable code
- Add appropriate comments and documentation
- Integrate changes properly with existing system

**Implementation Standards:**
- Follow project coding conventions and style guide
- Use appropriate types and interfaces (TypeScript, etc.)
- Handle errors gracefully with user-friendly messages
- Write self-documenting code with clear variable/function names
- Keep functions small and focused on single responsibility
- Maintain consistency with existing patterns

**Progress Communication:**
- Work systematically through implementation
- Surface any unexpected issues or discoveries immediately
- Ask clarifying questions if requirements are ambiguous
- Document any architectural decisions made during implementation

---

### Phase 4: Testing & Verification

**AI Actions:**
- Run all relevant automated tests (unit, integration, etc.)
- Execute build commands to verify compilation
- Perform any linting, type checking, or code quality checks
- Test edge cases and error conditions
- Verify acceptance criteria are met

**Testing Checklist:**
- [ ] All specified tests pass
- [ ] Build/compilation successful
- [ ] No new errors or warnings
- [ ] Code quality checks pass
- [ ] Acceptance criteria verified
- [ ] Error handling tested

**Build Commands (customize for your project):**
```bash
# Example commands - adapt to your tech stack
npm run build          # Build verification
npm run test           # Run test suite
npm run lint           # Code quality
npm run type-check     # Type verification
```

---

### Phase 5: Human Verification Request

**AI Actions:**
- Summarize implementation and changes made
- Provide specific testing instructions for human
- List expected behavior and results
- Request human verification in running application

**Required Communication Format:**
```
## [Story ID] Implementation Complete!

**Changes Made:**
- [Bulleted list of key changes]
- [Files modified or added]
- [New functionality implemented]

**Please Verify:**
1. [Start application with: command]
2. [Navigate to: location/page]
3. [Perform action: specific steps]
4. [Expected result: what should happen]

**Success Criteria:**
- [What you should see if working correctly]
- [Any specific UI changes or behaviors]
- [Performance or functional expectations]

Ready for your verification!
```

**Human Testing Areas:**
- Functional correctness (does it work as specified?)
- User experience (is it intuitive and polished?)
- Integration (does it work well with existing features?)
- Performance (is it responsive and efficient?)
- Edge cases (how does it handle unusual inputs/scenarios?)

---

### Phase 6: Human Testing & Feedback

**Human Actions:**
- Start application in development/testing mode
- Follow provided testing steps systematically
- Test both happy path and edge cases
- Verify acceptance criteria are fully met
- Provide clear feedback on results

**Feedback Patterns:**
- **Success:** "Perfect! That works exactly as expected."
- **Issues Found:** "I found an issue: [specific description of what's wrong]"
- **Partial Success:** "This works but [specific feedback for improvement]"

**If Issues Found:**
- Human describes specific problems encountered
- AI investigates and implements fixes
- Return to Phase 4 (Testing & Verification) 
- Repeat until verification succeeds

---

### Phase 7: Story Completion

**AI Actions (after successful human verification):**
- Mark story as "Completed" in project tracking
- Update progress indicators and project plan
- Document lessons learned and technical decisions
- Update relevant documentation files
- Prepare context for next story

**Completion Checklist:**
- [ ] Story marked as "Completed"
- [ ] Project plan/progress updated
- [ ] Lessons learned documented
- [ ] Technical decisions recorded
- [ ] Documentation updated
- [ ] Context prepared for next iteration

---

## Quality Standards

### Code Quality Requirements
- **Readability:** Clear, self-documenting code with meaningful names
- **Maintainability:** Modular design that's easy to modify and extend
- **Reliability:** Proper error handling and edge case coverage
- **Performance:** Efficient algorithms and resource usage
- **Consistency:** Follows established patterns and conventions
- **Testing:** Adequate test coverage for new functionality

### Documentation Standards
- **Code Comments:** Explain complex logic and business rules
- **API Documentation:** Clear interfaces and usage examples
- **Architecture Notes:** Document significant design decisions
- **User Documentation:** Update guides and help text as needed

## Knowledge Capture

### During Each Story
**Technical Decisions:**
- Document rationale for architectural choices
- Record trade-offs considered and why options were selected
- Note any deviations from standard patterns and why

**Issues & Solutions:**
- Capture problems encountered and how they were resolved
- Document debugging approaches that worked
- Note any workarounds or temporary solutions

**Lessons Learned:**
- What worked well in this implementation?
- What would you do differently next time?
- Any new patterns or techniques discovered?
- Performance considerations or optimizations found?

### Project-Level Documentation Updates
Keep these files current throughout development:
- **Architecture decisions** and their rationale
- **API documentation** and integration guides  
- **Setup and deployment** instructions
- **Testing strategies** and coverage notes
- **Performance benchmarks** and optimization notes

## Communication Guidelines

### Effective Patterns
**Starting Work:**
- Always confirm approach before implementing
- Ask clarifying questions early rather than making assumptions
- Break complex stories into smaller pieces if needed

**During Implementation:**
- Surface issues immediately, don't work around them silently
- Document significant decisions as you make them
- Request guidance when multiple valid approaches exist

**Completing Work:**
- Provide thorough testing instructions
- Explain what changed and why
- Set clear expectations for verification

### Context Management
**Session Boundaries:**
- Review project context and current objectives at session start
- Update progress tracking and documentation at session end
- Ensure context is preserved for next session

**Information Organization:**
- Keep conversations focused on current story
- Move detailed technical discussions to documentation
- Maintain clean separation between planning and implementation

## Troubleshooting Common Issues

### When Stories Are Too Large
**Symptoms:** Implementation takes longer than 30 minutes, complex testing requirements, multiple acceptance criteria that could be independent

**Solution:** Break into smaller, more atomic stories that build on each other

### When Context Gets Cluttered
**Symptoms:** Conversations become unfocused, contradictory information, difficulty tracking current objectives

**Solution:** Summarize key decisions, move details to documentation, start fresh context with current focus

### When Quality Issues Arise
**Symptoms:** Bugs found during testing, inconsistent with existing code, performance problems

**Solution:** Return to implementation phase, address issues systematically, update quality checklists

### When Communication Breaks Down
**Symptoms:** Unclear requirements, assumptions made without confirmation, misaligned expectations

**Solution:** Pause implementation, clarify requirements, confirm understanding before proceeding

---

## Customization Notes

**Adapt This Workflow:**
- Modify build commands for your tech stack
- Adjust story sizing for your project complexity
- Customize quality standards for your requirements
- Update documentation patterns for your tools

**Tool Integration:**
- Replace generic tracking with your project management tools
- Integrate with your testing and CI/CD pipeline
- Adapt communication patterns for your team preferences
- Customize verification procedures for your deployment process

---

*This workflow should be refined based on project experience. Update patterns that work well and modify approaches that create friction.*