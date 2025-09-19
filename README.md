# TidalCycles for VSCode & Cursor

Support for TidalCycles in VSCode and Cursor IDE. You can learn more about
TidalCycles at [TidalCycles](https://tidalcycles.org).

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Version](https://img.shields.io/visual-studio-marketplace/v/tidalcycles.vscode-tidalcycles.svg)](https://marketplace.visualstudio.com/items?itemName=tidalcycles.vscode-tidalcycles)

## Table of Contents

- [Features](#features)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
- [Configuration](#configuration)
- [Known Issues](#known-issues)
- [Contributing](#contributing)
- [License](#license)

## Features

This VSCode/Cursor extension for TidalCycles provides an interactive live coding environment with:

- **Live Code Evaluation**: Execute TidalCycles patterns in real-time
- **Visual Feedback**: Highlighted code blocks show what was just evaluated
- **Flexible Boot System**: Use default boot sequence or custom boot files
- **GHCi Integration**: Direct communication with GHCi/Stack
- **Multi-line Support**: Evaluate single lines or code blocks
- **Workspace Integration**: Output panel and console logging
- **Cross-Platform**: Works on Windows, macOS, and Linux

### Key Commands

- `Shift+Enter` - Evaluate single line under cursor
- `Ctrl+Enter` - Evaluate multiple lines/code block
- `Ctrl+Alt+H` - Hush (silence all patterns)

## Installation & Setup

### Prerequisites

1. **TidalCycles**: Install the TidalCycles Haskell package
2. **SuperDirt**: Install SuperCollider and SuperDirt for audio output
3. **GHCi**: Glasgow Haskell Compiler interactive environment

Complete installation instructions: [TidalCycles Installation Guide](https://tidalcycles.org/docs/getting-started/installation)

### Extension Installation

#### From Marketplace
- **VSCode**: Search for "TidalCycles" in the Extensions view
- **Cursor**: Install from VSCode marketplace (compatible with Cursor v0.1.0+)

#### From VSIX
1. Download the `.vsix` file from releases
2. Install via command palette: `Extensions: Install from VSIX...`

### Syntax Highlighting Setup

For proper syntax highlighting in `.tidal` files:

1. Install [Haskell Syntax Highlighting](https://marketplace.visualstudio.com/items?itemName=justusadam.language-haskell)
2. Add to your `settings.json`:

```json
{
    "files.associations": {
        "*.tidal": "haskell"
    }
}
```

## Usage

1. Open a `.tidal` file or create a new file with `.tidal` extension
2. Write TidalCycles patterns:
   ```haskell
   d1 $ sound "bd sn hh sn"
   d2 $ sound "arpy*4" # speed 2
   ```
3. Use `Shift+Enter` to evaluate single lines or `Ctrl+Enter` for blocks
4. Use `Ctrl+Alt+H` to silence all patterns

## Configuration

### Essential Settings

#### GHCi Path
Configure the path to your GHCi executable:

```json
{
    "tidalcycles.ghciPath": "ghci"  // if ghci is in PATH
    // OR
    "tidalcycles.ghciPath": "/usr/local/bin/ghci"  // absolute path
    // OR for Windows
    "tidalcycles.ghciPath": "c:\\path\\to\\ghci.exe"
}
```

#### Stack Users
If using Stack:

```json
{
    "tidalcycles.useStackGhci": true
}
```

### Boot Configuration

#### Default Boot
The extension includes a comprehensive default boot sequence that:
- Sets up TidalCycles context
- Configures SuperDirt connection
- Defines standard pattern functions (`d1`-`d16`)
- Sets up transition functions

#### Custom Boot Files
Use your own boot sequence:

```json
{
    "tidalcycles.bootTidalPath": "/path/to/your/BootTidal.hs"
    // OR use BootTidal.hs in workspace root
    "tidalcycles.useBootFileInCurrentDirectory": true
}
```

### All Configuration Options

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `tidalcycles.ghciPath` | string | `"ghci"` | Path to GHCi executable |
| `tidalcycles.useStackGhci` | boolean | `false` | Use Stack's GHCi instance |
| `tidalcycles.bootTidalPath` | string | `""` | Path to custom boot file |
| `tidalcycles.useBootFileInCurrentDirectory` | boolean | `false` | Use `BootTidal.hs` in workspace |
| `tidalcycles.feedbackColor` | string | `"rgba(100,250,100,0.3)"` | Highlight color for evaluated code |
| `tidalcycles.showEvalCount` | boolean | `false` | Show evaluation counter |
| `tidalcycles.showGhciOutput` | boolean | `true` | Display GHCi output |
| `tidalcycles.showOutputInConsoleChannel` | boolean | `true` | Show output in VS Code panel |
| `tidalcycles.randomMessageProbability` | number | `0` | Probability of random messages (0-1) |
| `tidalcycles.randomMessages` | array | `[]` | Array of random message strings |

### Full Configuration Example

```json
{
    "tidalcycles.ghciPath": "ghci",
    "tidalcycles.useStackGhci": false,
    "tidalcycles.feedbackColor": "rgba(100,250,100,0.5)",
    "tidalcycles.showEvalCount": true,
    "tidalcycles.showGhciOutput": false,
    "tidalcycles.showOutputInConsoleChannel": true,
    "tidalcycles.useBootFileInCurrentDirectory": false,
    "tidalcycles.bootTidalPath": ""
}
```

## Contributing

Want to contribute to this extension? Check out our [Development Guide](DEV.md) for setup instructions, architecture details, and contribution guidelines.

## Known Issues

- The `Eval and Copy` and `Eval Multi Line and Copy` commands from the Atom package are not implemented
- Some complex multi-line expressions may require manual formatting
- GHCi startup time may vary based on system configuration

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

## Links

- [TidalCycles Official Site](https://tidalcycles.org)
- [TidalCycles Documentation](https://tidalcycles.org/docs/)
- [GitHub Repository](https://github.com/tidalcycles/vscode-tidalcycles)
- [VSCode Marketplace](https://marketplace.visualstudio.com/items?itemName=tidalcycles.vscode-tidalcycles)
- [SuperCollider](https://supercollider.github.io/)

---

**Happy Live Coding!** 🎵