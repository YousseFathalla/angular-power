# Angular Power for Kiro

A [Kiro Power](https://kiro.dev/docs/powers/) that gives your AI agent comprehensive Angular development guidance combined with the official Angular CLI MCP server.

Built on the **official Angular CLI MCP server** and **Angular's official developer skills**, aligned with **Angular Latest Version** best practices.

## What it does

When you mention anything Angular-related in Kiro, this power activates automatically and gives the agent:

- **Live MCP tools** — search angular.dev docs, find modern code examples, list workspace projects, run OnPush/zoneless migrations
- **Best practices** — version-aware guidance aligned with Angular 21 standards
- **On-demand steering** — topic-specific guides loaded only when relevant (components, signals, forms, routing, DI, testing, styling, accessibility, CLI)

## Topics covered

| Steering File             | Topics                                                     |
| ------------------------- | ---------------------------------------------------------- |
| `components.md`           | Components, inputs, outputs, host elements, control flow   |
| `reactivity.md`           | Signals, computed, linkedSignal, resource, effects         |
| `forms.md`                | Signal forms (v21+), reactive forms, template-driven forms |
| `dependency-injection.md` | Services, providers, hierarchical injectors                |
| `routing.md`              | Routes, guards, resolvers, lazy loading, SSR               |
| `testing.md`              | Unit tests, component harnesses, router testing, E2E       |
| `styling.md`              | Component styles, Tailwind v4, animations                  |
| `accessibility.md`        | Angular Aria (Accordion, Listbox, Tabs, Menu, Tree, Grid)  |
| `tooling.md`              | Angular CLI commands, MCP server configuration             |

## MCP Tools

| Tool                        | Description                                    |
| --------------------------- | ---------------------------------------------- |
| `get_best_practices`        | Version-specific Angular best practices guide  |
| `search_documentation`      | Search official docs at angular.dev            |
| `find_examples`             | Modern Angular code examples                   |
| `list_projects`             | Workspace project metadata from `angular.json` |
| `onpush_zoneless_migration` | Step-by-step OnPush/zoneless migration plan    |
| `ai_tutor`                  | Interactive Angular AI tutor                   |

## Installation

### From GitHub (recommended)

1. Open Kiro → Powers panel
2. Click **Add Custom Power**
3. Select **GitHub Repository**
4. Enter this repo URL and set the path to `powers/angular`

### From local directory

1. Clone this repo
2. Open Kiro → Powers panel
3. Click **Add Custom Power** → **Local Directory**
4. Point to the `powers/angular` folder

## Structure

```text
powers/angular/
├── POWER.md              # Main docs, onboarding, best practices
├── mcp.json              # Angular CLI MCP server config
└── steering/
    ├── components.md
    ├── reactivity.md
    ├── forms.md
    ├── dependency-injection.md
    ├── routing.md
    ├── testing.md
    ├── styling.md
    ├── accessibility.md
    └── tooling.md
```

## Built on

- [Angular CLI MCP Server](https://angular.dev/tools/mcp) — official Angular MCP server by the Angular team
- [Angular Developer Skills](https://github.com/angular/skills) — official Angular coding guidelines
- [Angular 21](https://angular.dev) — latest Angular best practices and APIs

## Author

Youssef Fathalla

## License

MIT
