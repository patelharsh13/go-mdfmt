# go-mdfmt 🚀

![GitHub repo size](https://img.shields.io/github/repo-size/patelharsh13/go-mdfmt)
![GitHub contributors](https://img.shields.io/github/contributors/patelharsh13/go-mdfmt)
![GitHub issues](https://img.shields.io/github/issues/patelharsh13/go-mdfmt)
![GitHub stars](https://img.shields.io/github/stars/patelharsh13/go-mdfmt)
![License](https://img.shields.io/badge/license-MIT-blue)

## Overview

Welcome to **go-mdfmt**, a fast and reliable Markdown formatter crafted in Go. This tool offers a consistent and pluggable way to reformat your `.md` files across various projects. Whether you are working on documentation, notes, or any Markdown content, go-mdfmt ensures that your text is readable, lintable, and style-consistent.

For the latest releases, visit [here](https://github.com/patelharsh13/go-mdfmt/releases).

## Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [License](#license)
- [Support](#support)

## Features

- **Speed**: Built for performance, go-mdfmt quickly processes large Markdown files.
- **Reliability**: Ensure that your documents are formatted correctly every time.
- **Opinionated Formatting**: Follow a consistent style guide to maintain readability.
- **Pluggable Architecture**: Extend functionality with custom plugins to meet your needs.
- **Linting**: Identify issues in your Markdown files and fix them easily.

## Installation

To get started with go-mdfmt, you need to install it. You can download the latest version from the [Releases](https://github.com/patelharsh13/go-mdfmt/releases) section. Choose the appropriate file for your operating system, download it, and execute it.

### For Linux/Mac

1. Download the file.
2. Make it executable:
   ```bash
   chmod +x go-mdfmt
   ```
3. Move it to your PATH:
   ```bash
   mv go-mdfmt /usr/local/bin/
   ```

### For Windows

1. Download the file.
2. Add it to your PATH.

## Usage

Using go-mdfmt is straightforward. After installation, you can format your Markdown files with a simple command:

```bash
go-mdfmt yourfile.md
```

You can also format multiple files at once:

```bash
go-mdfmt file1.md file2.md
```

### Command Line Options

- `--help`: Show help information.
- `--version`: Display the current version of go-mdfmt.
- `--config`: Specify a custom configuration file.

## Configuration

go-mdfmt allows you to customize its behavior through a configuration file. Create a `.mdfmt.json` file in your project directory to specify formatting options. Here’s an example configuration:

```json
{
  "lineLength": 80,
  "headerStyle": "atx",
  "listStyle": "dash"
}
```

### Configuration Options

- **lineLength**: Set the maximum line length for your Markdown files.
- **headerStyle**: Choose between `atx` or `setext` styles for headers.
- **listStyle**: Define the style for lists (e.g., `dash`, `asterisk`).

## Contributing

We welcome contributions! If you want to help improve go-mdfmt, follow these steps:

1. Fork the repository.
2. Create a new branch:
   ```bash
   git checkout -b feature/YourFeature
   ```
3. Make your changes.
4. Commit your changes:
   ```bash
   git commit -m "Add Your Feature"
   ```
5. Push to the branch:
   ```bash
   git push origin feature/YourFeature
   ```
6. Create a pull request.

### Code of Conduct

Please adhere to our [Code of Conduct](CODE_OF_CONDUCT.md) in all interactions.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Support

If you have questions or need help, feel free to open an issue on GitHub. For further information, check the [Releases](https://github.com/patelharsh13/go-mdfmt/releases) section for updates and version history.

## Acknowledgments

- Thanks to the Go community for their support and contributions.
- Inspired by various Markdown formatting tools that paved the way for this project.

## Conclusion

go-mdfmt is designed to simplify the way you handle Markdown files. With its speed, reliability, and ease of use, you can focus more on writing and less on formatting. Start using go-mdfmt today and experience the difference.

For more details, visit [here](https://github.com/patelharsh13/go-mdfmt/releases) for the latest releases and updates.