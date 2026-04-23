# Quick Build Reference

This is a condensed quick reference for building Antigravity Tools. For detailed instructions, see platform-specific guides.

## Prerequisites Checklist

- [ ] Xcode Command Line Tools (macOS)
- [ ] Rust and Cargo (via rustup)
- [ ] Node.js 20.x
- [ ] npm (comes with Node.js)

## Quick Start Commands

### macOS (Apple Silicon M1/M2/M3)

```bash
# 1. Install prerequisites
xcode-select --install
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
brew install node@20

# 2. Add ARM64 target
rustup target add aarch64-apple-darwin

# 3. Clone and build
git clone https://github.com/lbjlaq/Antigravity-Manager.git
cd Antigravity-Manager
npm install --legacy-peer-deps
npm run tauri build -- --target aarch64-apple-darwin
```

### Universal Binary (Intel + ARM)

```bash
rustup target add x86_64-apple-darwin
npm run tauri build -- --target universal-apple-darwin
```

## Build Artifacts Location

**ARM64 Build:**
```
src-tauri/target/aarch64-apple-darwin/release/bundle/macos/Antigravity Tools.app
```

**Universal Build:**
```
src-tauri/target/universal-apple-darwin/release/bundle/macos/Antigravity Tools.app
```

## Development Mode

```bash
npm run tauri dev
```

## Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| "xcrun: error: unable to find utility" | `xcode-select --install` |
| npm install fails | Use `--legacy-peer-deps` flag |
| Linking with cc failed | `rustup target add aarch64-apple-darwin` |
| Permission denied on app | `xattr -cr "/path/to/Antigravity Tools.app"` |

## Detailed Guides

- [macOS M1/M2/M3 Complete Guide](BUILD_MACOS_M1.md)

## Build Times (Reference)

| Hardware | First Build | Incremental |
|----------|-------------|-------------|
| M1 Pro 16GB | 5-10 min | 1-3 min |
| M2 8GB | 7-12 min | 2-4 min |
| M3 Max | 3-6 min | <1 min |

## Essential npm Scripts

```bash
npm run dev          # Start Vite dev server
npm run build        # Build frontend (TypeScript + Vite)
npm run tauri dev    # Run in development mode
npm run tauri build  # Build production app
```

## System Architecture

```
┌─────────────────────────────────────┐
│  Frontend (React + TypeScript)      │
│  - Vite build system                │
│  - Modern UI components             │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  Tauri v2 Bridge                    │
│  - IPC communication                │
│  - Security layer                   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  Backend (Rust)                     │
│  - Native system integration        │
│  - Proxy server (Axum)              │
└─────────────────────────────────────┘
```

## Minimum Versions

- **macOS**: Sequoia (15.x) or later
- **Rust**: 1.70+
- **Node.js**: 20.x
- **npm**: 10.x
- **Xcode**: 15.0+

## Help & Support

- Detailed Guide: [BUILD_MACOS_M1.md](BUILD_MACOS_M1.md)
- Issues: [GitHub Issues](https://github.com/lbjlaq/Antigravity-Manager/issues)
- Main README: [README.md](../README.md)
