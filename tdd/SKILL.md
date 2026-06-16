---
name: tdd
description: use before writing implementation code in projects
---

# Test-Driven Development (TDD) Skill

## Inspiration

This skill incorporates ideas and patterns inspired by:

- https://github.com/vibeeval/vibecosystem/blob/main/skills/tdd/SKILL.md
- https://github.com/obra/superpowers/blob/main/skills/test-driven-development/SKILL.md

This implementation extends those approaches with:

- Explicit stop conditions for autonomous agents
- Scope-discipline safeguards against over-engineering
- Environment and convention discovery procedures
- Validation phases beyond the standard RED-GREEN-REFACTOR loop
- Stronger enforcement of minimal implementation principles

---

## Purpose

Enforce disciplined Test-Driven Development across agent frameworks. This skill prevents implementation-first development by requiring all production code changes to be driven by failing tests that specify intended behavior.

The objective is to produce minimal, correct implementations guided by executable specifications while avoiding over-engineering and unintended scope expansion.

---

# The Iron Law of Agentic TDD

1. **No production code without a failing test first.**
2. **If production code is generated before a failing test exists:**

- Stop execution
- Remove the untested production code
- Restart the TDD cycle

3. **A failing test must fail for the intended behavioral reason.**

- Acceptable: missing functionality, incorrect output, unmet requirements
- Unacceptable: syntax errors, compilation failures, import issues, misconfigured environments, or unrelated test failures

4. **Implement only enough code to satisfy currently failing tests.**
5. **Do not introduce functionality that is not required by an existing failing test.**

---

# Execution Protocol

## Phase 0: Environment & Convention Discovery

Before modifying any files:

1. Identify the project's:

- Testing frameworks
- Test runners
- Assertion libraries
- Mocking libraries
- Coverage tooling
- Linting and type-checking tools

2. Discover project conventions by examining existing tests:

- Test organization patterns
- Naming conventions
- Fixture usage
- Mocking strategies
- Existing TDD practices

3. Locate dependency manifests and build configurations, such as:

- `package.json`
- `requirements.txt`
- `pyproject.toml`
- `Cargo.toml`
- `pom.xml`
- `go.mod`

4. Execute the relevant existing test suite to establish a baseline:

- Confirm the workspace is initially stable
- Document any pre-existing failures before proceeding
- Identify the commands or scripts for running the test suite (e.g. a `Makefile`, `npm test`, or `pytest`) to ensure a repeatable baseline

---

## Phase 1: RED — Define the Desired Behavior

### 1. Write the Smallest Failing Test

Create the minimal test that demonstrates the requested behavior change.

The test should:

- Express a single behavior or requirement
- Clearly communicate intent
- Structure the test using an Arrange–Act–Assert (Given–When–Then) pattern to isolate setup, action, and assertion
- Fail only because the behavior has not yet been implemented

Avoid:

- Large test suites upfront
- Multiple unrelated assertions
- Premature abstraction

### 2. Select the Appropriate Test Level

Prefer the narrowest test capable of specifying the requirement:

1. Unit tests for isolated business logic
2. Integration tests when component interactions define behavior
3. End-to-end tests only when validating complete workflows

Do not default to end-to-end testing when lower-level tests sufficiently specify the behavior.

### 3. Execute the Test

Run only the relevant test or test subset whenever possible.

### 4. Validate the Failure

Verify that the failure:

- Directly corresponds to missing or incorrect behavior
- Demonstrates the intended requirement gap

Do **not** proceed if failures originate from:

- Syntax errors
- Missing imports
- Broken test infrastructure
- Environmental issues
- Unrelated failing tests

Resolve environmental issues before continuing.

---

## Phase 2: GREEN — Minimal Implementation

### 1. Implement the Smallest Possible Change

Write only the code necessary to satisfy the currently failing test.

Guidelines:

- Prefer straightforward solutions
- Avoid premature optimization
- Avoid speculative abstractions
- Do not implement future requirements
- It’s acceptable to use simple placeholders or hardcoded values to satisfy the current failing test, then improve them during refactoring

### 2. Execute Relevant Tests

Run the affected tests.

If tests fail:

