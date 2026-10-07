---
owner: "Product Manager"
reviewers:
  required: ["Architect"]
  optional: []
---

# Playbook: Start Blueprint

## Description

Creates or updates the product blueprint which breaks down the product vision into discrete features with clear dependencies, scope boundaries, and implementation priorities. This blueprint serves as the architectural roadmap for subsequent feature development.

## Inputs

- `blueprint-description` (required) - High-level description of the product to build, its purpose, target users, and key capabilities.
- `execution-mode` (optional) - Execution approach: "manual" (human checkpoints) or "autonomous" (auto-proceed). Defaults to "manual".

## Outputs

- Feature branch at `xe/{username}/blueprint`
- Blueprint specification at `.xe/specs/blueprint/spec.md` - Product-level functional/non-functional requirements and success criteria
- Blueprint plan at `.xe/specs/blueprint/plan.md` - Feature catalog and dependency graph that realizes the spec's requirements
- Blueprint tasks at `.xe/specs/blueprint/tasks.md` - Steps to populate the spec
- Blueprint research at `.xe/specs/blueprint/research.md` - Product analysis and competitive research
- Pull request for code review and merge

## 1. Validate inputs

- Ensure `blueprint-description` is provided and is a non-empty string
- If `execution-mode` provided, validate it matches allowed values: "manual" or "autonomous"
- All validations are soft - system will auto-correct or prompt for clarification

## 2. Initialize

- Read `.xe/process/development.md` for workflow phases
- Read `.xe/product.md` for product context
- Scan `.xe/specs/` directory for existing features
- **Analyze blueprint description for completeness:**
  - Assess what context is missing to understand the full product scope
  - Ask dynamic clarifying questions based on gaps identified:
    - If user personas unclear → Ask about target users and their needs
    - If user journeys unclear → Ask about key workflows and use cases
    - If platform unclear → Ask about platform/technology requirements
    - If integrations unclear → Ask about external dependencies
    - If business model unclear → Ask about monetization or value delivery
  - Continue asking questions until you have enough context to define discrete, implementable features

## 3. Research

### Development Process Phase 1: Analysis 🔬

