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
- **Write tests as you develop, not after** - Use TDD approach: write failing test first, then implement
- **Run tests immediately** - Don't just verify compilation; actually execute tests to catch runtime issues
- Target 100% test coverage for critical business logic
- Run existing tests before committing
- Follow existing test patterns in the project

#### Unit Tests (All Languages)
- For unit test methods, prefer structure GIVEN / WHEN / THEN and add those titles as comments
- Prefer prefixing test name with the method being tested (e.g., `test('.foo - should add a new item')`)
- Group tests clearly and use clear names for groups/tests
- DO NOT refer to current context or task with "BUG REPRODUCTION", "bug fix test" or similar in unit test names/descriptions, but use clear names telling what is tested

#### Design for Testability
- **Avoid static dependencies** - Don't hardcode dependencies on file system, databases, platform APIs (like `ApplicationData.Current` in WinUI)
- **Use dependency injection** - Accept dependencies via constructor parameters or properties
- **Provide test constructors** - Add `internal Constructor(dependencies)` for test-specific initialization
- **Pattern for platform dependencies**:
  ```csharp
  // Production: parameterless constructor uses platform API
  private MyClass() { } // Uses ApplicationData.Current internally

  // Testing: internal constructor accepts test dependencies
  internal MyClass(string filePath) { customFilePath = filePath; }

  // Implementation uses custom dependency if provided, otherwise platform API
  string path = customFilePath ?? ApplicationData.Current.LocalFolder.Path;
  ```
- **Make internal classes testable** - Use `<InternalsVisibleTo Include="MyProject.Tests" />` in .csproj
- **Design classes for mockability (C#/.NET with Moq)**:
  - Make methods `virtual` if they need to be mocked
  - Make constructors `protected` (not `private`) to allow Moq proxy creation
  - Example:
    ```csharp
    public class AccountManager
    {
        protected AccountManager() { } // Protected allows Moq to create proxy

        public virtual async Task<CalendarAccount> GetAccountAsync(string accountId) // Virtual allows mocking
        {
            // Implementation
        }
    }
    ```
- **Use factory pattern for singletons with DI**:
  ```csharp
  public class AccountOperations
  {
      private readonly Func<Task<AccountManager>> accountManagerFactory;

      // Production: uses singleton
      public AccountOperations()
          : this(() => AccountManager.Instance) { }

      // Testing: inject mock factory
      internal AccountOperations(Func<Task<AccountManager>> factory)
      {
          accountManagerFactory = factory;
      }
  }
  ```

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
- **Thread-safety in async code**: Use `AsyncLock` from Nito.AsyncEx instead of `lock` keyword when protecting async operations
  - `lock` cannot be used with `await` - will cause compiler error CS1996
  - Use `using (await asyncLock.LockAsync().ConfigureAwait(false)) { ... }` pattern
  - Example: `private readonly AsyncLock myLock = new AsyncLock();`
- Implement IDisposable correctly for resource management
- Use dependency injection following project patterns
- Never use `Console.WriteLine` or `Debug.WriteLine` for production logging
- Run tests before committing changes

#### WinUI/UWP Specific Testing
- **Platform APIs are NOT available in tests** - `ApplicationData.Current`, `CoreWindow.Current`, etc. don't work in MSTest even with WinUI test projects
- **Design for testability from the start** - Any class using platform APIs needs internal constructor for testing
- **Use MSTest for WinUI-dependent tests** - Test projects need WinUI app infrastructure (UnitTestApp.xaml, manifest, etc.)
- **Use xUnit for utility classes** - Faster, simpler for non-WinUI code
- **Pattern**: Provide internal constructor that accepts file paths, connections, or other testable dependencies instead of platform APIs

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

#### Handling Review Comments
When fixing automated code review comments (from Copilot, Claude bot, etc.):

1. **Mark fixed comments as resolved**: After fixing an issue, mark the corresponding review thread as "Resolved" in GitHub so the user can track progress
2. **Add replies directly to each unresolved comment thread**: For issues that cannot be fixed or are deferred, add a reply directly below each specific review comment explaining why
   - **IMPORTANT**: Reply to each individual comment thread, NOT as a general PR comment
   - Example replies: "This is by design because..." or "Deferred to issue #123"
   - DO NOT mark these as resolved - leave them open for visibility
   - This keeps context with the specific code and makes it easy to see which issues have been addressed

**Using GitHub MCP to resolve review threads**:
```
# Get review comments and thread IDs
mcp__github__pull_request_read(
  method: "get_review_comments",
  owner: "...",
  repo: "...",
  pullNumber: ...
)

# Resolve a specific thread using GraphQL
# Note: Use the pullRequestReviewThreadId from the comment, not the comment ID
gh api graphql -f query='
  mutation {
    resolveReviewThread(input: {threadId: "PRRT_..."}) {
      thread {
        isResolved
      }
    }
  }
'
```

### GitHub Issues and Priority Tags
When working with GitHub issues, use the following priority tags for hierarchical prioritization (lower number = higher priority):

- **P0**: Emergency. True emergencies only.
- **P1**: Critical/Urgent. Must be addressed immediately.
- **P2**: High. Essential for the current sprint or near-term roadmap.
- **P3**: Medium. Important, but can be deferred if necessary.
- **P4**: Low. Backlog or future consideration.
- **P5**: Low. Backlog or future consideration.

When creating or updating issues, apply appropriate priority tags based on urgency and impact.

### GitHub Integration

**CRITICAL: Always prefer GitHub MCP tools over `gh` CLI commands** for all GitHub operations (PRs, issues, commits, files, searching, etc.). The MCP provides structured, reliable access to GitHub APIs.

Only use `gh` CLI when:
- Opening PR in browser: `gh pr view {pr_number} --web`
- The MCP doesn't support the specific operation needed

