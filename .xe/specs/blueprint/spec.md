# Blueprint: Catalyst Founders Agreement

## Description

This blueprint defines the complete feature roadmap for the Catalyst Founders Agreement product—a GitHub template repository that automates the creation of structured founders agreements through AI-assisted, version-controlled collaboration.

The product transforms complex legal document creation into a guided, collaborative workflow where founders answer configuration questions, AI generates contextually appropriate content, and founders iteratively refine sections until consensus is reached. The final output is a professional PDF.

## Core Entities

**Configuration Profile**
- Startup type, funding intent, contribution balance, time commitment
- Equity flexibility, studio involvement, open source focus, investment avoidance, team size
- Stored as structured JSON, drives all downstream content generation

**Agreement Document**
- Markdown-based structured document with discrete sections
- Version-controlled with full audit trail
- Exports to PDF for legal review

**Content Snippet**
- Predefined content blocks for specific configuration combinations
- Organized by section and option
- Enables deterministic, repeatable agreement generation

**Section**
- Individual agreement component (equity split, vesting, IP assignment, governance, etc.)
- Completed independently via dedicated issue/PR workflow
- Tracks completion status

**Issue Template**
- Structured form for initialization and section completion
- Contains explicit instructions for AI execution
- Includes validation requirements

**GitHub Workflow**
- Event-driven automation for initialization, orchestration, finalization
- Triggered by issue creation, file updates, section completion
- Orchestrates multi-step processes

## Feature Dependency Graph

```mermaid
graph TD
    %% Tier 0: Foundation
    A[repository-structure]
    B[init-issue-template]
    C[agreement-guide]
    D[agreement-template]

    %% Tier 1: Core Configuration
    E[init-script]
    F[agreement-content]
    G[progress-tracking]

    %% Tier 2: Automation
    H[init-workflow]
    I[section-orchestration]

    %% Tier 3: Section Processing
    J[section-script]
    K[ai-guidance]
    L[validation-system]

    %% Tier 4: Finalization
    M[finalization-workflow]
    N[export-system]
    O[user-documentation]

    %% Dependencies
    A --> E
    A --> G
    B --> E
    C --> F
    D --> F
    D --> J

    E --> H
    F --> J
    G --> E
    G --> H
    G --> I
    G --> J
    G --> M

    H --> I
    I --> J

    J --> M
    K --> J
    L --> M

    M --> N
    N --> O
```

## Workflow Sequence Diagram

This diagram shows the end-to-end workflow from initialization through finalization:

```mermaid
sequenceDiagram
    actor Founder
    participant Issue as GitHub Issue
    participant InitWF as init-workflow
    participant InitScript as init-script
    participant Progress as progress-tracking
    participant SecOrch as section-orchestration
    participant AI as AI Agent
    participant SecScript as section-script
    participant FinalWF as finalization-workflow
    participant Export as export-system

    Founder->>Issue: Create initialization issue
    Note over Founder,Issue: 8 configuration questions answered

    Issue->>InitWF: Trigger (issue created)
    InitWF->>InitScript: Execute with issue data

    InitScript->>InitScript: Validate responses against schema
    InitScript->>InitScript: Create founders-agreement.md from template
    InitScript->>Progress: createProgressFile(sections[])
    Progress-->>InitScript: progress.md created
    InitScript->>InitScript: Save startup-settings.json
    InitScript->>InitScript: Update CODEOWNERS
    InitScript-->>InitWF: Success

    InitWF->>Issue: Post summary comment
    InitWF->>Founder: Create PR with generated files

    Founder->>Founder: Review and merge PR

    Note over Founder,SecOrch: PR merged, triggers section orchestration

    SecOrch->>Progress: getSectionStatus() for all sections
    Progress-->>SecOrch: List of incomplete sections

    loop For each incomplete section
        SecOrch->>Issue: Create section issue from template
        SecOrch->>AI: Assign section issue
    end

    loop For each section
        AI->>SecScript: Execute section-script
        SecScript->>SecScript: Load config from startup-settings.json
        SecScript->>SecScript: Select snippets based on config
        SecScript->>SecScript: Assemble section content
        SecScript->>SecScript: Update founders-agreement.md
        SecScript->>Progress: updateSectionStatus(sectionId, 'complete')
        Progress-->>SecScript: Updated
        SecScript->>AI: Create PR with section changes
        AI->>Founder: Request review
        Founder->>Founder: Review and merge section PR
    end

    Progress->>Progress: All sections complete
    Progress->>FinalWF: Trigger (all sections done)

    FinalWF->>Progress: isComplete()
    Progress-->>FinalWF: true
    FinalWF->>FinalWF: Run validation-system
    FinalWF->>Export: Execute export-system
    Export->>Export: Convert MD to PDF
    Export-->>FinalWF: founders-agreement.pdf
    FinalWF->>FinalWF: Create GitHub release (ISO date)
    FinalWF->>FinalWF: Attach PDF to release
    FinalWF->>Founder: Notify completion
```

