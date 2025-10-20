---
name: "pr-update"
description: Review PR feedback, address issues, reply to comments, and push changes automatically
allowed-tools: SlashCommand
argument-hint: <pr-number>
Usage: /catalyst:pr-update <pr-number>
Examples: /catalyst:pr-update 3
---

# Update pull request and respond to feedback

Analyzes PR feedback, implements valid suggestions while respectfully pushing back on questionable ones, replies to comments, and commits changes using the Catalyst `update-pull-request` playbook.

## Parameters

- `pr-number` (required): GitHub PR number to review and address

## Execution

Execute the `update-pull-request` playbook using `/catalyst:run update-pull-request {pr-number} Claude`
