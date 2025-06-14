# go-mdfmt: Architecture and Design Ideas

## Project Overview

go-mdfmt is a fast, reliable, and opinionated Markdown formatter written in Go. It provides a consistent, pluggable way to reformat .md files across projects — making your documentation readable, lintable, and style-consistent.

## Architecture Design

### Core Components

#### 1. Parser (`pkg/parser/`)
- **Responsibility**: Parse Markdown content into an Abstract Syntax Tree (AST)
- **Implementation**: Custom parser or wrapper around existing Go Markdown parser (e.g., goldmark)
- **Key Features**:
  - Preserve original formatting context for intelligent reformatting
  - Handle various Markdown dialects (CommonMark, GitHub Flavored Markdown)
  - Maintain source position information for error reporting

#### 2. Formatter (`pkg/formatter/`)
- **Responsibility**: Apply formatting rules to the parsed AST
- **Design Pattern**: Strategy pattern for different formatting rules
- **Components**:
  - `HeadingFormatter`: Normalize heading levels and spacing
  - `ParagraphFormatter`: Reflow text with configurable line width
  - `ListFormatter`: Ensure consistent bullet and numbering styles
  - `CodeBlockFormatter`: Fix indentation and language specification
  - `InlineFormatter`: Format inline code, links, emphasis
  - `WhitespaceFormatter`: Clean up excessive empty lines

#### 3. Renderer (`pkg/renderer/`)
- **Responsibility**: Convert formatted AST back to Markdown text
- **Features**:
  - Preserve user preferences (e.g., bullet style, emphasis style)
  - Maintain semantic equivalence with original content
  - Support different output formats (if needed)

#### 4. Configuration (`pkg/config/`)
- **Responsibility**: Manage formatting rules and preferences
- **Configuration Sources**:
  - Command-line flags
  - Configuration file (`.mdfmt.yaml` or `.mdfmt.json`)
  - Environment variables
  - Default values
- **Key Settings**:
  - Line width (default: 80)
  - Heading style preference
  - List bullet style
  - Code block language detection
  - Whitespace normalization rules

#### 5. CLI (`cmd/mdfmt/`)
- **Responsibility**: Command-line interface
- **Key Commands**:
  - `mdfmt [files...]` - Format files
  - `mdfmt --write [files...]` - Format and write in-place
  - `mdfmt --diff [files...]` - Show diff without writing
  - `mdfmt --check [files...]` - Check if files are formatted (CI mode)
  - `mdfmt --version` - Show version
  - `mdfmt --help` - Show help

#### 6. File Processor (`pkg/processor/`)
- **Responsibility**: Handle file operations and batch processing
- **Features**:
  - Recursive directory traversal
  - File filtering (by extension, patterns)
  - Concurrent processing for performance
  - Backup creation (if requested)
  - Git integration (format only tracked files)

### Design Principles

#### 1. Modularity
- Each component has a single responsibility
- Clear interfaces between components
- Easy to extend with new formatting rules
- Testable in isolation

#### 2. Performance
- Streaming processing for large files
- Concurrent file processing
- Minimal memory allocation
- Efficient AST manipulation

#### 3. Reliability
- Comprehensive error handling
- Graceful degradation on parse errors
- Content preservation (never lose data)
- Extensive testing coverage

#### 4. Extensibility
- Plugin architecture for custom formatters
- Configuration-driven behavior
- Support for custom Markdown extensions
- API for integration with other tools

## Configuration Schema

```yaml
# .mdfmt.yaml
line_width: 80
heading:
  style: "atx"  # atx (#) or setext (===)
  normalize_levels: true
list:
  bullet_style: "-"  # -, *, +
  number_style: "."  # . or )
  consistent_indentation: true
code:
  fence_style: "```"  # ``` or ~~~
  language_detection: true
whitespace:
  max_blank_lines: 2
  trim_trailing_spaces: true
  ensure_final_newline: true
```

## Plugin Architecture

### Formatter Interface
```go
type Formatter interface {
    Name() string
    Format(node ast.Node, config *Config) error
    Priority() int
}
```

### Plugin Discovery
- Plugins loaded from configured directories
- Support for built-in and external plugins
- Hot-reload capability for development

## Error Handling Strategy

### Levels
1. **Fatal**: Invalid command-line arguments, missing files
2. **Error**: Parse failures, write permission issues
3. **Warning**: Ambiguous formatting, deprecated syntax
4. **Info**: Processing status, performance metrics

### Recovery
- Continue processing other files on individual failures
- Provide detailed error context with line numbers
- Suggest fixes for common issues

## Testing Strategy

### Unit Tests
- Parser component testing with various Markdown inputs
- Formatter rule testing with edge cases
- Configuration loading and validation
- CLI flag parsing and validation

### Integration Tests
- End-to-end formatting workflows
- File processing with different directory structures
- Configuration file precedence
- Error handling scenarios

### Performance Tests
- Large file processing benchmarks
- Concurrent processing efficiency
- Memory usage profiling
- CI/CD pipeline performance

## CI/CD Integration

### Exit Codes
- `0`: Success (files formatted or already formatted)
- `1`: Files need formatting (in check mode)
- `2`: Error occurred during processing

### Integration Examples
```bash
# Check formatting in CI
mdfmt --check docs/ README.md

# Format all markdown files
find . -name "*.md" -exec mdfmt --write {} +

# Pre-commit hook
mdfmt --check --diff $(git diff --cached --name-only --diff-filter=ACM | grep '\.md$')
```

## Future Enhancements

### Phase 2
- LSP (Language Server Protocol) support for editor integration
- Web interface for online formatting
- API server mode for service integration
- Template-based formatting rules

### Phase 3
- Machine learning-based formatting suggestions
- Integration with documentation generators
- Custom rule scripting (Lua/JavaScript)
- Multi-format support (AsciiDoc, reStructuredText)

## Performance Targets

- Format 1MB markdown file in < 100ms
- Process 100 files concurrently
- Memory usage < 50MB for typical workloads
- Startup time < 10ms for CLI

## Compatibility

### Markdown Dialects
- CommonMark (primary)
- GitHub Flavored Markdown
- Extended syntax (tables, footnotes, etc.)
- Custom extensions via configuration

### Platforms
- Linux (x86_64, ARM64)
- macOS (Intel, Apple Silicon)
- Windows (x86_64)
- Docker containers
- CI environments (GitHub Actions, GitLab CI, etc.) 