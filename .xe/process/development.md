# Development Process

## Living Specification Principle

**Critical**: All specifications (spec.md, plan.md, tasks.md) must represent the final desired state, not a chronological log of changes.

- **spec.md and plan.md** contain only timeless requirements applicable to any future implementation
- **tasks.md** describes implementation steps as if building from scratch
- Exclude one-time setup/migration steps from permanent specs
- When requirements change, update specs to reflect the new desired state (not a diff)

**Example**: If one change adds a button and another later changes it to a link, tasks.md should have one task for "Add link component" with no mention of a button.

## Development Workflow

Use the following development process when assigned to a task or asked to generate code. Each step within each phase should be committed to the branch. **NEVER** commit directly to the `main` branch.

### Phase 0. Setup

- Determine a short, kebab-cased rollout ID
  - Use the feature ID when implementing a single, new feature
  - Use a logical short description when enhancing a feature or fixing a bug
- Create a branch for the rollout
  - **Format**: `{username}/{rollout-id}` (e.g., `flanakin/expand-score-display`)
    - `{username}` should be the GitHub account that owns the feature
    - `{rollout-id}` is a short kebab-cased identifier for the change being implemented
  - **If automated by Catalyst AI**: Prefix with `xe/` → `xe/{username}/{rollout-id}` (e.g., `xe/flanakin/expand-score-display`)
- Create placeholder `.xe/rollouts/rollout-{rollout-id}.md` as the central, async orchestrator for this change and add an entry to `.xe/rollouts/README.md` index for discovery

**Note**: Rollout plans can cover multiple features or a subset of a single feature depending on the scope of change.

### Phase 1. Analysis

- Step 1: Gain product and architectural context
  - Based on the provided inputs, determine if the requested change is for one or more new or existing features
    - Review `.xe/product.md` and `.xe/architecture.md` to understand the high-level system goals and architecture
    - Review the list of existing features by checking the folders in the `.xe/specs` folder
  - Create a `.xe/specs/{feature-id}/research.md` file and document any important product or architectural context with a date of the findings

- Step 2: Perform market research (if appropriate)
  - If this is for a new feature or major feature enhancement (not a bug), review the existing competitive analysis located at `.xe/competitive-analysis.md`
    - If any of the following are true, create or update the competitive analysis:
      - A product competitive analysis has not been documented
      - The competitive analysis was more than 3 months old
      - The change being implemented is a major pivot that shifts the product into a new category not covered by the current competitive analysis
    - To generate a competitive analysis, think deeply and perform web research to generate a detailed competitive analysis report
    - Save the competitive analysis report in `.xe/competitive-analysis.md`
  - Update the `.xe/specs/{feature-id}/research.md` file with any new information captured from market research (if any)

- Step 3: Understand feature requirements and source code
  - Review related source code based on the folder structure defined in `.xe/architecture.md` or feature `plan.md` files
  - For bugs:
    - Review the source code to identify the component that is causing the unexpected results
    - Identify the feature that the source code component maps to
    - Review existing `spec.md` and `plan.md` for the corresponding feature in the `.xe/specs/{feature-id}/` folder to understand intended behavior
  - If this change impacts one or more existing features:
    - Review the `spec.md` for each existing feature to understand the feature requirements
    - Review the `plan.md` for each existing feature to understand how the feature was implemented in source code
    - Determine if a subset of the feature or source code being updated could be refactored and extracted for reuse or improve maintainability
    - Determine if there is any existing technical debt that should be cleaned up as part of this change by considering:
      - **Implementation Scope**: How much additional work to include cleanup?
      - **Risk vs. Benefit**: Does cleanup justify increased complexity?
      - **Rollout Strategy**: Should debt cleanup be pre-implementation, during, or post-implementation?
  - If this change impacts multiple features:
    - Determine which feature is the primary feature
    - Determine what dependencies each feature has and create a dependency tree
    - Perform steps for each phase against the primary feature first, then each secondary feature
  - Update the `.xe/specs/{feature-id}/research.md` file with any important feature or source code context, including:
    - Current code paths and component ownership
    - Existing specs, plans, and tasks for related features
    - Any internal or external dependencies or integrations
    - Any migration or technical debt implications

### Phase 2. Specification

**For enhancements:**

- For new features:
  - Create `.xe/specs/{feature-id}/spec.md` based on the `.xe/templates/specs/spec.md` template
  - Review and complete each section based on the provided `> [INSTRUCTIONS]`
