## AI Agent Instructions for vscode-markdownlint
This is a Visual Studio Code extension that surfaces markdownlint diagnostics and quick fixes in the editor. It bundles for desktop and web and uses markdownlint-cli2 for workspace linting and CI.
### Architecture at a glance
- Activation: onLanguage:markdown. Entry is ESM (`extension.mjs`); `main` points to the bundled `bundle.js`, `browser` to `bundle.web.js`.
- Linting engine: markdownlint (via markdownlint-cli2). Rules come from upstream markdownlint. Project disables MD013 (line length) by default; others follow upstream unless configured.
- Configuration sources: `.markdownlint.{json,jsonc,yaml,yml,cjs}` and `.markdownlint-cli2.{jsonc,yaml,cjs}`. The command `markdownlint.openConfigFile` creates/opens workspace config.
- Commands: `markdownlint.fixAll`, `markdownlint.lintWorkspace`, `markdownlint.openConfigFile`, `markdownlint.toggleLinting`.
### Build and scripts
- Install: npm install (repo uses no lockfile in CI).
- Lint: `npm run lint` runs ESLint (flat config in `eslint.config.mjs`) and markdownlint-cli2 for repo docs.
- Compile: `npm run compile` (webpack prod) or `npm run compile-debug` (no minification). Also run `npm run schema` to copy JSON schemas to the root for validation contributions.
- Test (node): `npm test` runs Node tests with coverage, then lint, compile, schema, and enforces a clean git diff.
- Test (UI - desktop): `npm run test-ui` launches VS Code Extension Host via `@vscode/test-electron` and runs tests from `test-ui/index.cjs`/`tests.cjs`.
- Test (web): `npm run test-web` installs `@vscode/test-web` ad-hoc and runs headless web tests.
- Upgrade deps: `npm run upgrade` using npm-check-updates.
### Testing notes (what to expect)
- UI tests are CommonJS by design (VS Code test harness requires it). Files: `test-ui/index.cjs` (runner) and `test-ui/tests.cjs` (tests). Do not convert them to ESM.
- Tests open workspace files like `README.md`/`CHANGELOG.md`, intentionally introduce violations, verify diagnostics and code actions, then clean up.
- Timeouts and cleanup: Tests wrap operations with a 10s timeout and dispose subscriptions; avoid adding long-running operations without bumping timeouts.
- Web tests rely on the web bundle; avoid Node-only APIs in code paths executed in the web worker (see bundling constraints below).
### Debugging
- Use `.vscode/launch.json`:
  - Launch Extension (desktop): builds and runs the extension in the Extension Host.
  - Launch Web Extension (web): serves the web bundle and launches in a Web Extension Host.
### Bundling and web constraints
- Bundles: `webpack.config.js` outputs `bundle.js` (target `node`, libraryTarget `commonjs2`) and `bundle.web.js` (target `webworker`, libraryTarget `commonjs`).
- Externals: `vscode` is external; never require/import it in plain Node tests without the VS Code test harness.
- Module shims/replacements: browser build replaces certain Node modules (e.g., `path` → path-browserify, `stream` → stream-browserify) and stubs others (`fs` disabled). There are NormalModuleReplacementPlugins for markdown-it and some Node submodules. Keep runtime code web-safe.
### CI checks (GitHub Actions)
- CI workflow installs Node, runs `npm test` and `npm run test-ui` (with xvfb for UI on Linux), and validates schemas.
- Checkers workflow runs linkinator and spelling checks with a custom dictionary.
### Release and publishing (summary)
- Versioning: bump `version` in `package.json` and update `CHANGELOG.md` accordingly.
- Build: run compile and ensure tests/CI pass locally.
- Package/Publish: the repo uses `@vscode/vsce`. Use vsce to package and publish when ready (requires a publisher token). Tag the release in Git and push.
### Key files
- `extension.mjs`: activation logic, command registration, configuration precedence, diagnostics.
- `webpack.config.js`: dual-target bundling and browser shims.
- `eslint.config.mjs`: flat ESLint setup; tabs and double quotes stylistic rules.
- `test-ui/index.cjs`, `test-ui/tests.cjs`, `test-ui/run-tests.mjs`: UI test harness and cases.
- `.github/workflows/*`: CI and auxiliary checkers.
### Safe-change tips
- Respect ESM vs CJS boundaries: keep VS Code UI tests in CJS; extension code in ESM.
- Avoid Node-only modules or globals on the main execution path; ensure web worker compatibility.
- Don’t commit generated artifacts (`bundle.js`, `bundle.web.js`) or schema copies with changes; `npm test` enforces a clean diff.
- For autofix on save, users can enable: `"editor.codeActionsOnSave": { "source.fixAll.markdownlint": true }`.
### References
- README.md for usage, rules, and configuration examples.
- package.json for scripts, contributes, and validation wiring.
- markdownlint Custom Rules: https://github.com/DavidAnson/markdownlint/blob/main/doc/CustomRules.md
---
Keep this concise. When in doubt, follow README.md, package.json, and the existing test patterns.