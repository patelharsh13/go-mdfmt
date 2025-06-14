# go-mdfmt

A fast, reliable, and opinionated Markdown formatter written in Go. It provides a consistent, pluggable way to reformat `.md` files across projects — making your documentation readable, lintable, and style-consistent.

## Why go-mdfmt?

- **Consistency**: Markdown is widely used but rarely standardized across teams
- **Readability**: Many developers struggle with inconsistent formatting in `.md` files
- **Automation**: Provides a single-command solution to format Markdown like `gofmt` does for Go code
- **CI/CD Ready**: Perfect for automated formatting checks and enforcement

## Features

- **Text Reflow**: Wrap long paragraphs at configurable line width (80/100/120 chars)
- **Heading Normalization**: Ensure consistent heading levels and spacing
- **List Formatting**: Standardize bullet and numbered list styles
- **Code Block Fixes**: Auto-correct indentation and language specification
- **Inline Formatting**: Consistent inline code, links, and emphasis
- **Whitespace Cleanup**: Remove excessive empty lines and trailing spaces
- **CLI Interface**: Full-featured command-line tool with multiple modes
- **Diff Support**: Show changes before applying or run in check-only mode
- **CI Integration**: Built for continuous integration and pre-commit hooks

## Installation

### From Source
```bash
go install github.com/Gosayram/go-mdfmt/cmd/mdfmt@latest
```

### From Releases
Download the latest binary from the [releases page](https://github.com/Gosayram/go-mdfmt/releases).

### Using Go Module
```bash
git clone https://github.com/Gosayram/go-mdfmt.git
cd go-mdfmt
go build -o mdfmt cmd/mdfmt/main.go
```

## Quick Start

### Format Files
```bash
# Format and display to stdout
mdfmt README.md

# Format multiple files
mdfmt docs/*.md

# Format all markdown files in directory
mdfmt docs/
```

### Write Changes
```bash
# Format and write changes back to files
mdfmt --write README.md docs/*.md

# Format all markdown files in project
find . -name "*.md" -exec mdfmt --write {} +
```

### Check Mode (Perfect for CI)
```bash
# Check if files are properly formatted (exit code 1 if not)
mdfmt --check docs/ README.md

# Show what would change without writing
mdfmt --diff docs/ README.md
```

## Command Line Options

```
Usage: mdfmt [options] [files...]

Options:
  -w, --write           Write formatted content back to files
  -d, --diff            Show diff of changes without writing
  -c, --check           Check if files are formatted (exit 1 if not)
  -r, --recursive       Process directories recursively
      --line-width      Maximum line width for text reflow (default: 80)
      --config          Path to configuration file
      --ignore          Patterns to ignore (glob format)
  -v, --verbose         Verbose output
      --version         Show version information
  -h, --help            Show this help message

Examples:
  mdfmt README.md                    Format README.md to stdout
  mdfmt --write docs/               Format all .md files in docs/
  mdfmt --check --diff *.md         Check formatting and show diffs
  mdfmt --line-width 100 --write .  Format with 100-char line width
```

## Configuration

Create a `.mdfmt.yaml` file in your project root:

```yaml
# Line width for paragraph reflow
line_width: 80

# Heading configuration
heading:
  style: "atx"              # atx (#) or setext (===)
  normalize_levels: true    # Fix heading level jumps

# List formatting
list:
  bullet_style: "-"         # -, *, or +
  number_style: "."         # . or )
  consistent_indentation: true

# Code block formatting
code:
  fence_style: "```"        # ``` or ~~~
  language_detection: true  # Auto-detect and add language labels

# Whitespace handling
whitespace:
  max_blank_lines: 2        # Maximum consecutive blank lines
  trim_trailing_spaces: true
  ensure_final_newline: true

# File processing
files:
  extensions: [".md", ".markdown", ".mdown"]
  ignore_patterns: ["node_modules/**", ".git/**"]
```

## Integration Examples

### GitHub Actions
```yaml
name: Markdown Format Check
on: [push, pull_request]

jobs:
  markdown-format:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v3
        with:
          go-version: '1.21'
      - name: Install mdfmt
        run: go install github.com/Gosayram/go-mdfmt/cmd/mdfmt@latest
      - name: Check markdown formatting
        run: mdfmt --check --diff .
```

### Pre-commit Hook
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Check if any markdown files are staged
markdown_files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.md$')

if [ -n "$markdown_files" ]; then
    echo "Checking markdown formatting..."
    if ! mdfmt --check --diff $markdown_files; then
        echo "Markdown files are not properly formatted. Please run:"
        echo "  mdfmt --write $markdown_files"
        exit 1
    fi
fi
```

### Makefile Integration
```makefile
.PHONY: fmt-md check-md-fmt

fmt-md:
	mdfmt --write .

check-md-fmt:
	mdfmt --check --diff .

# Include in your main format target
fmt: fmt-go fmt-md

# Include in your main check target  
check: check-go check-md-fmt
```

## Before and After Examples

### Paragraph Reflow
**Before:**
```markdown
This is a very long paragraph that extends way beyond the reasonable line width and makes it difficult to read in editors or when reviewing diffs in pull requests.
```

**After:**
```markdown
This is a very long paragraph that extends way beyond the reasonable line
width and makes it difficult to read in editors or when reviewing diffs in
pull requests.
```

### List Consistency
**Before:**
```markdown
* Item one
- Item two  
  + Nested item
    * Deep nested
```

**After:**
```markdown
- Item one
- Item two
  - Nested item
    - Deep nested
```

### Heading Normalization
**Before:**
```markdown
# Title

### Skipped H2

##### Skipped H3 and H4
```

**After:**
```markdown
# Title

## Skipped H2

### Skipped H3 and H4
```

## Development

### Building
```bash
git clone https://github.com/Gosayram/go-mdfmt.git
cd go-mdfmt
go build -o mdfmt cmd/mdfmt/main.go
```

### Running Tests
```bash
go test ./...
go test -race ./...
go test -bench=. ./...
```

### Project Structure
```
go-mdfmt/
├── cmd/mdfmt/           # CLI application
├── pkg/
│   ├── parser/          # Markdown parsing
│   ├── formatter/       # Formatting rules
│   ├── renderer/        # Output generation
│   ├── config/          # Configuration management
│   └── processor/       # File processing
├── testdata/            # Test fixtures
├── docs/                # Documentation
└── examples/            # Usage examples
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass (`go test ./...`)
6. Format your code (`go fmt ./...` and `mdfmt --write .`)
7. Commit your changes (`git commit -am 'Add amazing feature'`)
8. Push to the branch (`git push origin feature/amazing-feature`)
9. Open a Pull Request

## Roadmap

- [x] Core formatting engine
- [x] CLI interface with all major flags
- [x] Configuration file support
- [x] CI/CD integration examples
- [ ] Plugin architecture for custom formatters
- [ ] Language Server Protocol (LSP) support
- [ ] Web interface for online formatting
- [ ] Performance optimizations for large files
- [ ] Additional Markdown dialect support

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Inspired by `gofmt` and the Go community's commitment to consistent formatting
- Built on top of excellent Go Markdown parsing libraries
- Thanks to all contributors and early adopters

## Support

- 📚 [Documentation](docs/)
- 🐛 [Issue Tracker](https://github.com/Gosayram/go-mdfmt/issues)
- 💬 [Discussions](https://github.com/Gosayram/go-mdfmt/discussions)
- 📧 [Email Support](mailto:abdurakhman.rakhmankulov@gmail.com) 