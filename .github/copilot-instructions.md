# Copilot Agent Instructions: Copilot-Agent-Vibing

## Repository Overview

**Vibing** - Modern Angular 21 ticket management SPA with drag-and-drop Kanban board. Persists data locally via IndexedDB (Dexie.js). Auto-deploys to GitHub Pages.

**Stack**: Angular 21 (standalone components, signals), Angular CDK, TypeScript
**Runtime**: Node.js 20.x, npm 10.x
**Size**: 14 source files

## Build & Development

### Setup
```bash
npm install          # Local development
npm ci              # CI/CD only - REQUIRED in GitHub Actions
```

### Commands
```bash
npm start           # Dev server at http://localhost:4200/ (~4-6s startup)
npm run build       # Production build → dist/vibing-app/browser/ (~6s)
npm run build -- --base-href=/Copilot-Agent-Vibing/  # REQUIRED for GitHub Pages
npm run watch       # Continuous rebuild on file changes
npm test            # Vitest tests (~2-3s, IndexedDB warning is EXPECTED)
```

**Critical**: Always use `--base-href=/Copilot-Agent-Vibing/` for GitHub Pages builds.

### Build Output
- Location: `dist/vibing-app/browser/`
- Size: ~419 KB (112 KB gzipped)
- Time: ~6 seconds production, ~3-4s development

### Code Formatting
- Prettier (package.json): 100 char width, single quotes, Angular HTML parser
- EditorConfig: 2-space indent, UTF-8, LF line endings
- **No lint command** - follow EditorConfig/Prettier config

## Project Architecture

### Structure
```
src/app/
  components/ticket-board/    # Main board component (TS, HTML, CSS)
  models/ticket.model.ts      # Data interfaces
  services/database.service.ts # Dexie.js IndexedDB wrapper
  app.ts, app.config.ts       # Root component & config
src/main.ts, index.html
.github/workflows/deploy.yml  # CI/CD
```

### Key Configs
- `angular.json`: Build/serve/test config
- `tsconfig.json`: Strict TypeScript, ES2022, experimental decorators
- `.gitignore`: Excludes dist/, node_modules/, .angular/cache

### Architecture Principles
- **Standalone components**: No NgModules (standalone: true required)
- **Signals**: Reactive state (lists, ticketsByList)
- **Dependency injection**: Services provided in 'root'
- **Dexie.js DB**: VibingDB v1, stores: tickets (++id, title, listId, order), ticketLists (id, name, order)

## CI/CD & Deployment

### GitHub Actions (.github/workflows/deploy.yml)
**Trigger**: Push to main or manual workflow_dispatch
**Steps**:
1. Checkout, Setup Node 20 with npm cache
2. `npm ci` (NEVER npm install in CI)
3. `npm run build -- --base-href=/Copilot-Agent-Vibing/` (base-href REQUIRED)
4. Upload `./dist/vibing-app/browser` to GitHub Pages
5. Deploy → https://borisp-qm.github.io/Copilot-Agent-Vibing/

## Development Workflow

1. Run `npm install` (first time or after package.json changes)
2. Make changes in `src/`
3. Build to verify: `npm run build`
4. Test: `npm test` (IndexedDB warning EXPECTED, exit code 0 = success)
5. Manual test: `npm start` → http://localhost:4200/
6. Commit (exclude dist/, node_modules/, .angular/)

### Common Tasks
- **New component**: Create in `src/app/components/`, set `standalone: true`, import in parent
- **Database schema**: Edit DatabaseService, increment version: `this.version(2).stores({...})`
- **Styling**: Global→`src/styles.css`, Component→use styleUrl property

## Troubleshooting

**Build**: Module not found→re-run `npm install`; TypeScript errors→check strict settings
**Tests**: IndexedDB error is EXPECTED; exit code 0 = pass; won't run→check `npm list vitest`
**Dev server**: Port in use→`ng serve --port 4201`; hot reload→increase fs.inotify.max_user_watches (Linux)
**Deploy**: 404→verify --base-href flag; routing broken→check base tag matches base-href

## Critical Reminders

1. Trust these instructions - only explore if incomplete/incorrect
2. Node 20.x + npm 10.x (CI uses specific versions from package.json)
3. Build before committing (catch TS errors early)
4. Clean build: remove dist/ and .angular/cache/
5. IndexedDB test error is EXPECTED
6. Use npm only (not yarn/pnpm)
7. Standalone architecture (no NgModules)
8. Prefer signals over RxJS
9. Build time: ~6s production, ~3-4s dev
10. Test time: ~2-3s
