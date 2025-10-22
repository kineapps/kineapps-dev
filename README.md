# KineApps Development Configuration

This repository contains global development configuration files shared across all KineApps projects.

## Contents

- **CLAUDE.md** - Global guidelines for Claude Code across all KineApps projects
- Instructions for setting up symbolic links on Windows and Mac

## Setup Instructions

### Windows Setup

1. Clone this repository:
   ```powershell
   git clone https://github.com/kineapps/kineapps-dev.git C:\dev\kineapps-dev
   ```

2. Create the `.claude` directory if it doesn't exist:
   ```powershell
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude"
   ```

3. Create a symbolic link to the global CLAUDE.md:
   ```powershell
   New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.claude\CLAUDE.md" -Target "C:\dev\kineapps-dev\CLAUDE.md"
   ```

### Mac Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/kineapps/kineapps-dev.git ~/dev/kineapps-dev
   ```

2. Create the `.claude` directory if it doesn't exist:
   ```bash
   mkdir -p ~/.claude
   ```

3. Create a symbolic link to the global CLAUDE.md:
   ```bash
   ln -s ~/dev/kineapps-dev/CLAUDE.md ~/.claude/CLAUDE.md
   ```

## Verifying Setup

After setup, verify the symbolic link works:

**Windows:**
```powershell
Get-Content "$env:USERPROFILE\.claude\CLAUDE.md"
```

**Mac:**
```bash
cat ~/.claude/CLAUDE.md
```

You should see the contents of the global CLAUDE.md file.

## Usage

Once set up, Claude Code will automatically use these global guidelines for all KineApps projects, unless overridden by project-specific CLAUDE.md files.

### Priority Order

Claude Code reads configuration in this order:
1. Project-specific `.claude/CLAUDE.md` (highest priority)
2. Project root `CLAUDE.md`
3. Global `~/.claude/CLAUDE.md` (this file, lowest priority)

This means project-specific guidelines override global ones.

## Updating Guidelines

To update the global guidelines:

1. Make changes to CLAUDE.md in this repository
2. Commit and push changes
3. Pull updates on all machines:
   ```bash
   cd ~/dev/kineapps-dev  # or C:\dev\kineapps-dev on Windows
   git pull
   ```

The symbolic links will automatically reflect the updates.

## Contributing

When adding new guidelines:
- Keep them general and applicable across multiple projects
- Place project-specific rules in individual project CLAUDE.md files
- Update this README if adding new configuration files
- Test on both Windows and Mac if possible

## Troubleshooting

### Symbolic link not working (Windows)

If you get a permission error creating symbolic links on Windows:
1. Run PowerShell as Administrator
2. Or enable Developer Mode in Windows Settings → Update & Security → For Developers

### File not found after setup

Verify the clone location matches the symbolic link target path:
```powershell
# Windows
Get-Item "$env:USERPROFILE\.claude\CLAUDE.md" | Select-Object Target
```

```bash
# Mac
ls -la ~/.claude/CLAUDE.md
```
