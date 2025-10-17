---
name: "run"
description: Execute Catalyst playbooks with intelligent input processing and context enhancement
allowed-tools: Read, Edit, Write, Glob, Grep, Bash, Task, TodoWrite
argument-hint: <playbook-id> [input1] [input2] ..
Usage: /catalyst:run <playbook-id> [input1] [input2] [input3] ..
Examples:
  /catalyst:run set-playbook "Create a playbook for automated code review"
  /catalyst:run new-feature user-dashboard "Dashboard for managing user accounts"
  /catalyst:run deploy-service payment-api production
---

# Run Catalyst Playbook

Execute a Catalyst playbook with intelligent input processing and context enhancement based on the playbook's requirements and structure

## Purpose

This command locates, parses, and executes Catalyst playbooks while intelligently enhancing user inputs based on the playbook's context, requirements, and implementation steps

## Usage

```bash
/catalyst:run <playbook-id> [input1] [input2] [input3] ..
```

## Parameters

- `playbook-id` (required): The ID of the playbook to execute
- `input1`, `input2`, etc. (optional): Input parameters to pass to the playbook

## Process

1. **Verify inputs**
   - Verify playbook ID is specified
   - Verify playbook exists at `.xe/playbooks/{playbook-id}.md`
   - If not found, list available playbooks and stop execution
2. **Read playbook**
   - Read the playbook file at `.xe/playbooks/{playbook-id}.md`
   - Extract inputs, outputs, error handling, success criteria, and execution steps
3. Enhance inputs intelligently
   - Map command inputs to playbook inputs
   - Apply smart transformations (e.g., convert names to kebab-case when appropriate)
   - Use product details in `.xe/product.md` and features defined in `.xe/specs/` for product and feature context, if needed to enrich inputs
   - Use playbook description to fill knowledge gaps
   - Use deep reasoning when processing inputs
   - Validate required inputs and provide helpful error messages if missing or insufficient
4. Execute playbook steps
   - Follow playbook steps sequentially starting from numbered H2 sections (e.g., `1. Validate inputs`)
   - Use mapped inputs for placeholders as appropriate
5. Provide execution summary (if not covered by playbook steps)
   - Report what was accomplished
   - Note any assumptions made or manual review needed
   - Highlight any errors or warnings encountered

## Error handling

**Common issues and solutions:**

- **Playbook not found** - List available playbooks in `.xe/playbooks/` and suggest closest matches
- **Missing or invalid inputs** - Show required inputs with descriptions from playbook "Inputs" section
- **Execution failures** - Follow playbook "Error Handling" section for specific recovery strategies
- **Validation errors** - Provide specific guidance based on playbook requirements and quality assurance steps
- **Malformed playbook** - Report parsing errors and suggest fixes for playbook structure

Always attempt to resolve issues intelligently based on available context. If issues cannot be resolved internally, provide clear guidance on how to resolve issues and retry the command.

## Success criteria

- [ ] Playbook located and parsed successfully
- [ ] All playbook steps completed
- [ ] Playbook outputs created successfully
- [ ] Playbook success criteria are fully met
- [ ] User receives execution summary

## Examples

```bash
# Execute set-playbook with description
/catalyst:run set-playbook "Create a playbook for automated code review"
```

This will:

1. Find `set-playbook.md` in `.xe/playbooks/`
2. Parse input requirements (description field)
3. Enhance the description with context
4. Execute playbook steps to create new playbook

```bash
# Execute new-feature with multiple inputs
/catalyst:run new-feature user-dashboard "Dashboard for managing user accounts"
```

This will:

1. Find `new-feature.md` playbook
2. Map inputs to requirements (feature-name, description)
3. Convert "user-dashboard" to kebab-case
4. Execute feature creation workflow
