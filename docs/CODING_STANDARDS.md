# Go Coding Standards for go-mdfmt

This document outlines the coding standards and best practices for the go-mdfmt project, with emphasis on eliminating magic numbers and maintaining high code quality.

## Constants Usage Examples

### 1. Regex Pattern Constants

Instead of inline regex patterns, define them as constants:

```go
const (
    // Markdown link patterns
    MarkdownLinkPattern = `\[[^\]]*\]\([^)]*\)`
    BrokenLinkPattern = `\[([^\]]*)\n([^\]]*)\]\(([^)]*)\)`
    MultiBreakPattern = `\[([^\]]*(?:\n[^\]]*)*)\]\(([^)]*)\)`
)
```

### 2. Array Index Constants

For regex match groups and array access:

```go
const (
    // Regex match indices for broken links
    BrokenLinkPartsCount = 4
    MultiBreakPartsCount = 3
    LinkTextPart1Index = 1
    LinkTextPart2Index = 2
    URLPartIndex = 3
    LinkTextIndex = 1
    URLIndex = 2
)
```

### 3. Configuration Constants

For default values and limits:

```go
const (
    // Default configuration values
    DefaultLineWidth = 80
    DefaultMaxBlankLines = 2
    MinHeadingLevel = 1
    MaxHeadingLevel = 6
    SetextMaxLevel = 2
    
    // File permissions
    ConfigFilePermissions = 0o600
    OutputFilePermissions = 0o600
)
```

### 4. Priority Constants

For formatter execution order:

```go
const (
    // Formatter priorities (higher runs first)
    HeadingFormatterPriority = 100
    ParagraphFormatterPriority = 90
    ListFormatterPriority = 80
    CodeFormatterPriority = 70
    InlineFormatterPriority = 60
    WhitespaceFormatterPriority = 10
)
```

### 5. String Constants

For node types and styles:

```go
const (
    // Heading styles
    AtxHeadingStyle = "atx"
    SetextHeadingStyle = "setext"
    
    // List markers
    DefaultBulletMarker = "-"
    DefaultNumberMarker = "."
    
    // Code fence styles
    BacktickFence = "```"
    TildeFence = "~~~"
)
```

## Function Complexity Management

### Before (High Complexity)
```go
func (f *ListFormatter) Format(node parser.Node, cfg *config.Config) error {
    // 50+ lines of complex logic
    switch n := node.(type) {
    case *parser.List:
        // Complex nested logic
        if !n.Ordered {
            // Unordered list logic
        } else {
            // Ordered list logic
        }
        // More complex logic...
    }
    return nil
}
```

### After (Refactored)
```go
func (f *ListFormatter) Format(node parser.Node, cfg *config.Config) error {
    switch n := node.(type) {
    case *parser.List:
        return f.formatList(n, cfg)
    case *parser.ListItem:
        return f.formatListItem(n, cfg)
    }
    return nil
}

func (f *ListFormatter) formatList(list *parser.List, cfg *config.Config) error {
    if !list.Ordered {
        f.formatUnorderedList(list, cfg)
    } else {
        f.formatOrderedList(list, cfg)
    }
    return f.processListItems(list, cfg)
}
```

## Error Handling Standards

### Consistent Error Messages
```go
const (
    // Error message templates
    ErrParseFailedTemplate = "failed to parse markdown: %w"
    ErrFormatFailedTemplate = "failed to format document: %w"
    ErrRenderFailedTemplate = "failed to render document: %w"
    ErrWriteFailedTemplate = "failed to write file: %w"
)

func formatMarkdownContent(content []byte, cfg *config.Config) (string, error) {
    p := parser.DefaultParser()
    doc, err := p.Parse(content)
    if err != nil {
        return "", fmt.Errorf(ErrParseFailedTemplate, err)
    }
    // ... rest of function
}
```

## Testing Constants

### Test Data Constants
```go
const (
    // Test timeouts and limits
    TestTimeout = 5 * time.Second
    MaxTestRetries = 3
    
    // Expected test values
    ExpectedStatusOK = 0
    ExpectedStatusError = 2
    ExpectedStatusChangesNeeded = 1
    
    // Test file patterns
    TestDataPattern = "testdata/*.md"
    TestOutputDir = "testdata/results"
    TestCopiesDir = "testdata/copies"
)
```

## Linter Compliance Checklist

- [ ] No magic numbers (use named constants)
- [ ] No duplicate code branches
- [ ] Functions under 15 cyclomatic complexity
- [ ] No unused functions or variables
- [ ] Consistent naming conventions
- [ ] Proper error handling with context
- [ ] All imports organized and used
- [ ] GoDoc comments for public functions

## Code Review Guidelines

When reviewing code, check for:

1. **Magic Numbers**: Any numeric literal that isn't 0, 1, or -1 in obvious contexts
2. **String Literals**: Repeated strings that should be constants
3. **Function Length**: Functions that are too complex or long
4. **Error Handling**: Proper error wrapping and context
5. **Constants Organization**: Related constants grouped together
6. **Documentation**: Clear comments explaining the purpose of constants

## Tools and Commands

Use these commands to maintain code quality:

```bash
# Run all quality checks
make check-all

# Run linter specifically
make lint

# Run tests with coverage
make test-coverage

# Format code
make fmt
```

## Conclusion

Following these standards ensures:
- Better code maintainability
- Easier debugging and testing
- Consistent code style across the project
- Compliance with Go best practices
- Clean linter output without warnings 