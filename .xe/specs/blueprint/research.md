# Blueprint Research

**Date**: 2025-10-20

## Product Analysis

### Problem Statement

Founders need a structured, collaborative way to create founders agreements that:

- Captures configuration decisions early in the process
- Provides intelligent defaults based on startup type and funding model
- Enables async collaboration with clear sign-off mechanisms
- Integrates AI assistance for content generation and guidance
- Maintains version control and audit trails throughout the process

### Target Users

- Technical and non-technical startup founders (2-4 founders typically)
- Early-stage startups before formal legal incorporation
- Teams needing structured collaboration on legal foundations
- Founders seeking AI-assisted guidance on agreement options

### Core User Journeys

**Journey 1: Initialization & Configuration**

1. Founder creates GitHub repository from template
2. Founder creates initialization issue with 8 configuration questions
3. AI processes responses and generates initial agreement structure
4. System saves configuration to `.xe/config/settings.json`
5. System updates CODEOWNERS with founder information

**Journey 2: Section-by-Section Completion**

1. GitHub Action detects initialization completion
2. System creates per-section issues with targeted instructions
3. AI assigned to each section issue
4. AI runs template script to populate section from snippets
5. AI creates PR for founder review
6. Founders review, comment, approve or request changes
7. Cycle repeats for all sections

**Journey 3: Collaborative Discussion**

1. Founder asks question on section PR or issue
2. AI reads context from `docs/` help content
3. AI provides detailed, contextual guidance
4. Founders discuss and reach consensus
5. Agreement section updated based on discussion

**Journey 4: Finalization**

1. All sections marked complete
2. GitHub Action triggers finalization workflow
3. System runs validation and cleanup
4. System exports to PDF format
5. System creates GitHub release with ISO date (yyyy-MM-dd) version
6. PDF attached to release as downloadable artifact
7. Founders receive completion notification with release link

### Key Technical Challenges

**Challenge 1: Configuration-Driven Content Generation**

- Decision: Use JSON schema + TypeScript scripts for reliable generation
- Rationale: Type-safe, testable, deterministic outputs
- Alternatives considered: Templating engines (Handlebars, Mustache) - rejected due to complexity

**Challenge 2: Multi-Founder Collaboration**

- Decision: Git-based workflow with PR reviews and CODEOWNERS
- Rationale: Leverages GitHub's native collaboration features
- Alternatives considered: Custom approval system - rejected for complexity

**Challenge 3: AI Integration**

- Decision: GitHub Copilot via issue assignment + tuned instructions
- Rationale: Native GitHub integration, no external dependencies
- Alternatives considered: OpenAI API direct integration - rejected for setup friction

**Challenge 4: Section Orchestration**

- Decision: GitHub Actions event-driven architecture
- Rationale: Automated, observable, version-controlled
- Alternatives considered: Manual process - rejected for friction; Webhooks - rejected for complexity

## Feature Breakdown & Dependencies

### Core Entities

- **Startup Configuration**: 8 questions with dropdown options and default behaviors
- **Agreement Document**: Markdown file with structured sections
- **Section**: Individual component of agreement (equity, vesting, IP, governance, etc.)
- **Snippet**: Predefined content block for specific configuration option
- **Issue Template**: Structured form for configuration and section completion
- **Workflow**: GitHub Action triggered by events
- **AI Instructions**: Tuned guidance for AI behavior per context

### Feature Dependency Analysis

**Tier 0 (Foundation - No Dependencies)**

- `repository-structure`: Define and create directory structure, configuration files
- `init-issue-template`: Create initialization issue template with 8 questions
- `agreement-guide`: Write detailed guide explaining all configuration options

**Tier 1 (Core Configuration - Depends on Tier 0)**

- `init-script`: TypeScript script to process init issue and generate base structure
- `snippet-library`: Create predefined content snippets for all section/option combinations
- `settings-schema`: Define JSON schema for configuration storage

**Tier 2 (Automation - Depends on Tier 1)**

- `init-workflow`: GitHub Action to process init issue and trigger script
- `section-orchestration`: GitHub Action to create section-specific issues
- `section-templates`: Dynamic issue templates for each section

**Tier 3 (Section Processing - Depends on Tier 2)**

- `section-script`: TypeScript script to populate sections from snippets
- `ai-instructions`: Tuned instructions for AI collaboration and guidance
- `validation-system`: Content validation and completeness checks

**Tier 4 (Finalization - Depends on Tier 3)**

- `finalization-workflow`: GitHub Action for final processing and release creation
- `export-system`: Generate PDF from markdown and attach to GitHub release with ISO date version
- `progress-tracking`: Visual progress indicators for founders
- `documentation`: Complete product documentation for end users (in `docs/`)

### Complexity Estimates

**Small (1-3 days)**

- `repository-structure`: Directory creation, basic files
- `settings-schema`: JSON schema definition
- `progress-tracking`: Simple status indicators

**Medium (4-7 days)**

- `init-issue-template`: 8-question form with descriptions
- `agreement-guide`: Detailed explanatory content
- `snippet-library`: Content creation for all combinations
- `section-templates`: Dynamic template generation
- `ai-instructions`: Contextual AI guidance
- `documentation`: Product documentation for end users

**Large (8+ days)**

- `init-script`: Complex configuration processing
- `init-workflow`: GitHub Action orchestration
- `section-orchestration`: Multi-issue creation logic
- `section-script`: Template assembly from snippets
- `validation-system`: Comprehensive validation rules
- `finalization-workflow`: Multi-step finalization process with release creation
- `export-system`: PDF generation and GitHub release integration

## Architecture Alignment

All features align with `.xe/architecture.md`:

- TypeScript for scripts (type-safe, testable)
- GitHub Actions for automation (event-driven, native)
- Markdown for content (Git-friendly, export-ready)
- JSON/YAML for configuration (structured, parseable)
- GitHub Copilot for AI (native integration)

All features follow `.xe/engineering.md` principles:

- Modular design (single responsibility per feature)
- Configuration-driven (no hardcoded logic)
- Testable (unit tests for scripts, integration tests for workflows)
- Deterministic (same inputs → same outputs)
- Fail-fast (validation at each step)