## Features

### Tier 0: Foundation (No Dependencies)

#### Feature 1: repository-structure
**ID**: `repository-structure`
**Dependencies**: None
**Complexity**: Medium
**Priority**: 1

**Scope**: Define and create the complete directory structure, base configuration files, GitHub repository settings, and labels required by all other features. Does NOT create content files (founders-agreement.md, progress.md) - those are owned by their respective features.

**Deliverables**:

1. **Directory Structure**:
   - `.xe/config/` - Configuration schemas and settings
   - `.xe/snippets/` - Content templates directory structure
   - `.xe/scripts/` - TypeScript automation scripts
   - `docs/` - End-user documentation
   - `.github/workflows/` - GitHub Actions
   - `.github/ISSUE_TEMPLATE/` - Issue forms
   - `templates/` - Agreement template files

2. **Base Configuration Files**:
   - `package.json` - Node.js project configuration with dependencies
   - `tsconfig.json` - TypeScript configuration
   - `jest.config.js` - Test configuration
   - `.gitignore` - Git ignore rules
   - `README.md` - Repository overview

3. **GitHub Repository Configuration**:
   - Enable template repository flag
   - Configure branch protection for main branch
   - Set up required status checks

4. **GitHub Labels**:
   - `ready-for-processing` - Issue ready for automation
   - `section:{name}` - Labels for each agreement section (8 labels)
   - `status:draft` - Agreement in draft state
   - `status:in-review` - Agreement under review
   - `status:complete` - Agreement finalized

#### Feature 2: init-issue-template
**ID**: `init-issue-template`
**Dependencies**: None
**Complexity**: Medium
**Priority**: 2

**Scope**: Create GitHub issue form template with 8 configuration questions, each with dropdown options and clear descriptions. Template includes explicit instructions for AI on how to process responses and which script to execute.

**Questions** (each with 3 options + "Undecided"):

1. **Funding Intent**: How do you plan to fund the company?
   - Bootstrap - No external funding planned
   - Raise Capital - Plan to raise VC/angel funding
   - Undecided - Haven't decided yet

2. **Contribution Balance**: Are founder contributions expected to be equal or unequal?
   - Equal - All founders contribute equally
   - Weighted - Contributions vary by role/time/capital
   - Undecided - Haven't decided yet

3. **Time Commitment**: Will all founders work full-time?
   - Full-Time - All founders fully committed
   - Mixed - Some full-time, some part-time
   - Undecided - Haven't decided yet

4. **Equity Flexibility**: How flexible should equity splits be over time?
   - Fixed - Equity locked at founding
   - Dynamic - Equity adjusts based on contribution
   - Undecided - Haven't decided yet

5. **Studio Involvement**: Is a studio or parent company involved?
   - Yes - Studio/parent company involved
   - No - Independent startup
   - Undecided - Haven't decided yet

6. **Open Source Focus**: Will the product be open source?
   - Yes - Core product will be open source
   - No - Proprietary/closed source
   - Undecided - Haven't decided yet

7. **Investment Avoidance**: Do you want to avoid traditional VC investment?
   - Yes - Avoiding VC, alternative funding
   - No - Open to VC investment
   - Undecided - Haven't decided yet

8. **Team Size**: How many founders are there?
   - 2 Founders
   - 3-4 Founders
   - 5+ Founders

**Default Behavior Mapping**: "Undecided" always means no defaults applied - founders must explicitly choose options during refinement.

#### Feature 3: agreement-guide
**ID**: `agreement-guide`
**Dependencies**: None
**Complexity**: Medium
**Priority**: 3