- For enhancements to existing features:
  - Review instructions from the `.xe/templates/specs/spec.md` template for context
  - Update each section in `.xe/specs/{feature-id}/spec.md` as appropriate for the enhancement being applied
  - Consider extracting compartmentalized requirements into a dedicated feature to increase reuse and maintainability when needed
- **DO NOT** add implementation details (languages, frameworks, APIs)
  - If there is an upstream platform or integration requirement, define that rather than the current technology constraints (since the technology constraints may change over time)
- Focus on user value and business needs - the WHAT and WHY, not HOW
- Write for non-technical stakeholders and AI code generation
- Follow specification writing standards (see below)
- Remove instructions when the section is complete
- Verify requirements are testable and unambiguous
- Verify all success criteria are measurable
- Verify all dependencies and assumptions identified
- Review and approve `spec.md` before proceeding to planning
  - Call out any key updates to market or competitive analyses that uncovered shortcomings in the current feature spec

**For bugs:**

- Update specs if requirements were unclear or need clarification
- Review and approve spec changes before proceeding to planning

### Phase 3. Planning

**Purpose**: Create implementation plan that serves as design approval checkpoint before code generation.

**CRITICAL**: All planning documents (plan.md, tasks.md) describe implementation from scratch, as if no code exists. Use "Create", "Implement", "Build" - NOT "Modify", "Add to", "Update".

- Step 0: Research & Design Decisions
  - _Prerequisites: spec.md complete_
  - Think deeply about how to best implement this feature within the technical constraints defined by `.xe/architecture.md`:
    - What specific problem does this feature solve? (reference spec.md scenarios)
    - What are the edge cases and failure modes?
    - What existing patterns/features can be reused?
  - Consider design alternatives:
    - What alternative approaches were considered?
    - Why was this approach chosen? (tradeoffs, constraints, alignment with architecture)
    - What research was needed?
  - Document pivotal technical decisions in `research.md`:
    - Decision: [what was chosen]
    - Rationale: [why chosen]
    - Alternatives considered: [what else evaluated]
    - Data flow: [inputs, transformations, outputs]
  - Validate architecture fit:
    - How does this align with `.xe/architecture.md` patterns?
    - What new patterns does this introduce?
    - How does this feature compose with existing features?

- Step 1: Data Model
  - _Prerequisites: research.md complete_
  - Define data entities this feature introduces or modifies
  - **ALWAYS document entities inline in `plan.md` `## Data Model` section**
  - Only create separate `data-model.md` if entities are complex (3+ entities with state machines/complex validation)
  - For each entity, document:
    - Entity name, purpose, fields with types
    - Key relationships to other entities
    - Validation rules from functional requirements
    - State transitions if applicable
  - Split entities into:
    - Entities owned by this feature
    - Entities from other features (consumed)

- Step 2: Contracts
  - _Prerequisites: Data model complete_
  - **ALWAYS document contracts inline in `plan.md` `## Contracts` section**
  - **For function/library features:**
    - Define function signatures with comprehensive help/documentation
    - Document parameter types, validation, return values
    - Include 1-2 usage examples per function
  - **For REST/GraphQL API features:**
    - Generate API contracts from functional requirements
    - For each user action → endpoint
    - Use standard REST/GraphQL patterns
    - Output OpenAPI/GraphQL schema to `.xe/specs/{feature-id}/contracts/api.yaml`
    - Reference schema in plan.md, summarize key endpoints
  - **Generate contract tests** from contracts:
    - One test file per endpoint/function
    - Assert request/response schemas or function signatures
    - Tests must fail (no implementation yet)
  - **Extract test scenarios** from user stories:
    - Each story → integration test scenario
    - Quickstart test = story validation steps

