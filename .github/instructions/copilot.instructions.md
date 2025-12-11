# Copilot Instructions for vscode-markdownlint

## Code Style

- Use **tabs** for indentation (not spaces)
- Use **double quotes** for strings
- Use **semicolons** at end of statements
- Use **1tbs** brace style (opening brace on same line)
- No trailing commas in arrays/objects
- Max line length: 130 characters

## Markdown

- Do NOT use links to `npmjs.com` - they return 403 errors in CI link checker
- Use GitHub links instead of npm search links
- Run `npx markdownlint-cli2 --fix "*.md"` to auto-fix markdown issues

## JavaScript/Node.js

- This is a VS Code extension using CommonJS modules
- Run `npx eslint --fix` to auto-fix code style issues
- Follow existing patterns in the codebase

## Before Committing

1. Run `npm test` to verify all tests pass
2. Run `npm run lint` to check for lint errors
3. Ensure no `npmjs.com` links are added to markdown files

## CI Workflows

- **Checkers**: Runs link checker and spell checker on markdown
- **CI**: Runs tests and UI tests
- **Auto Format**: Automatically fixes ESLint and markdownlint issues on push
- **CodeQL**: Security analysis

## File Patterns to Ignore

- `bundle.js` - Generated webpack output
- `bundle.web.js` - Generated webpack output for web
- `node_modules/` - Dependencies