---
name: handoff
description: use to create handoff documents for agents and knowledge bases
---

# Handoff Skill

This skill creates structured handoff documents that allow another agent (or future session) to continue work efficiently without rediscovering context, repeating failed approaches, or revisiting settled decisions.

Handoffs also serve as a **knowledge base**, preserving important technical context, architectural reasoning, lessons learned, and project terminology for future retrieval.

## Inspiration

This skill incorporates ideas and patterns inspired by:

- https://github.com/softaworks/agent-toolkit/blob/main/skills/session-handoff/README.md
- https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md

This implementation extends those approaches by emphasizing:

- Preservation of decision rationale
- Separation of verified facts from assumptions
- Explicit documentation of blockers and open questions
- High signal-to-noise ratio
- Immediate resumability for future agents
- Long-term usefulness as a knowledge base
- Privacy and portability of documented information

---

# Execution Rules

1. **Determine Necessity**
  - Create a handoff when:
    - The user explicitly requests one
    - Work spans multiple sessions
    - The task remains incomplete
    - Important discoveries, trade-offs, or blockers were identified
  - Do not create handoffs for trivial, self-contained tasks unless requested.

2. **Target Directory**
  - Store handoff files in `~/handoffs/`
  - Create the directory if it does not exist

3. **File Naming**
  - Use the format:

    ```
    [yyyy-mm-dd]-[task-slug].md
    ```

  - If multiple handoffs for the same task are created on the same day, append a timestamp:

    ```
    [yyyy-mm-dd]-[hhmmss]-[task-slug].md
    ```

  - Examples:

    ```
    2026-06-16-auth-refactor.md
    2026-06-16-143022-auth-refactor.md
    2026-06-16-payment-webhook-debugging.md
    ```

4. **Prioritize Signal Over Volume**
  - Avoid copying large code blocks
  - Avoid reproducing entire conversations
  - Avoid duplicating information already captured in:
    - ADRs
    - Design documents
    - Issues
    - Specifications
    - Existing knowledge base entries
  - Reference existing artifacts instead
  - Focus on:
    - Architectural decisions
    - Verified findings
    - Failed approaches
    - Hidden constraints
    - Critical implementation details

5. **Distinguish Facts from Assumptions**
  - Clearly separate:
    - What has been verified
    - What is inferred or assumed
    - What still requires validation

6. **Capture Decision Rationale**
  - Record why important decisions were made
  - Include notable alternatives that were rejected
  - Prevent future agents from reopening already-resolved discussions unnecessarily

7. **Document Blockers Explicitly**
  - Record anything preventing progress:
    - Missing information
    - External dependencies
    - Access limitations
    - Technical uncertainties

8. **Protect Sensitive Information**
  - Never include:
    - API keys
    - Passwords
    - Access tokens
    - Secrets
    - Personally identifiable information (PII)
  - Redact sensitive values if they are necessary for context.

9. **Provide Immediate Next Actions**
  - Next steps must be concrete and actionable
  - Prefer:
    - Specific files
    - Commands
    - Tests to run
    - Small implementation tasks
  - The next agent should know exactly how to resume work

10. **Path Privacy & Portability**
  - Use `~` for locations within the user's home directory
  - Prefer repository-relative paths for project artifacts
  - Avoid exposing absolute system paths unless explicitly required

11. **Knowledge Base Optimization**
  - Write handoffs so they remain useful weeks or months later
  - Preserve important decisions and failed approaches
  - Include terminology and context that future retrieval may depend on
  - Favor concise explanations over exhaustive detail

12. **Clean Exit**
  - Save the handoff document
  - Respond only with:
    - The filename
    - A one-sentence confirmation that the handoff was created successfully

---

# Handoff Document Template

Generate the handoff markdown using the following structure:

````markdown
# [yyyy-mm-dd] [task slug]

## 1. Initial Prompt & Objective

> [Original request or concise summary of the primary objective]

---

## 2. Current Status

- **Completion Status:** Complete | Partial | Blocked
- **Outcome Summary:** [Brief description of what was achieved]

---

## 3. Scope & Boundaries

### In Scope

- [Areas that were intentionally worked on]

### Out of Scope

- [Areas intentionally left unchanged]

### Important Constraints

- [Technical, organizational, or environmental constraints]

---

## 4. Technical Context & Discoveries

### Verified Working

- [Functionality confirmed through tests, execution, or direct observation]
- [Verified implementation details]

### Key Decisions & Rationale

#### Decision: [Decision made]

- **Reason:** [Why this approach was selected]
- **Alternatives Considered:** [Other approaches evaluated]
- **Confidence:** High | Medium | Low

### Assumptions Requiring Validation

- [Unverified assumptions]
- [Expected behaviors not yet confirmed]

### Failed Approaches & Lessons Learned

- [Approaches that did not work]
- [Reasons they failed]
- [Insights gained]

### Quirks & Edge Cases

- [Unexpected behaviors]
- [Environmental constraints]
- [API inconsistencies]
- [Important implementation discoveries]

---

## 5. Artifacts & References

### Modified Files

- `relative/path/to/file`
- `another/relative/path`

### Important Locations

- `relative/path/to/file:123` — [Relevant implementation detail]
- `relative/path/to/other_file:45` — [Important context]

### Important Commands

```bash
command used during work
another useful command
```

### Related Resources

* Documentation
* ADRs
* Design notes
* Relevant tickets/issues
* Existing knowledge base entries
* External references

---

## 6. Known Issues & Blockers

### Known Issues

* [Existing defects or limitations]

### Current Blockers

* [Factors preventing further progress]

### Open Questions

* [Questions requiring clarification or future decisions]

---

## 7. Immediate Next Steps

* [ ] [First concrete action]
* [ ] [Second concrete action]
* [ ] [Third concrete action]
````

---

# Retrieving Handoffs

When instructed to:

- "resume from handoff"
- "continue from handoff"
- "load handoff"
- "pick up previous work"

follow this process:

1. Search `~/handoffs/` for relevant handoff documents using:
  - Explicit filenames provided by the user
  - Task-related keywords
  - Project names
  - Recently modified handoffs when context is ambiguous

2. Select the most relevant handoff based on:
  - Explicit filename references
  - Matching task names
  - Most recent timestamp when ambiguous

3. Read the entire document before taking action.

4. Treat **Immediate Next Steps** as the default execution plan.

5. Validate assumptions and documented status against the current project state.

6. Preserve previously documented decisions unless new evidence justifies revisiting them.

7. Continue work while maintaining awareness of:
  - Scope boundaries
  - Known issues
  - Existing blockers
  - Open questions

8. Upon completing substantial additional work, generate an updated handoff document.

---

# Handoff Quality Checklist

Before saving the handoff, verify:

- [ ] The original objective is clear
- [ ] Current completion status is accurately represented
- [ ] Scope boundaries are documented when relevant
- [ ] Verified facts are separated from assumptions
- [ ] Important decisions include rationale
- [ ] Failed approaches or pitfalls are documented
- [ ] Blockers and open questions are explicit
- [ ] Immediate next steps are concrete and actionable
- [ ] Paths use `~` or repository-relative notation where appropriate
- [ ] Sensitive information and secrets have been removed
- [ ] Existing artifacts are referenced instead of duplicated
- [ ] Unnecessary detail and large code dumps have been removed
- [ ] Another agent could continue work without rereading the full conversation
- [ ] The handoff remains useful as a future knowledge base entry
