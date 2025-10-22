# Global KineApps Development Guidelines

This file provides global guidance to Claude Code across all KineApps projects.

## General Coding Standards

### Code Quality
- Follow MVVM pattern best practices if project uses MVVM (UI code should contain only UI code and not business logic)
- Keep class/method etc. documentation up-to-date when making changes

### Logging
- Always use appropriate logging frameworks for production code
- Use structured logging with meaningful context
- Include appropriate log levels (Error, Warning, Info, Debug)

### Code Style
- **Brace style**: Always use braces `{}` even for single-line blocks; place braces on separate lines
- **Naming conventions**: Follow language-specific standards (see Language & Framework-Specific Guidelines)
- **Comments**: Only add comments that provide value and should be committed to git; avoid commenting obvious code
- **Member ordering**: Follow language-specific conventions (typically: private fields → constructor → public properties → protected members → private members)

## Localization
- Support internationalization from the start
- Use resource files for user-facing strings
- Never hardcode user-facing text
- Follow project-specific translation workflows
- If maximum length for a string is defined, count characters (not bytes)

### Dependencies
- Check licenses for compatibility

## Multi-Platform Development

- Be aware of path separator differences (Windows: `\`, Mac/Linux: `/`)
- Avoid hardcoding absolute paths; use environment variables for user-specific paths

## Code Quality

### Testing
- Write tests for new functionality
- Target 100% test coverage
- Run existing tests before committing
- Follow existing test patterns in the project

#### Unit Tests (All Languages)
- For unit test methods, prefer structure GIVEN / WHEN / THEN and add those titles as comments
- Prefer prefixing test name with the method being tested (e.g., `test('.foo - should add a new item')`)
- Group tests clearly and use clear names for groups/tests
- DO NOT refer to current context or task with "BUG REPRODUCTION", "bug fix test" or similar in unit test names/descriptions, but use clear names telling what is tested

### Performance
- Consider performance implications of changes
- Profile before optimizing
- Document performance-critical code

### Security
- Always encrypt sensitive data
- Validate all input
- Use parameterized queries for databases
- Follow principle of least privilege
- Keep dependencies up to date

## Documentation

### Code Documentation
- Document public APIs using language-appropriate documentation standards
- Update README files when adding new features
- Keep documentation in sync with code

### Code Comments
- Explain "why" not "what"
- Document non-obvious behavior
- Add TODO comments for future work (with issue numbers if applicable)
- Remove outdated comments

### Project Documentation
- Maintain project-specific CLAUDE.md files
- Document build and deployment processes
- Include setup instructions for new developers

## Communication

- Ask clarifying questions when requirements are unclear

## Language & Framework-Specific Guidelines

### Flutter / Dart Projects

- Use Dart MCP when possible
- Format code with `dart format`, but format only the files you create or modify. Do not format generated files like strings*.dart
- Follow common Dart style for documenting all public types and members
- Ensure no new lint errors are added
- Do not launch the app in debugger or use hot reload. I will test the app manually

### C# Projects

- Use modern C# features appropriately (pattern matching, records, nullable reference types, etc.)
- Follow C# naming conventions:
  - PascalCase for public members, methods, properties, and types
  - camelCase with underscore prefix (`_fieldName`) for private fields
  - UPPER_CASE for constants
- Use XML documentation comments (`///`) for all public types and members
- Prefer `var` for local variables when type is obvious
- Use async/await properly; avoid `.Result` or `.Wait()` which can cause deadlocks
- Implement IDisposable correctly for resource management
- Use dependency injection following project patterns
- Never use `Console.WriteLine` or `Debug.WriteLine` for production logging
- Run tests before committing changes

### TypeScript Projects

- Use strict TypeScript configuration; avoid `any` type unless absolutely necessary
- Follow naming conventions:
  - camelCase for variables, functions, and methods
  - PascalCase for classes, interfaces, types, and enums
  - UPPER_CASE for constants
- Prefer interfaces over type aliases for object shapes
- Use modern ES6+ features (arrow functions, destructuring, async/await, etc.)
- Document public APIs using JSDoc comments
- Use `const` by default, `let` when reassignment is needed, never `var`
- Handle errors properly; use try/catch for async operations
- Run linter and type checker before committing
- Ensure no new ESLint warnings or TypeScript errors are introduced

## GitHub Workflow

### General
- Never commit secrets, credentials, or sensitive data

### Branch and Pull Request Management
- Create always a branch and PR when working on issues, features etc.
- When creating a branch, include issue number as prefix
- When asked to fix review comments in a PR, ignore comments marked as RESOLVED
- When merging PR, delete the branch (local and remote)

### GitHub Issues and Priority Tags
When working with GitHub issues, use the following priority tags for hierarchical prioritization (lower number = higher priority):

- **P0**: Emergency. True emergencies only.
- **P1**: Critical/Urgent. Must be addressed immediately.
- **P2**: High. Essential for the current sprint or near-term roadmap.
- **P3**: Medium. Important, but can be deferred if necessary.
- **P4**: Low. Backlog or future consideration.
- **P5**: Low. Backlog or future consideration.

When creating or updating issues, apply appropriate priority tags based on urgency and impact.

### GitHub CLI (`gh`) Usage

#### Viewing Pull Request Review Comments

**CRITICAL: When asked to check review comments after a commit, you MUST use the method below to see ALL comments reliably.**

##### The ONLY Reliable Method to View ALL PR Comments

Use `gh pr view --comments` which shows ALL comments (inline code reviews, general comments, from all reviewers):

```bash
# View ALL PR comments including latest reviews
gh pr view {pr_number} --comments

# To see just recent comments, use tail
gh pr view {pr_number} --comments | tail -200

# To see comments from a specific reviewer
gh pr view {pr_number} --comments | grep -A 100 "author:.*claude"
gh pr view {pr_number} --comments | grep -A 100 "author:.*copilot"
```

**Why this is the ONLY reliable method:**
- `gh api` endpoints with timestamps often miss comments due to timezone/caching issues
- `gh pr view --json comments` doesn't show inline code review comments
- `gh api /repos/.../pulls/{pr}/comments` only shows inline comments, not general comments
- `gh pr view --comments` shows EVERYTHING in human-readable format

##### Getting Inline Review Comments (Alternative Method)

If you specifically need just inline code review comments in JSON format:

```bash
# Get ALL inline review comments (from all commits)
gh api /repos/{owner}/{repo}/pulls/{pr}/comments

# The output is JSON with each comment containing:
# - path: file path
# - line/position: line number
# - body: comment text with suggestions
# - diff_hunk: code context
# - commit_id: which commit the comment is on
```

**Important**: This API endpoint shows ALL inline comments regardless of commit, so you'll always see them even if they're on an older commit.

##### Other Useful GitHub CLI Commands

```bash
# View PR details
gh pr view {pr_number}

# View PR in browser
gh pr view {pr_number} --web

# Check PR status and reviews
gh pr view {pr_number} --json state,reviewDecision,reviews

# List all PR comments (general comments, not inline code reviews)
gh pr view {pr_number} --comments
```