**Scope**: Comprehensive documentation in `docs/` that explains each configuration question, all available options, the implications of each choice, and how they affect the generated agreement. Used by founders during initialization and by AI when answering questions.

**Content Areas**:
- Overview of founders agreements
- Detailed explanation of each of 8 configuration questions
- When to choose each option
- How options interact with each other
- Common scenarios and recommended configurations
- Glossary of legal and startup terms

#### Feature 4: agreement-template
**ID**: `agreement-template`
**Dependencies**: None
**Complexity**: Medium
**Priority**: 4

**Scope**: Markdown template file defining the structure of the founders agreement document with placeholders for sections, founder names, dates, and configuration-driven content.

**Template Structure**:
- Document header with title and metadata placeholders
- Introduction section with purpose statement
- Placeholders for 8 core agreement sections:
  - Equity split and ownership
  - Vesting schedules and acceleration
  - IP assignment and licensing
  - Governance and decision-making
  - Roles and responsibilities
  - Capital contributions
  - Exit provisions and buyouts
  - Dispute resolution
- Signature blocks with founder name placeholders
- Document footer with version and date placeholders

**Usage**: This template is instantiated by init-script and populated by section-script with content from content-snippets based on startup configuration.

---

### Tier 1: Core Configuration (Depends on Tier 0)

#### Feature 5: init-script
**ID**: `init-script`
**Dependencies**: `repository-structure`, `init-issue-template`, `progress-tracking`
**Complexity**: Large
**Priority**: 5

**Scope**: TypeScript script with embedded JSON schema that processes initialization issue responses, validates inputs, generates base agreement structure from template, initializes progress tracking, saves configuration, and updates CODEOWNERS file.

**Functionality**:
- Define JSON schema for `.xe/config/startup-settings.json` (8 questions, founder info, metadata)
- Parse issue body and extract question responses
- Validate responses against embedded schema
- Apply default behaviors based on configuration profile
- Instantiate `founders-agreement.md` from agreement-template with section placeholders
- Call progress-tracking API to create initial `progress.md` with section checklist
- Save validated configuration to `.xe/config/startup-settings.json`
- Update `.github/CODEOWNERS` with founder information
- Create summary comment on initialization issue

**Configuration Schema** (embedded in script):
- Configuration responses (8 questions with selected options)
- Founder details (names, emails, roles)
- Repository metadata (creation date, template version)
- Validation rules for all fields

**Files Created**:
- `founders-agreement.md` - Instantiated from agreement-template with placeholders
- `.xe/config/startup-settings.json` - Validated configuration
- `progress.md` - Created via progress-tracking library
- Updated `.github/CODEOWNERS` - With founder information

#### Feature 6: agreement-content
**ID**: `agreement-content`
**Dependencies**: `agreement-guide`, `agreement-template`
**Complexity**: Large
**Priority**: 6

**Scope**: Complete content library including agreement template structure, content snippets for all configuration combinations, and section-specific issue templates for AI-driven completion.

**Deliverables**:

1. **Agreement Template** (`templates/agreement-template.md`):
   - Document header with metadata placeholders
   - Introduction section with purpose statement
   - 8 core section placeholders (equity, vesting, IP, governance, roles, capital, exit, dispute)
   - Signature blocks and footer

2. **Content Snippets** (`.xe/snippets/{section}/{option}.md`):
   - Equity split models (equal, weighted, dynamic)
   - Vesting schedules (standard, custom, none)
   - IP assignment clauses
   - Governance structures (simple majority, board-based, role-weighted)
   - Capital contribution terms
   - Exit and buyout provisions
   - Non-compete and non-solicitation clauses
   - Dispute resolution mechanisms

3. **Section Issue Templates** (`.github/ISSUE_TEMPLATE/sections/`):
   - One template per agreement section (8 templates)
   - Section context and configuration references
   - Explicit script execution instructions for AI
   - Validation requirements and acceptance criteria

**Coverage**: All meaningful combinations of 8 configuration questions across all agreement sections.

#### Feature 7: progress-tracking
**ID**: `progress-tracking`
**Dependencies**: `repository-structure`
**Complexity**: Small
**Priority**: 7

**Scope**: Utility library for creating and updating `progress.md` with visual progress indicators showing which sections are complete, in progress, or not started. Consumed by init-script, section-script, and finalization-workflow.

