---
name: review-code
description: Reviews code changes for Excalidraw conventions, security, performance, and correctness. Use when the user asks to review code, check a PR, or validate changes.
disable-model-invocation: true
allowed-tools: Read, Grep, Glob, Bash
argument-hint: [file path, PR number, or "staged" for git staged changes]
---

# Review Code

## How to use this command

Type your target after the command in the chat — Cursor appends it as plain text. There is no variable substitution (`$ARGUMENTS`, `$0`, etc. are not supported).

**Usage examples:**

- `/review-code path/to/file.ts` — Review a specific file and its recent changes
- `/review-code 42` — Review PR #42 (Claude will run `gh pr diff 42`)
- `/review-code staged` — Review staged changes (Claude will run `git diff --cached`)
- `/review-code` (no argument) — Review all unstaged changes (Claude will run `git diff`)

**What Claude does for each scenario:**

| Input | Action |
|---|---|
| File path | Reads the file; runs `git diff <path>` for recent changes |
| PR number | Runs `gh pr diff <number>` to fetch the pull request diff |
| `staged` | Runs `git diff --cached` to review staged changes |
| _(nothing)_ | Runs `git diff` to review all unstaged changes |

## What to review

## Checklist

### Architecture & Conventions
- [ ] State changes go through ActionManager or Jotai atoms — not direct mutation
- [ ] Canvas rendering stays in the two-layer pipeline (StaticCanvas/InteractiveCanvas)
- [ ] Cross-package imports use `@excalidraw/*` aliases, not relative paths
- [ ] No modifications to protected files (Renderer.ts, restore.ts, manager.tsx, types.ts, Scene.ts) without justification
- [ ] Functional components preferred (class components only if extending App.tsx)
- [ ] `import type` used for type-only imports

### Security
- [ ] User-provided URLs pass through `normalizeLink()` or `toValidURL()`
- [ ] No `innerHTML`/`dangerouslySetInnerHTML` with user-controlled content
- [ ] No `eval()`, `new Function()`, `document.write()`
- [ ] Iframe embeds validated against domain whitelist
- [ ] `postMessage` calls specify explicit `targetOrigin`

### TypeScript
- [ ] No `any` types introduced — use proper typing
- [ ] No `@ts-ignore` or `@ts-expect-error` added
- [ ] New types added to appropriate package (common types → `packages/common/`, element types → `packages/element/`)

### Performance
- [ ] No expensive operations in render paths
- [ ] Element lookups use `elementsMap` (Map) not array scans
- [ ] Memoization where appropriate for expensive computations

### Testing
- [ ] Changes have corresponding test updates
- [ ] Tests use project helpers: `API.createElement()`, `Pointer`, `Keyboard`, `UI`
- [ ] Snapshots updated if visual/structural changes (`yarn test:update`)

## Output Format

Provide review as:

### Summary
One-paragraph overview of the changes.

### Issues Found
List each issue with severity (🔴 critical / 🟡 warning / 🔵 suggestion), file:line, and explanation.

### Approved / Changes Requested
Final verdict with reasoning.