- Analyze the failure output carefully
- Make localized corrections
- Re-run the tests

Repeat until all targeted tests pass.

### 3. Expand Behavior Incrementally

Once the initial requirement passes:

- Introduce additional failing tests for:
  - Edge cases
  - Invalid inputs
  - Boundary conditions
  - Error handling scenarios

Apply additional RED → GREEN cycles for each newly specified behavior.

---

## Phase 3: REFACTOR — Improve Design Safely

Only refactor while all tests remain green.

Refactoring goals:

- Improve readability
- Eliminate duplication
- Clarify naming
- Simplify logic
- Reduce unnecessary complexity
- Improve maintainability

Guidelines:

- Preserve all externally observable behavior
- Run relevant tests frequently during refactoring
- Prioritize clarity over cleverness
- Optimize performance only when supported by explicit requirements or performance-focused tests
- Remember to refactor regularly; neglecting this step often leads to messy code

---

## Phase 4: VALIDATE — Confirm System Integrity

After refactoring:

1. Execute all affected test suites.

2. Run project quality checks when they exist:

- Linters
- Type checkers
- Static analysis tools

3. Verify that newly introduced behavior integrates correctly with existing functionality.

4. If coverage tooling exists:
  - Ensure modified code paths remain adequately tested
  - Prefer maintaining or improving existing coverage levels
  - Do not create superficial tests solely to satisfy coverage metrics

5. Verify zero regressions: if any previously-passing tests fail, revert to the last green state and re-evaluate the changes.

---

# Scope Discipline

Always maintain strict implementation boundaries.

## Required Practices

- Implement only behavior required by failing tests
- Introduce one behavior change at a time
- Prefer simple solutions first
- Allow design to emerge through successive TDD cycles

## Avoid

- Anticipating future requirements
- Building extensibility without demonstrated need
- Creating abstractions prematurely
- Adding helper utilities before they are justified by duplication
- Performing broad architectural changes unrelated to the requested behavior

Apply the **Rule of Three**:

- First occurrence: implement directly
- Second occurrence: tolerate duplication
- Third occurrence: consider refactoring into an abstraction

---

# Testing Guidelines

## Prefer Real Logic

Test actual business behavior whenever practical.

Mock only:

- External services
- Network boundaries
- Databases
- File systems
- Time sources
- Random number generators
- Third-party integrations

Avoid mocking:

- Core business rules
- Internal implementation details
- Logic under direct evaluation
- Keep each test focused and straightforward, testing one clear behavior at a time

Tests should validate outcomes rather than implementation mechanics.

---

# Troubleshooting Playbook

## The agent cannot determine how to test a highly coupled component.

**Resolution:**

- Introduce seams through dependency injection
- Define explicit interfaces or contracts
- Isolate external dependencies behind adapters

---

## Test setup consumes excessive context or complexity.

**Resolution:**

- Extract repetitive setup into fixtures or helper utilities
- Reuse builders and factories appropriately
- Keep individual tests focused on relevant behavior

---

## Multiple failing tests obscure the intended change.

**Resolution:**

- Address environmental or unrelated failures first
- Isolate the smallest reproducible failing scenario
- Resume normal TDD cycles once the target failure is clear

---

## Existing tests conflict with the requested behavior.

**Resolution:**

- Determine whether requirements have changed
- Update tests only when they no longer represent correct behavior
- Preserve existing specifications unless explicitly superseded

---

# Stop Conditions

Pause and request human guidance when:

- The expected behavior is ambiguous
- Multiple valid implementations imply different business rules
- Architectural decisions exceed the scope of the request
- Required infrastructure cannot be executed locally
- Existing tests exhibit nondeterministic behavior
- Requirements conflict with established specifications
- The agent cannot determine an appropriate testing strategy with reasonable confidence
- 

Do not guess business intent.

---

# Success Criteria

The TDD cycle is complete only when:

- A failing test was written before production code
- The failure represented the intended missing behavior
- Minimal implementation code was introduced
- All relevant tests pass
- Refactoring preserved behavior
- Validation checks succeeded
- No unnecessary functionality was added

**Every line of production code should be justifiable by a failing test that required its existence.**