- Step 3: Implementation Approach
  - _Prerequisites: Contracts complete_
  - Create `plan.md` based on `.xe/templates/specs/plan.md` template
  - Document HOW the feature will be built from scratch (not as enhancement)
  - Structure using H3 subsections (for 3-8 concerns):
    1. Data Structures (input/output formats, entity schemas)
    2. Core Algorithms (step-by-step logic with numbered lists)
    3. Integration Points (dependencies and consumers)
    4. Error Handling (graceful degradation, edge cases)
    5. Performance Considerations (optimization strategies, profiling)
    6. Testing Strategy (unit/integration/performance approaches, >90% coverage)
  - For complex features with distinct phases: Consider breaking into multiple H2 sections
  - Include code examples (pseudocode or actual syntax) for complex logic
  - Reference `research.md` for design rationale (don't duplicate)
  - Follow specification writing standards (see below)

- Step 4: Usage Examples
  - _Prerequisites: Implementation Approach complete_
  - Document how future developers will consume this feature after it's built
  - Create `plan.md ## Usage Examples` section with 2-3 practical examples:
    1. Basic usage (minimal parameters)
    2. Advanced usage (with optional parameters)
    3. Common integration pattern (if applicable)
  - Show what "working" looks like (validates design makes sense)
  - AI uses these for generating integration code and tests

- Step 5: Task Breakdown
  - _Prerequisites: plan.md complete_
  - Generate `tasks.md` based on the `.xe/templates/specs/tasks.md` template with implementation task breakdown:
    - Contains ONLY repeatable implementation steps (code, tests, docs)
    - Never includes one-time migration/setup actions
    - Describes final state, not incremental changes
    - Written as if building from scratch (use "Create", "Implement", not "Modify", "Add to")
    - Can be processed by AI agents, automation, or humans
  - Generate tasks from design docs (contracts, data model, test scenarios):
    - Each contract → contract test task
    - Each entity → model creation task
    - Each user story → integration test scenario task
    - Implementation tasks to make tests pass
    - Polish tasks (edge cases, documentation, code review)

- Step 6: Rollout Orchestration
  - _Prerequisites: tasks.md complete_
  - Update `.xe/rollouts/rollout-{rollout-id}.md` with full details based on `.xe/templates/specs/rollout.md` template
  - Add feature references to rollout frontmatter: `features: [list]` (can span multiple features)
  - Rollout orchestrates: pre-implementation → tasks.md → post-implementation → cleanup
  - Simple changes may have empty pre/post sections
  - One-time migration/setup actions go in rollout, NOT in tasks.md
  - Follow template instructions for complete structure

- Step 7: Approval Checkpoint
  - Review and approve `plan.md` before implementation (design approval checkpoint):
    - **Data Model**: Are entities/schemas correct?
    - **Contracts**: Are function signatures/APIs correct?
    - **Implementation Approach**: Is algorithm logic sound?
    - **Usage Examples**: Does the design make sense to consume?
  - Review and approve `tasks.md` (implementation checklist complete?)
  - Review and approve rollout plan (orchestration defined?)
  - **DO NOT proceed to implementation without explicit approval**

### Phase 4. Implementation

**Entry point:**

- Follow rollout plan as the orchestrator for the entire workflow

**Rollout orchestrated workflow:**

1. Execute pre-implementation actions (if any)
2. Execute `.xe/specs/{feature-id}/tasks.md` implementation checklist:
   - Extract reusable code from existing code first
   - Create new reusable code next (with unit tests and high coverage)
   - AI-assisted code generation following approved `spec.md` and plan.md
   - Focus only on code required for this task (YAGNI)
   - Keep changes small and scoped to single responsibility
   - Generate or update tests in expected location per architecture spec
   - Validate behavior, not internal implementation details
3. Execute post-implementation actions (if any)
4. Complete immediate cleanup actions
5. Delete rollout plan file when complete
6. Remove entry from `.xe/rollouts/README.md`

**Note**: Long-term cleanup tasks go to Stack backlog, not rollout plan.

### Phase 5. Validation

- Run validation checks:
  - `npm run format` - Prettier formatting for markdown
  - `npm run lint` - PowerShell Script Analyzer
  - `npm run test` - Unit and integration tests
- PowerShell Script Analyzer with zero critical issues
- Ensure all standards in `.xe/standards/` are applied
- Run all unit tests (>90% code coverage target)
- Run integration tests for end-to-end workflows
- Ensure no test warnings or errors
- Automated quality gate enforcement through engineering standards compliance testing
- Manual review for code quality, security, and performance
- Integration testing with existing Stack ecosystem
- Documentation completeness verification

### Phase 6. Review

- Create a new pull request into the default branch
  - Set the title to `[Catalyst][{type}] {feature-name}`, where `{type}` is "Feature" or "Bug" and `{feature-name}` is a friendly display name
  - Summarize the feature or bug fixed in the body description
  - If the change is related to an issue, include `Fixes #{id}`, if the issue is fully resolved, or `Related to #{id}`, if not
- If the project uses GitHub Copilot as a reviewer on PRs, assign `copilot-swe-agent` as a reviewer

## Feature Documentation Structure

Each feature in `.xe/specs/{feature-id}/` contains:

- **`spec.md`** - WHAT: Business requirements, acceptance criteria, success metrics
- **`plan.md`** - HOW: Technical design, architectural decisions, integration patterns
- **`tasks.md`** - TODO: Implementation task breakdown (describes final state)

Templates for each file are located in `.xe/templates/specs/`.

## Rollout Plan Structure

Every feature requires a rollout plan as the orchestrator. See [`.xe/templates/specs/rollout.md`](../templates/specs/rollout.md) for the complete template.

**Directory structure:**

```text
.xe/rollouts/
├── README.md                      # Active rollouts index for AI discovery
└── rollout-{rollout-id}.md        # Individual rollout plans
```

**Key concepts:**

- **Template**: Use [`.xe/templates/specs/rollout.md`](.xe/templates/specs/rollout.md) as starting point
- **Lifecycle**: Placeholder created during specification → completed during planning → deleted after implementation
- **Orchestration**: Coordinates pre-implementation → tasks.md → post-implementation → cleanup
- **Scope**: Can cover multiple features, single feature, or subset of a feature
- **Flexibility**: Simple changes may have empty pre/post/cleanup sections
- **Tracking**: Markdown checkbox lists with status updates in frontmatter
- **Long-term tasks**: Add to Stack backlog, not rollout plan

**Discovery index: `.xe/rollouts/README.md`**

- Lists active rollouts with one-line descriptions
- AI scans this to find relevant rollout for current work
- Updated when rollouts are created or deleted
- Example:

  ```markdown
  # Active Rollouts

  - [rollout-update-metadata.md](./rollout-update-metadata.md) - Create update-metadata orchestrator
  - [rollout-migrate-specs.md](./rollout-migrate-specs.md) - Migrate feature.md → spec.md
  ```

## Specification Writing Standards

### Requirements Specifications (spec.md)

- **Purpose**: Define WHAT the feature does - business requirements, acceptance criteria, success metrics
- **Focus**: User-facing behavior, functional requirements, validation criteria
- **Avoid**: Implementation details, code examples, hardcoded configuration values
- **Living Specification Principle**: Include only requirements applicable to any future implementation; exclude one-time setup/migration steps

### Implementation Plans (plan.md)

- **Purpose**: Define HOW the feature integrates - technical design, component interactions, engineering decisions
- **Focus**: Architectural decisions, data flow, integration points, processing logic
- **Avoid**:
  - Duplicating requirements from `spec.md` (reference instead)
  - Pseudo-code that will drift from implementation (describe flow, don't write code)
  - Hardcoded configuration values (reference config files instead)
  - Score tables or weights (reference `.stack/settings.json`)
- **Best Practices**:
  - Use decision tables and flow diagrams instead of code samples
  - Reference actual implementation files as source of truth
  - Focus on architectural decisions, not implementation details

## Code Generation Standards

### Quality Requirements

- Generated PowerShell functions must include comprehensive comment-based help
- Parameter validation using PowerShell validation attributes is mandatory
- Consistent error handling patterns following Stack conventions
- All code must follow established naming conventions (see `.xe/standards/powershell.md`)

### Integration Requirements

- Module loading and export validation with existing Stack ecosystem
- Comprehensive testing with >90% code coverage
- Engineering standards compliance validation

## Quality Gates

All features must pass:

- **Engineering Standards Compliance**: Stack-EngineeringStandards.Tests.ps1 validation
- **Code Quality**: PowerShell Script Analyzer with zero critical issues
- **Testing**: >90% unit test coverage with comprehensive integration testing
- **Documentation**: Complete `spec.md` and plan.md with implementation documentation
- **Integration**: Stack ecosystem compatibility validation

## Code Review Standards

### Specification Compliance

- Implementation strictly adheres to approved `spec.md` and plan.md
- Business requirements fulfilled per acceptance criteria
- Technical design patterns match architectural specifications

### Quality Enforcement

- Code quality assessed using PowerShell Script Analyzer
- Help documentation complete for all public functions
- Parameter validation implemented for input security
- Error handling patterns follow Stack conventions

### Security and Performance

- Input validation and sanitization patterns implemented
- File system access and path validation security reviewed
- Performance impact assessed for existing Stack operations
- Memory usage and resource consumption validated

## Testing Strategy

### Unit Testing

- Test individual functions and cmdlets in isolation
- Use Pester testing framework with mocking capabilities
- Target >90% code coverage
- Mock external dependencies
- Test edge cases and error conditions

### Integration Testing

- Test end-to-end processing pipeline validation
- Test file system operations with various directory structures
- Test error handling with malformed input scenarios
- Validate performance under load conditions

### Continuous Integration

- GitHub Actions for automated testing on changes
- PowerShell Script Analyzer for code quality
- Prettier formatting for markdown consistency
