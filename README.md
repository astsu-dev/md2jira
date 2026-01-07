# md2jira

A command-line tool and Go library to convert Markdown-formatted text into JIRA Text Formatting Notation (Wiki Markup).

## Problem

Corporate JIRA Server/Data Center installations often lack native Markdown support, requiring manual reformatting of documentation, README files, and other Markdown content before pasting into JIRA tickets. This tool automates that conversion.

## Installation

### From Source

```bash
go install github.com/astsu-dev/md2jira@latest
```

Or clone and build:

```bash
git clone https://github.com/astsu-dev/md2jira.git
cd md2jira
go build -o md2jira .
```

## Usage

### Command Line

```bash
# Convert a file to stdout
md2jira input.md

# Convert a file to output file
md2jira -o output.txt input.md

# Convert from stdin
cat README.md | md2jira

# Pipe from clipboard (macOS)
pbpaste | md2jira | pbcopy

# Show conversion warnings
md2jira --verbose input.md

# Show version
md2jira --version

# Show help
md2jira --help
```

### As a Go Library

```go
package main

import (
    "fmt"
    "github.com/astsu-dev/md2jira"
)

func main() {
    markdown := `# Hello World

This is **bold** and *italic*.

- Item 1
- Item 2
`

    // Basic conversion
    jira := md2jira.Convert(markdown)
    fmt.Println(jira)

    // With options
    result, _ := md2jira.ConvertWithOptions(markdown, md2jira.Options{
        WarnOnUnsupported: true,
    })
    fmt.Println(result.Output)
    for _, warning := range result.Warnings {
        fmt.Println("Warning:", warning)
    }
}
```

## Conversion Reference

### Text Formatting

| Markdown     | JIRA       | Description             |
| ------------ | ---------- | ----------------------- |
| `**bold**`   | `*bold*`   | Bold text               |
| `__bold__`   | `*bold*`   | Bold text (alternate)   |
| `*italic*`   | `_italic_` | Italic text             |
| `_italic_`   | `_italic_` | Italic text (alternate) |
| `~~strike~~` | `-strike-` | Strikethrough           |
| `` `code` `` | `{{code}}` | Inline code             |
| `***both***` | `*_both_*` | Bold and italic         |

### Headings

| Markdown           | JIRA            |
| ------------------ | --------------- |
| `# Heading 1`      | `h1. Heading 1` |
| `## Heading 2`     | `h2. Heading 2` |
| `### Heading 3`    | `h3. Heading 3` |
| `#### Heading 4`   | `h4. Heading 4` |
| `##### Heading 5`  | `h5. Heading 5` |
| `###### Heading 6` | `h6. Heading 6` |

### Lists

| Markdown         | JIRA                   |
| ---------------- | ---------------------- |
| `- item`         | `* item`               |
| `* item`         | `* item`               |
| `1. item`        | `# item`               |
| Nested unordered | `** item`              |
| Nested ordered   | `## item`              |
| Mixed nested     | `*# item` or `#* item` |
| `- [ ] task`     | `* ( ) task`           |
| `- [x] task`     | `* (/) task`           |

### Links and Images

| Markdown              | JIRA              |
| --------------------- | ----------------- |
| `[text](url)`         | `[text\|url]`     |
| `[text](url "title")` | `[text\|url]`     |
| `![alt](url)`         | `!url\|alt=text!` |

### Code Blocks

Fenced code blocks with language hints:

````markdown
```javascript
function hello() {
  console.log("Hello!");
}
```
````

Converts to:

```
{code:javascript}
function hello() {
    console.log("Hello!");
}
{code}
```

Supported language mappings include: `js`/`javascript`, `ts`/`typescript`, `py`/`python`, `rb`/`ruby`, `sh`/`bash`, `go`, `java`, `rust`, `cpp`, `yaml`, and more.

### Blockquotes

```markdown
> This is a quote
> spanning multiple lines
```

Converts to:

```
{quote}
This is a quote
spanning multiple lines
{quote}
```

### Tables

```markdown
| Header 1 | Header 2 |
| -------- | -------- |
| Cell 1   | Cell 2   |
| Cell 3   | Cell 4   |
```

Converts to:

```
||Header 1||Header 2||
|Cell 1|Cell 2|
|Cell 3|Cell 4|
```

### Horizontal Rules

`---`, `***`, or `___` all convert to `----`

## Examples

### Input (Markdown)

```markdown
# Project Documentation

A **bold** statement with _emphasis_.

## Features

- First feature
- Second feature
  - Sub-feature
- Third feature

## Installation

\`\`\`bash
npm install my-package
\`\`\`

## Links

Visit [our website](https://example.com) for more info.

| Option | Description |
| ------ | ----------- |
| `-v`   | Verbose     |
| `-h`   | Help        |
```

### Output (JIRA)

```
h1. Project Documentation

A *bold* statement with _emphasis_.

h2. Features

* First feature
* Second feature
** Sub-feature
* Third feature

h2. Installation

{code:bash}
npm install my-package
{code}

h2. Links

Visit [our website|https://example.com] for more info.

||Option||Description||
|-v|Verbose|
|-h|Help|
```

## Limitations

- Inline HTML tags (`<sup>`, `<sub>`, etc.) have limited support when mixed with text
- Reference-style links are resolved but the reference definitions are not preserved
- Some advanced Markdown extensions (footnotes, definition lists) are not supported
- Emoji shortcodes are passed through as-is

## Requirements

- Go 1.21+ (for building from source)

## Dependencies

- [goldmark](https://github.com/yuin/goldmark) - Markdown parser

## License

MIT License
