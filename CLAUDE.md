# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Essential Commands

### Development
- `npm install` - Install dependencies
- `npm run compile` - Compile TypeScript to JavaScript
- `npm run watch` - Watch mode compilation for active development
- `npm test` - Run unit tests
- `npm run package` - Package extension as .vsix file

### Testing
- Press `F5` in VSCode to launch Extension Development Host for testing
- Create `.tidal` files to test functionality
- Check "TidalCycles" output channel for debugging

## Architecture Overview

This is a VSCode/Cursor extension that provides TidalCycles live coding integration. The architecture follows a modular pattern with clear separation of concerns:

### Core Data Flow
```
User Input (Shift+Enter) → Command Registration → REPL.evaluate() →
TidalEditor.getTidalExpressionUnderCursor() → Tidal.sendTidalExpression() →
Ghci.writeLn() → GHCi Process → SuperDirt/Audio
```

### Key Components

**main.ts** - Extension entry point that registers three commands:
- `tidal.eval` (Shift+Enter) - single line evaluation
- `tidal.evalMulti` (Ctrl+Enter) - multi-line block evaluation
- `tidal.hush` (Ctrl+Alt+H) - silence all patterns

**repl.ts** - Orchestrates user interactions. Only operates on `.tidal` files. Provides visual feedback by temporarily highlighting evaluated code with configurable colors.

**tidal.ts** - Contains comprehensive TidalCycles boot sequence and manages expression sending. Uses `:{` ... `:}` blocks for multi-line expressions. Supports both default boot code and custom boot files.

**ghci.ts** - Manages GHCi subprocess. Supports both system GHCi and Stack-managed GHCi. Handles cross-platform process spawning and stream management.

**editor.ts** - Text processing utilities. `getTidalExpressionUnderCursor()` intelligently detects expression boundaries using blank lines as delimiters.

**config.ts** - Type-safe access to VSCode settings with prefix `tidalcycles.*`

### Interface-Based Design
Core components implement interfaces (`ITidal`, `IGhci`, `IRepl`) for testability using TypeMoq mocking.

## Key Implementation Details

### Expression Detection Logic
- Single-line: Evaluates current line if non-empty
- Multi-line: Searches backwards/forwards from cursor to find blank line boundaries
- Only operates on `.tidal` file extensions

### GHCi Integration
- Spawns either `ghci` or `stack ghci` based on `useStackGhci` setting
- Uses `-XOverloadedStrings` extension by default
- Streams stdout/stderr for logging and debugging

### Boot System
- Default boot sequence sets up TidalCycles context, SuperDirt connection, pattern functions (d1-d16), and transition functions
- Custom boot files supported via `bootTidalPath` or `useBootFileInCurrentDirectory`
- Boot sequence runs once per REPL instance

### Visual Feedback
- Highlights evaluated code with configurable color (`feedbackColor` setting)
- Feedback duration: 250ms
- Uses VSCode text decoration API

## File Structure Notes

- **src/** - TypeScript source (compiles to **out/**)
- **test/** - Unit tests using Mocha + Chai + TypeMoq
- **package.json** - Extension manifest with command definitions and configuration schema
- **README.md** - User documentation
- **DEV.md** - Developer documentation

## Testing Strategy

Tests mock VSCode APIs using TypeMoq. Key test areas:
- Expression parsing with various cursor positions
- REPL command execution flow
- Configuration validation
- Error handling scenarios

Run single test file: `npm test -- --grep "test name"`

## Extension Compatibility

Supports both VSCode (^1.23.0) and Cursor IDE (^0.1.0). No platform-specific code - handles Windows/macOS/Linux path differences in configuration.