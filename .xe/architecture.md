# System Architecture

## Overview

This document defines the technical architecture for the Catalyst Founders Agreement template repository: technology choices, structure, and integration patterns. Feature-specific requirements are documented in individual feature specifications in the `.xe/specs` folder.

For engineering principles and standards, see [`.xe/engineering.md`](engineering.md).

For the development process, see [`.xe/process/development.md`](process/development.md).

## Technology Stack

| Aspect            | Details                                                                 |
| ----------------- | ----------------------------------------------------------------------- |
| Template Engine   | GitHub Templates with dynamic workflow generation                       |
| Automation        | GitHub Actions (YAML workflows) + TypeScript/Node.js scripting         |
| Configuration     | JSON/YAML configuration files, Markdown content templates               |
| Storage           | Git repository (version-controlled, audit trail, collaborative)         |
| Content Format    | Markdown (human-readable, Git-friendly, export-ready)                   |
| AI Integration    | GitHub Copilot                                                          |
| Testing           | Jest (TypeScript), GitHub Actions CI/CD                                 |
| Target Platform   | GitHub                                                                  |
| Performance Goals | Template instantiation <30 seconds; workflow execution <5 minutes       |
| Constraints       | GitHub-native, multi-founder collaborative, non-technical user friendly |
| Scale/Scope       | One agreement per repository, unlimited founders per agreement          |
| Core Runtime      | Node.js 18+, TypeScript, GitHub Actions, Git, Markdown processors       |
| Distribution      | GitHub Template Repository                                              |

## Template Repository Structure

```text
# Template configuration and generation logic
.xe/
├── config/
│   ├── agreement-schema.json     # Agreement structure definition
│   ├── startup-types.json        # Industry/type-specific configurations
│   └── settings.json             # Instance-specific configuration (generated)
├── snippets/{section}/           # Content templates by section/option
├── scripts/                      # TypeScript automation scripts
│   ├── init-agreement.ts         # Initial setup and configuration
│   ├── update-section.ts         # Section content generation
│   ├── finalize-agreement.ts     # Final processing and PDF generation
│   ├── generators/               # Dynamic content generation utilities
│   └── validators/               # Content validation and completeness checks
└── docs/                         # AI context and help content

# GitHub automation and workflows
.github/
├── workflows/
│   ├── initialize-repo.yml       # Sets up the repo
│   ├── section-issue.yml         # Template for section completion
│   └── export-agreement.yml      # Generates final documents
├── ISSUE_TEMPLATE/
│   ├── init-agreement.yml        # Initial configuration template
│   └── sections/                 # Per-section issue templates (generated)
└── CODEOWNERS                    # Generated based on founder configuration

# Documentation and guidance
docs/
├── agreement-guide.md            # Detailed options explanation
└── section/                      # Per-section guidance

founders-agreement.md             # Main agreement document (root level)
progress.md                       # Agreement completion status
package.json                      # Node.js/TypeScript dependencies
README.md                         # Template usage instructions
```

## Engineering Patterns

- **TypeScript Scripts**: Configuration processing, content generation, validation
- **GitHub Actions**: Event-driven automation triggered by issues and file updates  
- **AI Integration**: Issue assignment, content assistance, and collaborative guidance
- **Configuration-Driven**: Dynamic generation based on saved startup settings