**API Functions**:
- `createProgressFile(sections[])` - Initialize progress.md with section checklist
- `updateSectionStatus(sectionId, status)` - Mark section as complete/in-progress
- `getCompletionPercentage()` - Calculate overall progress
- `isComplete()` - Check if all sections are done
- `getSectionStatus(sectionId)` - Get current status of a section

**Display Format**:
- Section checklist with status icons (✅ complete, 🔄 in-progress, ⬜ not started)
- Percentage complete indicator
- Last updated timestamp
- Link to related issue/PR for each section
- Overall agreement status (Draft, In Review, Complete)

**Updates**:
- Called by init-script to create initial progress.md
- Called by section-script after each section completion
- Called by section-orchestration to check which sections need issues
- Consumed by finalization-workflow to determine readiness

---

### Tier 2: Automation (Depends on Tier 1)

#### Feature 8: init-workflow
**ID**: `init-workflow`
**Dependencies**: `init-script`, `progress-tracking`
**Complexity**: Large
**Priority**: 8

**Scope**: GitHub Action workflow that triggers when initialization issue is created/updated, executes init-script, and orchestrates the initialization process.

**Triggers**:
- Issue created with `init-agreement` template
- Issue updated with label `ready-for-processing`

**Steps**:
1. Validate issue has required label and assignments
2. Install dependencies (npm install)
3. Execute init-script with issue data
4. Commit generated files to new branch
5. Create PR for founder review
6. Post summary comment with next steps
7. Close initialization issue when PR merged

#### Feature 9: section-orchestration
**ID**: `section-orchestration`
**Dependencies**: `init-workflow`, `agreement-content`, `progress-tracking`
**Complexity**: Large
**Priority**: 9

**Scope**: GitHub Action workflow that detects when `founders-agreement.md` is created (post-initialization) and automatically creates section-specific issues for each agreement section using templates from agreement-content, assigning them to AI.

**Triggers**:
- Creation or update of `founders-agreement.md`
- Detection of incomplete sections in `progress.md`

**Functionality**:
- Read agreement structure and identify sections
- For each incomplete section, create issue from section template (from agreement-content feature)
- Assign issue to configured AI agent (e.g., copilot-swe-agent, Claude Code)
- Add section context and configuration from startup-settings.json
- Include explicit instructions for which script to run
- Track created issues in `progress.md`

---

### Tier 3: Section Processing (Depends on Tier 2)

#### Feature 10: section-script
**ID**: `section-script`
**Dependencies**: `agreement-content`, `agreement-template`, `section-orchestration`, `progress-tracking`
**Complexity**: Large
**Priority**: 10

**Scope**: TypeScript script that reads configuration, selects appropriate snippets from agreement-content, assembles section content, and updates `founders-agreement.md` with the completed section.

**Functionality**:
- Read section ID from script arguments
- Load configuration from startup-settings.json
- Determine which snippets to use based on configuration
- Assemble section content from agreement-content snippets
- Insert section content into correct location in agreement (based on agreement-template structure)
- Update progress.md to mark section complete
- Generate PR with section changes
- Validate section completeness

**Selection Logic**:
- Map configuration profile to snippet paths
- Handle "Undecided" options (use neutral/placeholder content)
- Merge multiple snippets when required
- Apply variable substitution (founder names, dates, etc.)

#### Feature 11: ai-guidance
**ID**: `ai-guidance`
**Dependencies**: `agreement-guide`
**Complexity**: Medium
**Priority**: 11

**Scope**: Tuned instruction files for GitHub Copilot, Claude Code, and other AI agents that guide collaborative behavior, tone, and approach when answering founder questions about agreement options.

**Instruction Areas**:
- How to interpret founder questions
- When to reference agreement-guide documentation
- Tone and style (collaborative, educational, non-legal-advice disclaimer)
- How to explain tradeoffs between options
- When to suggest alternatives
- How to handle ambiguous requests
- Disclaimers about not providing legal advice

**File Locations**:
- `CLAUDE.md` in repo root (for Claude Code)
- `.github/copilot-instructions.md` (for GitHub Copilot)
- Future: `.xe/ai/` directory for additional agent-specific instructions

**Supported AI Platforms**:
- Claude Code
- GitHub Copilot
- Future: Cursor, Windsurf, other AI coding assistants

#### Feature 12: validation-system
**ID**: `validation-system`
**Dependencies**: `init-script`, `section-script`
**Complexity**: Large
**Priority**: 12

