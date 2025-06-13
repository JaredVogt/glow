# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Glow is a terminal-based markdown reader that renders markdown files with style in the CLI. It supports:
- Reading from local files, URLs, stdin, GitHub/GitLab repositories
- Interactive TUI mode for browsing and searching
- Direct CLI output mode
- Mouse support and customizable keybindings
- Glamour-based markdown rendering with syntax highlighting

## Tech Stack

- **Language**: Go 1.23.6+
- **CLI Framework**: Cobra
- **TUI Framework**: Bubble Tea (github.com/charmbracelet/bubbletea)
- **Markdown Rendering**: Glamour (github.com/charmbracelet/glamour)
- **Configuration**: Viper
- **HTTP Client**: Custom with go-readability for web content extraction

## Common Commands

```bash
# Build
go build

# Run tests
task test
# or directly:
go test ./...

# Lint code
task lint
# or directly:
golangci-lint run --config .golangci-soft.yml

# Debug logging
GLOW_LOGFILE=debug.log glow [command]
task log  # tail the debug log

# Run glow
./glow [file.md]                    # render markdown file
./glow github.com/owner/repo        # browse GitHub repo
./glow -p                           # page through multiple files
./glow -s dark                      # use dark style
```

## Architecture

### Directory Structure
- `main.go` - Entry point, initializes Cobra root command
- `ui/` - Bubble Tea TUI components
  - `ui.go` - Main TUI model and update logic
  - `stash.go` - Document management and stash functionality
  - `pager.go` - Markdown viewing pager
- `utils/` - Utility functions for URL detection, GitHub/GitLab parsing
- `styles.json` - Built-in style definitions

### Key Components

1. **CLI Layer** (`main.go`)
   - Uses Cobra for command parsing
   - Handles both TUI and non-TUI modes
   - Manages configuration via Viper

2. **TUI Layer** (`ui/`)
   - Built on Bubble Tea framework
   - State machine pattern with different views (stash, pager)
   - Handles keyboard/mouse input
   - Document filtering and search

3. **Rendering Pipeline**
   - Glamour processes markdown with configurable styles
   - Supports ANSI color output
   - Handles terminal width constraints
   - Code syntax highlighting via Chroma

### Configuration
- Uses Viper for config management
- Config file locations: `~/.config/glow/glow.yml` or `~/.glow.yml`
- Environment variables: `GLOW_*`
- Command-line flags override config values

## Development Guidelines

### Testing
- Use `task test` to run all tests
- Tests are located alongside source files (*_test.go)
- Focus on testing public APIs and complex logic

### Code Style
- Follow standard Go conventions
- Use golangci-lint configuration (.golangci-soft.yml)
- Keep functions focused and testable
- Proper error handling with wrapped errors

### Common Patterns
- Use Bubble Tea's Cmd pattern for async operations
- Glamour's RenderWithEnvironmentConfig for terminal-aware rendering
- URL detection via regex in utils package
- GitHub/GitLab content fetching through raw endpoints

## Debugging

Enable debug logging:
```bash
GLOW_LOGFILE=debug.log glow [command]
tail -f debug.log  # or use: task log
```

Debug logs include:
- TUI state transitions
- Document loading/parsing
- Configuration values
- Error traces

## Common Issues

1. **Rendering Issues**: Check terminal capabilities and TERM environment variable
2. **GitHub Rate Limits**: Use GITHUB_TOKEN for authenticated requests
3. **Style Problems**: Verify style name exists in styles.json or custom style file
4. **Performance**: Large documents may need paging mode (-p flag)