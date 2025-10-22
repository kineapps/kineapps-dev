# Global KineApps Development Guidelines

This file provides global guidance to Claude Code across all KineApps projects.

## General Coding Standards

### Logging
- Always use appropriate logging frameworks (never Console.WriteLine or Debug.WriteLine for production code)
- Use structured logging with meaningful context
- Include appropriate log levels (Error, Warning, Info, Debug)

### Code Style
- **Brace style**: Always use braces `{}` even for single-line blocks; place braces on separate lines
- **Naming conventions**: Follow platform-specific standards (PascalCase for C# public members, camelCase for private fields)
- **Comments**: Only add comments that provide value and should be committed to git; avoid commenting obvious code
- **Member ordering**: private fields → constructor → public properties → protected members → private members

### Git Practices
- Write clear, descriptive commit messages
- Keep commits focused and atomic
- Never commit secrets, credentials, or sensitive data
- Review changes before committing

## Multi-Platform Development

### Cross-Platform Considerations
- Be aware of path separator differences (Windows: `\`, Mac/Linux: `/`)
- Use platform-agnostic APIs when available
- Test on both platforms when making cross-platform changes

### File Paths
- Use Path.Combine() or equivalent for constructing file paths
- Avoid hardcoding absolute paths
- Use environment variables for user-specific paths

## Code Quality

### Testing
- Write tests for new functionality
- Run existing tests before committing
- Follow existing test patterns in the project

### Performance
- Consider performance implications of changes
- Profile before optimizing
- Document performance-critical code

### Security
- Validate all input
- Use parameterized queries for databases
- Follow principle of least privilege
- Keep dependencies up to date

## Documentation

### Code Documentation
- Document public APIs
- Include XML documentation comments for C# public members
- Update README files when adding new features
- Keep documentation in sync with code

### Project Documentation
- Maintain project-specific CLAUDE.md files
- Document build and deployment processes
- Include setup instructions for new developers

## Issue Tracking and Planning

### GitHub Issues
- Create issues for bugs and feature requests
- Use labels appropriately
- Link commits to issues when applicable
- Close issues when work is complete

### Pull Requests
- Create descriptive PR titles and descriptions
- Reference related issues
- Request reviews when appropriate
- Address review feedback promptly

## Development Workflow

### Before Starting Work
1. Pull latest changes from main branch
2. Create feature branch if needed
3. Review related issues and documentation

### During Development
1. Follow project-specific CLAUDE.md guidelines
2. Write tests as you code
3. Commit frequently with meaningful messages

### Before Committing
1. Run tests
2. Build the project
3. Review your changes
4. Update documentation if needed

## KineApps-Specific Practices

### Project Structure
- Follow established project architecture patterns
- Place reusable components in shared libraries
- Keep platform-specific code isolated

### Dependencies
- Prefer well-maintained, widely-used packages
- Document why dependencies are needed
- Keep dependencies up to date
- Check licenses for compatibility

### Localization
- Support internationalization from the start
- Use resource files for user-facing strings
- Never hardcode user-facing text
- Follow project-specific translation workflows

## Communication

### With Users
- Be professional and concise
- Explain technical concepts clearly
- Provide examples when helpful
- Ask clarifying questions when requirements are unclear

### Code Comments
- Explain "why" not "what"
- Document non-obvious behavior
- Add TODO comments for future work (with issue numbers if applicable)
- Remove outdated comments

## Continuous Learning

- Stay updated on platform changes
- Learn from code reviews
- Document lessons learned
- Share knowledge with the team