**Scope**: TypeScript validation utilities that check agreement completeness, section consistency, configuration validity, and readiness for finalization.

**Validation Rules**:
- All required sections present and non-empty
- Founder information complete and consistent
- Configuration profile matches applied content
- No placeholder text remaining
- Cross-references between sections valid
- Markdown formatting correct
- All progress.md items marked complete

**Validation Triggers**:
- After each section update (section-level validation)
- Before finalization (full agreement validation)
- On-demand via script execution

---

### Tier 4: Finalization (Depends on Tier 3)

#### Feature 13: finalization-workflow
**ID**: `finalization-workflow`
**Dependencies**: `validation-system`, `section-script`, `progress-tracking`
**Complexity**: Large
**Priority**: 13

**Scope**: GitHub Action workflow that triggers when all sections are complete, runs final validation, executes export-system, creates GitHub release with ISO date version, and attaches PDF.

**Triggers**:
- All items in progress.md marked complete
- Manual workflow dispatch with "finalize" label

**Steps**:
1. Run full agreement validation
2. Execute export-system to generate PDF
3. Create git tag with ISO date (yyyy-MM-dd)
4. Create GitHub release with tag
5. Attach PDF to release as artifact
6. Update README with release link
7. Post completion notification comment
8. Archive all section issues
9. Clean up temporary files

#### Feature 14: export-system
**ID**: `export-system`
**Dependencies**: `finalization-workflow`, `validation-system`
**Complexity**: Large
**Priority**: 14

**Scope**: TypeScript script that converts `founders-agreement.md` to professional PDF format with proper styling, formatting, and legal document conventions.

**Functionality**:
- Read and parse markdown agreement
- Apply professional document styling
- Generate table of contents
- Add headers/footers with founder names and date
- Format sections with proper legal numbering
- Embed metadata (version, creation date, founders)
- Output PDF to `.xe/output/founders-agreement-{date}.pdf`
- Validate PDF generation success

**Technical Approach**:
- Use markdown-to-pdf library (e.g., markdown-pdf, puppeteer)
- Custom CSS for legal document formatting
- Ensure reproducible, deterministic output

#### Feature 15: user-documentation
**ID**: `user-documentation`
**Dependencies**: `export-system`
**Complexity**: Medium
**Priority**: 15

**Scope**: Complete end-user product documentation in `docs/` covering how to use the template, customize agreements, understand workflows, and troubleshoot issues. This is the instructional guide that walks founders through the initialization questions and helps them understand their options.

**Documentation Files**:
- `docs/README.md` - Overview and quick start
- `docs/configure.md` - Detailed configuration guide (from feature 3)
- `docs/workflow.md` - How the automation works
- `docs/customize.md` - How to customize snippets and templates
- `docs/troubleshoot.md` - Common issues and solutions
- `docs/work-with-ai.md` - How to work effectively with AI
- `docs/disclaimer.md` - Not legal advice disclaimer
- `docs/faq.md` - Frequently asked questions

---

## Success Criteria

- [ ] All features documented with unique IDs
- [ ] Feature dependency graph is acyclic and correctly represents dependencies
- [ ] Features are organized into 5 dependency tiers
- [ ] Each feature has scope description (1-2 sentences)
- [ ] Each feature has complexity estimate (Small, Medium, Large)
- [ ] Each feature has priority order number
- [ ] All features align with product vision in `.xe/product.md`
- [ ] Blueprint enables implementation of complete Catalyst Founders Agreement product

## Implementation Approach

Features will be implemented using `/catalyst:run start-rollout {feature-id}` for each feature in dependency order. Features within the same tier can be implemented in parallel if multiple developers are available.

**Recommended Implementation Order**:
1. Tier 0 features (parallel): repository-structure, init-issue-template, agreement-guide, agreement-template
2. Tier 1 features (sequential): startup-config-schema → init-script, then content-snippets
3. Tier 2 features (sequential): init-workflow → section-orchestration → section-templates
4. Tier 3 features (parallel): section-script, ai-guidance, validation-system
5. Tier 4 features (sequential): finalization-workflow → export-system → progress-tracking → user-documentation

Total estimated timeline: 12-18 weeks with 1 full-time developer, 6-9 weeks with 2 developers working in parallel on independent tiers.
