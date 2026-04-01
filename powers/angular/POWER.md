---
name: "Angular"
displayName: "Angular"
description: "Complete Angular development power with best practices, code patterns, and MCP tools for building modern Angular applications. Covers components, signals, forms, routing, DI, testing, accessibility, styling, and CLI tooling."
keywords: ["angular", "angular cli", "ng", "component", "signal", "directive", "pipe", "rxjs", "ngrx", "zoneless"]
author: "Youssef Fathalla"
---

# Angular

## Overview

This power provides comprehensive Angular development guidance combined with the Angular CLI MCP server. It covers every major aspect of Angular development — from components and signals to routing, forms, dependency injection, testing, accessibility, and CLI tooling.

The Angular CLI MCP server gives you direct access to tools for searching documentation, finding code examples, listing workspace projects, getting best practices, and migrating to OnPush/zoneless change detection.

## Onboarding

### Step 1: Validate Angular CLI

Before using this power, ensure the Angular CLI is available:

```bash
ng version
```

If not installed, install it globally:

```bash
npm install -g @angular/cli
```

### Step 2: Analyze the project

Always start by running `list_projects` to understand the workspace structure, Angular version, test framework, style language, and selector prefix before making any changes.

### Step 3: Version-aware guidance

Always check the project's Angular version before providing guidance. Best practices and available features vary significantly between versions. Use `get_best_practices` with the workspace path for version-specific guidance.

## Angular Developer Guidelines

1. Always analyze the project's Angular version before providing guidance, as best practices and available features can vary significantly between versions. If creating a new project with Angular CLI, do not specify a version unless prompted by the user.

2. When generating code, follow Angular's style guide and best practices for maintainability and performance. Use the Angular CLI for scaffolding components, services, directives, pipes, and routes to ensure consistency.

3. Once you finish generating code, run `ng build` to ensure there are no build errors. If there are errors, analyze the error messages and fix them before proceeding.

## Creating New Projects

If no guidelines are provided by the user, here are default rules to follow when creating a new Angular project:

1. Use the latest stable version of Angular unless the user specifies otherwise.
2. Use Signal Forms for form management in new projects (available in Angular v21 and newer).

**Execution Rules for `ng new`:**

**Step 1: Check for an explicit user version.**

- If the user requests a specific version, bypass local installations and use `npx`.
- Command: `npx @angular/cli@<requested_version> new <project-name>`

**Step 2: Check for an existing Angular installation.**

- If no specific version is requested, run `ng version` to check if the CLI is installed.
- If it succeeds, use: `ng new <project-name>`

**Step 3: Fallback to Latest.**

- If no version requested AND `ng version` fails, use: `npx @angular/cli@latest new <project-name>`

## When to Load Steering Files

This power includes detailed steering files for specific Angular topics. Load them on-demand based on the task:

- Working with components, inputs, outputs, or host elements → `components.md`
- Working with signals, computed, linkedSignal, resource, or effects → `reactivity.md`
- Working with forms (signal forms, reactive forms, template-driven) → `forms.md`
- Working with dependency injection, services, or providers → `dependency-injection.md`
- Working with routing, guards, resolvers, or navigation → `routing.md`
- Working with unit tests, component harnesses, router testing, or E2E → `testing.md`
- Working with styling, Tailwind CSS, or animations → `styling.md`
- Working with accessible components (Accordion, Listbox, Combobox, Menu, Tabs, Toolbar, Tree, Grid) → `accessibility.md`
- Working with Angular CLI commands or MCP server configuration → `tooling.md`

## Best Practices Summary

- Prefer standalone components (default since Angular 19)
- Use signal-based inputs (`input()`) over `@Input()` decorator
- Use function-based outputs (`output()`) over `@Output()` decorator
- Use `computed()` for derived state, `linkedSignal()` for resettable derived state
- Never use `effect()` to propagate state — use `computed()` or `linkedSignal()`
- Use Signal Forms for new forms in Angular v21+
- Use `inject()` function over constructor injection
- Use `providedIn: 'root'` for singleton services
- Use functional route guards and resolvers
- Use the Angular CLI (`ng generate`) for scaffolding
- Do not add the `Component` suffix to component class names unless the project convention requires it
- Always use `track` in `@for` loops
- Use `@if`, `@for`, `@switch` control flow (not `*ngIf`, `*ngFor`, `*ngSwitch`)

## MCP Server Tools

The Angular CLI MCP server provides these tools:

| Tool                        | Description                                                                                  |
| --------------------------- | -------------------------------------------------------------------------------------------- |
| `get_best_practices`        | Retrieves the Angular Best Practices Guide, version-specific when workspace path is provided |
| `search_documentation`      | Searches official Angular documentation at angular.dev                                       |
| `find_examples`             | Finds best-practice code examples for modern Angular features                                |
| `list_projects`             | Lists all projects in the workspace with metadata (version, test framework, style language)  |
| `onpush_zoneless_migration` | Analyzes code and provides step-by-step migration plan to OnPush change detection            |
| `ai_tutor`                  | Interactive AI-powered Angular tutor                                                         |

## Troubleshooting

### MCP Server Connection Issues

**Problem:** Angular MCP server won't start
**Solutions:**

1. Verify Angular CLI is installed: `npx @angular/cli version`
2. Ensure you're in an Angular workspace (has `angular.json`)
3. Restart Kiro and try again

### Common Build Errors

**Error:** `NG8001: 'component-selector' is not a known element`
**Cause:** Component not imported
**Solution:** Add the component to the `imports` array of the consuming component

**Error:** `ExpressionChangedAfterItHasBeenChecked`
**Cause:** State mutation inside `effect()` or lifecycle hooks
**Solution:** Use `computed()` or `linkedSignal()` for derived state instead of `effect()`

---

**Package:** `@angular/cli`
**MCP Server:** angular-cli