1. Review `.xe/product.md` and `.xe/architecture.md` context
2. Conduct market research and save in `.xe/competitive-analysis.md` if never documented, >3 months old, or major product pivot
3. Analyze product requirements and identify core user journeys
4. Think deeply about the product architecture and feature breakdown:
   - Define a modular design with discrete features based on `.xe/engineering.md` principles (e.g., Separation of Concerns, Single Responsibility, Don't Repeat Yourself)
   - Break out independently implementable and testable sub-features for reuse and maintainability with clear scope boundaries (1-2 sentences per feature)
   - Estimate complexity for each feature (Small, Medium, Large)
5. Build feature dependency graph:
   - Identify which features depend on others
   - Ensure no circular dependencies
   - Group features into dependency tiers (features in same tier can be built in parallel)
6. Prioritize features:
   - Order by dependencies first (can't build X without Y)
   - Within same dependency tier, order by business value and risk
7. Document findings in `.xe/specs/blueprint/research.md`
8. **Human Checkpoint** → Present TLDR feature list and dependency graph for review:

   |    #     | Option                     | Notes                               |
   | :------: | -------------------------- | ----------------------------------- |
   | 🔵 **A** | **Continue (review spec)** | Proceed to specification phase      |
   | 🟢 **B** | **Change feature graph**   | Describe what to change             |
   | 🟣 **Z** | **Continue autonomously**  | Auto-approve spec and proceed to PR |

   **Wait for explicit user confirmation before proceeding**

## 4. Execute

### Development Process Phase 0: Setup 🛠️

1. Create feature branch: `xe/{username}/blueprint`
2. Create placeholder rollout plan at `.xe/rollouts/rollout-blueprint.md`
3. Add entry to `.xe/rollouts/README.md` index

### Development Process Phase 2: Specification Development 📝

1. Create `.xe/specs/blueprint/spec.md` using the `.xe/templates/specs/spec.md` template:
   - **Description:** Product vision and blueprint purpose
   - **Core Entities:** Domain concepts that span multiple features
   - **Functional Requirements:** Product-level FRs stated as capabilities the system MUST provide (e.g., "system MUST allow founders to configure X", "system MUST generate Y structured into Z"). These describe WHAT the product does, never HOW (no file paths, API signatures, workflow triggers, or feature IDs — those are implementation details and belong in plan.md)
   - **Non-Functional Requirements:** Product-level qualities (determinism, extensibility, auditability, etc.)
   - **Success Criteria:** Criteria for successfully implementing the blueprint (i.e., delivering the product) — NOT criteria about the spec document's own completeness. Example: "all features are implemented and merged", "a user can complete the end-to-end workflow", "the product fulfills `.xe/product.md`"
   - **IMPORTANT:** The feature catalog (IDs, dependencies, complexity, priority, deliverables) and the dependency graph are the implementation mechanism for these requirements — they belong in plan.md, not spec.md. spec.md states requirements; plan.md states how features realize them.
2. **Human Checkpoint** → Present specification for review:

   |    #     | Option                     | Notes                               |
   | :------: | -------------------------- | ----------------------------------- |
   | 🔵 **A** | **Continue (review plan)** | Proceed to planning phase           |
   | 🟢 **B** | **Change specification**   | Describe what to change             |
   | 🟣 **Z** | **Continue autonomously**  | Auto-approve plan and proceed to PR |

   **Wait for explicit user confirmation before proceeding**

### Development Process Phase 3: Planning 🏗️

1. Create `.xe/specs/blueprint/plan.md` using the `.xe/templates/specs/plan.md` template:
   - **Feature Dependency Graph:** Mermaid graph (must be acyclic), organized by tier
   - **Feature Catalog:** For each feature: ID (kebab-case), name, 1-2 sentence scope, dependencies (feature IDs), complexity (Small/Medium/Large), priority order, deliverables
   - **Data Model:** Not applicable (blueprints don't define data models — those belong to individual features)
   - **Implementation Approach:** Recommended feature build order, parallelization opportunities, critical path
   - **Constraints:** This populates the plan.md and spec.md files only - no code implementation
   - **IMPORTANT:** Do not duplicate the dependency graph or feature catalog into spec.md — spec.md links here instead.
2. Create `.xe/specs/blueprint/tasks.md` using the `.xe/templates/specs/tasks.md` template:
   - Task 1: Populate spec.md with core entities, functional requirements, and non-functional requirements
   - Task 2: Add feature dependency graph to plan.md (mermaid format)
   - Task 3: Add all features to plan.md's feature catalog with IDs, dependencies, scope, complexity, priority
   - Task 4: Validate no circular dependencies in graph
   - Task 5: Validate all features have required fields
   - Task 6: Review for completeness and accuracy
3. **Human Approval Checkpoint (if not running autonomously)** → Present Implementation Plan for review:

   |    #     | Option          | Notes                           |
   | :------: | --------------- | ------------------------------- |
   | 🔵 **A** | **Continue**    | Proceed to implementation phase |
   | 🟢 **B** | **Change plan** | Describe what to change         |

   **Wait for explicit user confirmation before proceeding**

### Development Process Phase 4: Implementation Execution 🚀

1. Execute pre-implementation actions (if any in rollout plan)
2. Execute `.xe/specs/blueprint/tasks.md` to populate `.xe/specs/blueprint/spec.md` and `.xe/specs/blueprint/plan.md`
3. **IMPORTANT:** This ONLY populates the spec.md and plan.md files with requirements and features. It does NOT implement any features via code.
4. Validate the blueprint:
   - All features have unique IDs (kebab-case)
   - Dependency graph is present and acyclic
   - Features are numbered in priority/dependency order
   - Each feature has: ID, dependencies, scope, complexity, priority
   - spec.md contains requirements only — no feature catalog or dependency graph duplicated from plan.md
5. Execute post-implementation actions (if any in rollout plan)
6. Complete immediate cleanup actions
7. Feature implementation tracking lives in `tasks.md` (one task per feature, checked off as each is implemented) — do NOT add a "Feature Status" section or per-feature status labels to the rollout plan. The rollout plan only tracks the blueprint rollout itself (pre/post-implementation actions, cleanup).
8. **Keep rollout plan and README entry** - Do NOT delete until all blueprint features are implemented

**Next Steps After Blueprint PR is Merged:**

- Features will be implemented one-by-one using `/catalyst:run start-rollout {feature-id}`
- Each feature implementation will read `.xe/specs/blueprint/spec.md` for context
- Update `rollout-blueprint.md` feature status as features are completed
- Only delete rollout plan when all features are complete

## 5. Verify

Verify all items in Success Criteria section below are met:

- Blueprint spec at `.xe/specs/blueprint/spec.md` states product-level functional/non-functional requirements only
- Blueprint plan at `.xe/specs/blueprint/plan.md` documents all features with IDs, dependencies, scope, complexity, and priority
- Dependency graph is present in plan.md and acyclic
- Features are prioritized appropriately

## 6. Request review

1. Create pull request into default branch
2. Set title: `[Catalyst][Blueprint] {product-name} Blueprint`
3. Summarize product vision and feature breakdown in body description
4. Link related issues with `Fixes #{id}` or `Related to #{id}`
5. Assign reviewers per `.xe/product.md` team roles if defined (both human and AI reviewers)

## 7. Publish

Post PR comment with:

- Link to blueprint spec (`.xe/specs/blueprint/spec.md`)
- Links to plan, tasks, and research docs
- Summary of features identified (count, complexity breakdown)
- Recommended starting feature based on dependencies
- Next steps: Merge blueprint, then start implementing features via `/catalyst:run start-rollout {feature-id}`

## Error handling

**Implementation Failures:**

- If implementation task fails: preserve completed work, document blocker in rollout plan
- Escalate to human review if blocker cannot be resolved

**Plan Changes During Implementation:**

- Stop current implementation immediately if plan becomes invalid
- Document what was completed and what needs modification in rollout plan under "Changes Log"
- Never deviate from approved plan without user consent

**Rollback Procedures:**

- Clean up partial files if user cancels during implementation
- Document what was attempted in rollout plan or PR comment

**Context/Dependency Issues:**

- If required files missing (templates, architecture docs), halt and notify user
- If external dependencies unavailable, document blocker in rollout plan and suggest alternatives
- If merge conflicts detected, resolve or escalate to human review

## Success criteria

- [ ] Feature branch created at `xe/{username}/blueprint`
- [ ] Rollout plan created at `.xe/rollouts/rollout-blueprint.md` with:
  - [ ] References `tasks.md` for feature implementation tracking (no separate "Feature Status" section)
  - [ ] Entry added to `.xe/rollouts/README.md` index
- [ ] Blueprint spec created at `.xe/specs/blueprint/spec.md` with:
  - [ ] Product vision documented
  - [ ] Core entities listed
  - [ ] Functional and non-functional requirements documented (no feature catalog or dependency graph)
  - [ ] Success criteria describe blueprint implementation (product delivery), not spec document completeness
- [ ] Blueprint plan created at `.xe/specs/blueprint/plan.md` with:
  - [ ] Feature dependency graph (acyclic, mermaid format)
  - [ ] All features documented with ID, dependencies, scope, complexity, and priority
- [ ] Blueprint tasks created at `.xe/specs/blueprint/tasks.md`
- [ ] Product research documented at `.xe/specs/blueprint/research.md`
- [ ] Pull request created with proper title and description
- [ ] Reviewers assigned per `.xe/product.md` if defined
- [ ] Rollout plan kept active (will be deleted only when all features are complete)
