# Angular CLI & MCP Server

## Angular CLI

The Angular CLI (`ng`) is the primary tool for managing an Angular workspace. Always prefer CLI commands over manual file creation.

### Managing Dependencies

**ALWAYS use `ng add` for Angular libraries** instead of `npm install`:

```bash
ng add @angular/material
ng add tailwindcss
ng add @angular/fire
```

Update Angular:

```bash
ng update @angular/core @angular/cli
```

### Generating Code

| Target | Command | Notes |
|--------|---------|-------|
| Component | `ng g c path/to/name` | Use `-s` for inline style, `-t` for inline template |
| Service | `ng g s path/to/name` | Creates `providedIn: 'root'` service |
| Directive | `ng g d path/to/name` | |
| Pipe | `ng g p path/to/name` | |
| Guard | `ng g g path/to/name` | Functional guard |
| Environments | `ng g environments` | Scaffolds `src/environments/` |

No command for route definitions — generate a component, then add to `Routes` array manually.

### Development Server

```bash
ng serve
```

#### Backend API Proxying

1. Create `src/proxy.conf.json`:
   ```json
   { "/api/**": { "target": "http://localhost:3000", "secure": false } }
   ```
2. Update `angular.json` serve target:
   ```json
   "options": { "proxyConfig": "src/proxy.conf.json" }
   ```

### Building

```bash
ng build
```

Defaults to production (AOT, minification, tree-shaking). Use `--configuration=staging` for other configs.

### Testing

- Unit: `ng test`
- E2E: `ng e2e`

### Deployment

```bash
ng add @angular/fire  # Example for Firebase
ng deploy
```

---

## Angular CLI MCP Server

The Angular CLI includes an MCP server for AI assistants.

### Default Tools

| Tool | Description |
|------|-------------|
| `ai_tutor` | Interactive AI-powered Angular tutor |
| `find_examples` | Best-practice code examples for modern features |
| `get_best_practices` | Angular Best Practices Guide (version-specific) |
| `list_projects` | Lists workspace projects from `angular.json` |
| `onpush_zoneless_migration` | Migration plan for OnPush change detection |
| `search_documentation` | Searches angular.dev documentation |

### Experimental Tools

Enable with `--experimental-tool` (`-E`) flag:

| Tool | Description |
|------|-------------|
| `build` | One-off `ng build` |
| `devserver.start` | Start dev server |
| `devserver.stop` | Stop dev server |
| `devserver.wait_for_build` | Get latest build logs |
| `e2e` | Run E2E tests |
| `modernize` | Code migrations to latest patterns |
| `test` | Run unit tests |

### Configuration

```json
{
  "mcpServers": {
    "angular-cli": {
      "command": "npx",
      "args": ["-y", "@angular/cli", "mcp"]
    }
  }
}
```

### Command Options

- `--read-only`: Only non-modifying tools
- `--local-only`: Only offline tools
- `-E build`, `-E modernize`: Enable experimental tools

Example:
```json
"args": ["-y", "@angular/cli", "mcp", "--read-only", "-E", "build", "-E", "modernize"]
```
