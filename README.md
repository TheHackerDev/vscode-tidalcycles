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
- [Development](#development)
- [Architecture](#architecture)
- [Testing](#testing)
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

## Development

### Development Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/tidalcycles/vscode-tidalcycles.git
   cd vscode-tidalcycles
   ```

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Build the extension**:
   ```bash
   npm run compile
   ```

4. **Watch mode for development**:
   ```bash
   npm run watch
   ```

### Building & Packaging

#### Compile TypeScript
```bash
npm run compile
```

#### Package as VSIX
```bash
# Install vsce if not already installed
npm install -g vsce

# Package the extension
vsce package
```

This creates a `.vsix` file that can be installed manually.

#### Development Testing
1. Open the project in VSCode
2. Press `F5` to launch Extension Development Host
3. Test the extension in the new window

### NPM Scripts

| Script | Description |
|--------|-------------|
| `npm run compile` | Compile TypeScript to JavaScript |
| `npm run watch` | Watch mode compilation |
| `npm run test` | Run unit tests |
| `npm run vscode:prepublish` | Pre-publish compilation |

## Architecture

### Project Structure

```
vscode-tidalcycles/
├── src/                    # TypeScript source code
│   ├── main.ts            # Extension entry point & command registration
│   ├── repl.ts            # REPL interface and evaluation logic
│   ├── tidal.ts           # TidalCycles integration & boot sequence
│   ├── ghci.ts            # GHCi process management
│   ├── editor.ts          # Text editor utilities & expression parsing
│   ├── config.ts          # Configuration management
│   ├── history.ts         # Command history tracking
│   └── logging.ts         # Logging utilities
├── test/                  # Unit tests
├── out/                   # Compiled JavaScript (generated)
├── images/               # Extension icon and assets
├── package.json          # Extension manifest
├── tsconfig.json         # TypeScript configuration
└── README.md            # This file
```

### Core Components

#### Extension Activation (`main.ts`)
- Registers VSCode commands (`tidal.eval`, `tidal.evalMulti`, `tidal.hush`)
- Sets up keyboard shortcuts
- Manages REPL instances per text editor
- Handles extension lifecycle

#### REPL Management (`repl.ts`)
- `IRepl` interface for interactive evaluation
- Manages text editor decorations for visual feedback
- Coordinates between editor, TidalCycles, and history
- Handles single-line and multi-line evaluation

#### TidalCycles Integration (`tidal.ts`)
- `ITidal` interface for TidalCycles communication
- Manages boot sequence (default or custom)
- Sends expressions to GHCi
- Handles multi-line expression formatting

#### GHCi Process Management (`ghci.ts`)
- `IGhci` interface for GHCi communication
- Spawns and manages GHCi/Stack processes
- Handles stdin/stdout/stderr streams
- Cross-platform process management

#### Editor Utilities (`editor.ts`)
- `TidalEditor` class for text manipulation
- `TidalExpression` for parsed expressions
- Cursor position and range handling
- Multi-line block detection

#### Configuration (`config.ts`)
- VSCode settings integration
- Type-safe configuration access
- Default value management

### Data Flow

1. **User Input**: User presses evaluation key combination
2. **Command Registration**: VSCode triggers registered command
3. **REPL Coordination**: REPL gets active text editor and parses expression
4. **TidalCycles Processing**: Expression sent to TidalCycles module
5. **GHCi Communication**: TidalCycles sends formatted commands to GHCi
6. **Visual Feedback**: Editor highlights evaluated code
7. **History Logging**: Expression logged for history tracking

### Key Interfaces

```typescript
interface ITidal {
    sendTidalExpression(expression: string): Promise<void>;
}

interface IGhci {
    writeLn(command: string): void;
}

interface IRepl {
    hush(): Promise<void>;
    evaluate(isMultiline: boolean): Promise<void>;
}
```

## Testing

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode (during development)
npm run watch
```

### Test Structure

- **Unit Tests**: Located in `test/` directory
- **Test Framework**: Mocha with Chai assertions
- **Mocking**: TypeMoq for interface mocking
- **Coverage**: Tests cover core functionality of each module

### Test Files

- `test/expression.test.ts` - Expression parsing tests
- `test/ghci.test.ts` - GHCi integration tests
- `test/logger.test.ts` - Logging functionality tests
- `test/repl.test.ts` - REPL evaluation tests
- `test/tidal.test.ts` - TidalCycles integration tests

### Writing Tests

Tests use Mocha and Chai with TypeMoq for mocking:

```typescript
import { expect } from 'chai';
import * as TypeMoq from 'typemoq';

describe('Component Tests', () => {
    it('should test functionality', () => {
        // Test implementation
        expect(result).to.equal(expected);
    });
});
```

## IDE Compatibility

### VSCode Support
- **Minimum Version**: 1.23.0
- **Full compatibility** with all VSCode features
- Native extension marketplace integration

### Cursor IDE Support
- **Minimum Version**: 0.1.0
- **Full compatibility** with Cursor's VSCode-based architecture
- All features work identically to VSCode
- Install from VSCode marketplace or manual VSIX installation

### Platform Support
- **Windows**: Full support with proper path handling
- **macOS**: Native support
- **Linux**: Complete compatibility

## Contributing

### Development Workflow

1. **Fork** the repository
2. **Create** a feature branch: `git checkout -b feature/amazing-feature`
3. **Make** your changes with tests
4. **Run** tests: `npm test`
5. **Commit** changes: `git commit -m 'Add amazing feature'`
6. **Push** to branch: `git push origin feature/amazing-feature`
7. **Create** a Pull Request

### Code Style

- **TypeScript**: Strict mode enabled
- **Formatting**: Follow existing patterns
- **Interfaces**: Use for all major components
- **Error Handling**: Comprehensive error catching and logging
- **Testing**: Unit tests for new functionality

### Reporting Issues

When reporting issues, please include:
- VSCode/Cursor version
- Extension version
- Operating system
- TidalCycles/GHCi version
- Steps to reproduce
- Error messages/logs

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