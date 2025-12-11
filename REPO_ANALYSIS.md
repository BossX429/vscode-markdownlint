# Repository Analysis

This document summarizes notable aspects of the `vscode-markdownlint` extension repository.

## Purpose and scope
- Provides markdownlint linting and formatting for Visual Studio Code using the `markdownlint` and `markdownlint-cli2` engines.
- Exposes commands such as fixing all violations, linting the workspace, opening config files, and toggling linting while supporting markdown document activation events.

## Build, test, and tooling
- Node.js >=16 is required; the extension targets VS Code >=1.75.
- Key npm scripts:
  - `npm test` runs Node tests with coverage, then lints, compiles bundles, refreshes schemas, and asserts a clean git tree.
  - `npm run test-ui` executes UI tests via the `test-ui/run-tests.mjs` harness.
  - `npm run compile` builds production bundles with webpack; `compile-debug` produces an unminified build.
  - `npm run schema` copies the markdownlint and markdownlint-cli2 configuration schemas into the repo.
- Continuous integration convenience script `npm run ci` chains unit tests and UI tests.

## Extension configuration and contributions
- Declares commands (`markdownlint.fixAll`, `markdownlint.lintWorkspace`, `markdownlint.openConfigFile`, `markdownlint.toggleLinting`) with command palette entries for Markdown files.
- Supplies JSON/YAML validation for `.markdownlint` and `.markdownlint-cli2` config files and contributes a `markdownlint` task definition and problem matcher.
- Exposes settings such as `markdownlint.config`, `configFile`, `customRules`, `focusMode`, `lintWorkspaceGlobs`, and `run` to control linting behavior.
- Includes Markdown snippets and supports limited functionality in untrusted or virtual workspaces.

## Dependencies and tech stack
- Runtime dependency: `markdownlint-cli2` for linting execution.
- Development toolchain includes ESLint (with stylistic and Node/unicorn plugins), webpack/terser for bundling, VS Code testing utilities, and helper packages for browser compatibility (e.g., `path-browserify`, `stream-browserify`).

## Notable artifacts
- Bundled entry points are `bundle` (main) and `bundle.web` (browser); icons and README provide marketplace presentation.
- Schemas `markdownlint-config-schema.json` and `markdownlint-cli2-config-schema.json` are stored at the repo root for validation contributions.
