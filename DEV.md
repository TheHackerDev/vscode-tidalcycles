# TidalCycles Extension - Development Guide

This guide provides comprehensive information for developers working on the TidalCycles VSCode/Cursor extension.

## Table of Contents

- [Development Setup](#development-setup)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Core Components](#core-components)
- [Building & Packaging](#building--packaging)
- [Testing](#testing)
- [IDE Compatibility](#ide-compatibility)
- [Contributing](#contributing)
- [Debugging](#debugging)

## Development Setup

### Prerequisites

1. **Node.js**: Version 16.x or higher
2. **npm**: Latest version
3. **VSCode**: For development and testing
4. **TidalCycles**: For testing functionality
5. **GHCi**: Glasgow Haskell Compiler Interactive

### Initial Setup

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

### Development Workflow

1. **Open in VSCode**: Open the project root in VSCode
2. **Press F5**: Launch Extension Development Host
3. **Test changes**: Create a `.tidal` file and test functionality
4. **Debug**: Use breakpoints and console logging
5. **Iterate**: Make changes and reload the Extension Development Host

## Architecture

### Overview

The extension follows a modular TypeScript architecture with clear separation of concerns:

- **Extension Entry Point** (`main.ts`) - Command registration and lifecycle
- **REPL Interface** (`repl.ts`) - User interaction and coordination
- **TidalCycles Integration** (`tidal.ts`) - TidalCycles-specific logic
- **GHCi Management** (`ghci.ts`) - Process management
- **Text Processing** (`editor.ts`) - Expression parsing
- **Configuration** (`config.ts`) - Settings management
- **Utilities** (`history.ts`, `logging.ts`) - Supporting functionality

### Data Flow

```
User Input (Shift+Enter)
    ↓
VSCode Command System
    ↓
REPL.evaluate()
    ↓
TidalEditor.getTidalExpressionUnderCursor()
    ↓
Tidal.sendTidalExpression()
    ↓
Ghci.writeLn()
    ↓
GHCi Process
    ↓
SuperDirt/Audio Output
```

### Key Design Principles

1. **Interface-based Design**: Core components implement interfaces for testability
2. **Separation of Concerns**: Each module has a single responsibility
3. **Error Handling**: Comprehensive error catching and user feedback
4. **Cross-platform Support**: Works on Windows, macOS, and Linux
5. **Configurability**: Extensive configuration options for different setups

## Project Structure

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
│   ├── runTest.ts         # Test runner entry point
│   ├── suite/             # Test suite configuration
│   └── *.test.ts         # Individual test files
├── out/                   # Compiled JavaScript (generated)
├── images/               # Extension icon and assets
├── package.json          # Extension manifest and dependencies
├── tsconfig.json         # TypeScript configuration
├── README.md            # User documentation
└── DEV.md              # This development guide
```

## Core Components

### Extension Activation (`main.ts`)

**Purpose**: Entry point and command registration

**Key Functions**:
- `activate()` - Called when extension activates
- Command registration for `tidal.eval`, `tidal.evalMulti`, `tidal.hush`
- REPL instance management per text editor
- Event handler setup for GHCi output

**Important Details**:
- Creates separate REPL instances for different editors
- Manages extension lifecycle and cleanup
- Sets up output channel for logging

### REPL Management (`repl.ts`)

**Purpose**: User interaction coordination

**Key Functions**:
- `evaluate(isMultiline: boolean)` - Main evaluation function
- `hush()` - Stop all patterns
- `feedback()` - Visual feedback for evaluations
- `editingTidalFile()` - File type validation

**Important Details**:
- Only operates on `.tidal` files
- Provides visual feedback with configurable colors
- Coordinates between editor, TidalCycles, and history

### TidalCycles Integration (`tidal.ts`)

**Purpose**: TidalCycles-specific functionality

**Key Functions**:
- `sendTidalExpression()` - Send expressions to GHCi
- `init()` - Initialize TidalCycles environment
- Boot sequence management (default and custom)

**Important Details**:
- Contains comprehensive default boot sequence
- Supports custom boot files
- Handles multi-line expression formatting using `:{` ... `:}`

**Default Boot Sequence Includes**:
- TidalCycles context setup
- SuperDirt connection configuration
- Standard pattern functions (`d1`-`d16`)
- Transition functions (xfade, jump, etc.)

### GHCi Process Management (`ghci.ts`)

**Purpose**: GHCi subprocess management

**Key Functions**:
- `getGhciProcess()` - Get or create GHCi process
- `writeLn()` - Send commands to GHCi
- Process spawning and stream management

**Important Details**:
- Supports both system GHCi and Stack-managed GHCi
- Cross-platform process spawning
- Stream handling for stdout/stderr
- Error handling for process communication

### Editor Utilities (`editor.ts`)

**Purpose**: Text processing and expression extraction

**Key Classes**:
- `TidalEditor` - Main text processing class
- `TidalExpression` - Represents parsed expressions

**Key Functions**:
- `getTidalExpressionUnderCursor()` - Extract expressions
- Expression boundary detection
- Cursor position handling

**Important Details**:
- Smart multi-line block detection using blank lines
- Handles both single-line and multi-line expressions
- Provides range information for visual feedback

### Configuration (`config.ts`)

**Purpose**: VSCode settings integration

**Key Functions**:
- Type-safe access to all configuration options
- Default value management
- Settings validation

**Configuration Categories**:
- **Essential**: `ghciPath`, `useStackGhci`
- **Boot**: `bootTidalPath`, `useBootFileInCurrentDirectory`
- **Display**: `feedbackColor`, `showEvalCount`, `showGhciOutput`
- **Advanced**: `randomMessageProbability`, `randomMessages`

## Building & Packaging

### Development Build

```bash
# Compile TypeScript
npm run compile

# Watch mode for active development
npm run watch
```

### Production Packaging

```bash
# Install vsce globally if not already installed
npm install -g @vscode/vsce

# Package the extension
npm run package
# OR
vsce package
```

This creates a `.vsix` file that can be:
- Installed manually in VSCode/Cursor
- Published to the marketplace
- Distributed independently

### NPM Scripts

| Script | Description |
|--------|-------------|
| `npm run compile` | Compile TypeScript to JavaScript |
| `npm run watch` | Watch mode compilation for development |
| `npm run test` | Run unit tests |
| `npm run package` | Package as .vsix file |
| `npm run vscode:prepublish` | Pre-publish compilation |

### Build Configuration

**TypeScript Configuration** (`tsconfig.json`):
- **Target**: ES2017
- **Module**: CommonJS
- **Output Directory**: `out/`
- **Strict Type Checking**: Enabled
- **Source Maps**: Generated for debugging

## Testing

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode (during development)
npm run watch
```

### Test Framework

- **Test Runner**: Mocha with TDD interface
- **Assertions**: Chai
- **Mocking**: TypeMoq for interface mocking
- **VS Code Testing**: @vscode/test-electron for integration tests

### Test Structure

```
test/
├── runTest.ts              # Test runner entry point
├── suite/
│   └── index.ts           # Test suite configuration
├── expression.test.ts      # Expression parsing tests
├── ghci.test.ts           # GHCi integration tests
├── logger.test.ts         # Logging functionality tests
├── repl.test.ts           # REPL evaluation tests
└── tidal.test.ts          # TidalCycles integration tests
```

### Writing Tests

Example test structure:

```typescript
import { expect } from 'chai';
import * as TypeMoq from 'typemoq';
import { TidalEditor } from '../src/editor';

describe('TidalEditor Tests', () => {
    let editor: TidalEditor;

    beforeEach(() => {
        // Setup mock objects
        const mockDocument = TypeMoq.Mock.ofType<vscode.TextDocument>();
        editor = new TidalEditor(mockDocument.object);
    });

    it('should extract single line expression', () => {
        // Test implementation
        const expression = editor.getTidalExpressionUnderCursor(false);
        expect(expression).to.not.be.null;
    });
});
```

### Test Coverage Areas

1. **Expression Parsing**: Various text scenarios and cursor positions
2. **REPL Operations**: Command execution and error handling
3. **Configuration**: Settings validation and default values
4. **GHCi Integration**: Process management and communication
5. **Error Scenarios**: Edge cases and failure conditions

## IDE Compatibility

### VSCode Support
- **Minimum Version**: 1.23.0
- **Engine Declaration**: `"vscode": "^1.23.0"`
- **Full API Compatibility**: Uses standard VSCode extension APIs

### Cursor IDE Support
- **Minimum Version**: 0.1.0
- **Engine Declaration**: `"cursor": "^0.1.0"`
- **Architecture**: Cursor is VSCode-based, so full compatibility
- **Installation**: Via VSCode marketplace or manual .vsix

### Platform Support

**Windows**:
- Proper path handling with backslashes
- `ghci.exe` executable support
- Stack integration support

**macOS**:
- Native Unix path support
- Homebrew GHCi installations
- Stack via Homebrew

**Linux**:
- Standard Unix tooling
- Package manager GHCi installations
- Stack via curl installer

## Contributing

### Development Workflow

1. **Fork** the repository on GitHub
2. **Clone** your fork locally
3. **Create** a feature branch: `git checkout -b feature/amazing-feature`
4. **Setup** development environment: `npm install`
5. **Make** changes with appropriate tests
6. **Test** thoroughly: `npm test`
7. **Commit** with clear messages: `git commit -m 'Add amazing feature'`
8. **Push** to your fork: `git push origin feature/amazing-feature`
9. **Create** a Pull Request

### Code Style Guidelines

**TypeScript Standards**:
- Strict mode enabled in `tsconfig.json`
- Use interfaces for all major components
- Prefer `const` over `let` where possible
- Use meaningful variable and function names

**Error Handling**:
- Always catch and handle errors appropriately
- Use the logging system for error reporting
- Provide user-friendly error messages

**Testing Requirements**:
- Unit tests for all new functionality
- Mock external dependencies using TypeMoq
- Test both success and failure scenarios

**Documentation**:
- JSDoc comments for public interfaces
- Inline comments for complex logic
- Update this DEV.md for architectural changes

### Reporting Issues

When reporting bugs or requesting features, include:

**Environment Information**:
- VSCode/Cursor version
- Extension version
- Operating system and version
- TidalCycles version
- GHCi/Stack version

**Reproduction Steps**:
1. Detailed steps to reproduce
2. Expected behavior
3. Actual behavior
4. Error messages or logs
5. Sample TidalCycles code if applicable

## Debugging

### Extension Development Host

1. Open project in VSCode
2. Press `F5` to launch Extension Development Host
3. Set breakpoints in TypeScript source
4. Use Developer Tools for logging

### Logging and Diagnostics

**Output Channel**: "TidalCycles"
- View via: View → Output → TidalCycles

**Log Levels**:
- `logger.log()` - General information
- `logger.warning()` - Warning messages
- `logger.error()` - Error messages (also shows VS Code notification)

**Debug Configuration** (`.vscode/launch.json`):
```json
{
    "name": "Extension",
    "type": "extensionHost",
    "request": "launch",
    "args": ["--extensionDevelopmentPath=${workspaceFolder}"],
    "outFiles": ["${workspaceFolder}/out/**/*.js"],
    "preLaunchTask": "${workspaceFolder}/npm: watch"
}
```

### Common Debugging Scenarios

**GHCi Communication Issues**:
- Check GHCi path configuration
- Verify GHCi process startup in output
- Test Stack vs system GHCi

**Expression Parsing Problems**:
- Add logging to `editor.ts`
- Test cursor position and range detection
- Check multi-line vs single-line logic

**Boot Sequence Failures**:
- Verify TidalCycles installation
- Check custom boot file paths
- Review default boot code execution

### Performance Profiling

Use VSCode's built-in profiling:
1. Command Palette → "Developer: Reload With Extensions Profiling Enabled"
2. Use extension normally
3. Command Palette → "Developer: Show Running Extensions"
4. View performance data

---

## Quick Reference

### Key Files to Understand
1. `main.ts` - Start here for command registration
2. `repl.ts` - Core user interaction logic
3. `tidal.ts` - TidalCycles-specific functionality
4. `package.json` - Extension manifest and configuration

### Testing Checklist
- [ ] Single-line evaluation works
- [ ] Multi-line evaluation works
- [ ] Hush command works
- [ ] Visual feedback appears
- [ ] Custom boot files load
- [ ] Error handling works
- [ ] Cross-platform compatibility

### Release Checklist
- [ ] All tests pass
- [ ] Version number updated
- [ ] CHANGELOG.md updated
- [ ] README.md reviewed
- [ ] Extension packages without errors
- [ ] Manual testing in clean environment

Happy coding! 🎵