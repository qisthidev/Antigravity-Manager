# Building Antigravity Tools on MacBook Pro M1 with Sequoia macOS

This guide provides detailed instructions for building Antigravity Tools from source on Apple Silicon (M1/M2/M3) Macs running macOS Sequoia (15.x) or later.

> **Need a quick reference?** See [BUILD_QUICK_REFERENCE.md](BUILD_QUICK_REFERENCE.md) for condensed commands and common issues.

## Table of Contents

- [Prerequisites](#prerequisites)
- [System Requirements](#system-requirements)
- [Installation Steps](#installation-steps)
- [Building the Application](#building-the-application)
- [Running the Application](#running-the-application)
- [Troubleshooting](#troubleshooting)
- [Development Mode](#development-mode)

## Prerequisites

Before building Antigravity Tools, you need to install the following dependencies:

### 1. Xcode Command Line Tools

Xcode Command Line Tools are required for compiling native code on macOS.

```bash
xcode-select --install
```

If already installed, verify with:
```bash
xcode-select -p
```

Expected output: `/Applications/Xcode.app/Contents/Developer` or `/Library/Developer/CommandLineTools`

### 2. Homebrew (Package Manager)

Install Homebrew if you haven't already:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

After installation, ensure Homebrew is in your PATH (for Apple Silicon):
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 3. Rust and Cargo

Install Rust using rustup (the official Rust installer):

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Follow the prompts and select the default installation. After installation:

```bash
source $HOME/.cargo/env
```

Verify installation:
```bash
rustc --version
cargo --version
```

Add the ARM64 target for Apple Silicon:
```bash
rustup target add aarch64-apple-darwin
```

### 4. Node.js and npm

Install Node.js (version 20.x is recommended):

```bash
brew install node@20
```

Verify installation:
```bash
node --version  # Should show v20.x.x
npm --version   # Should show 10.x.x or higher
```

Alternatively, you can use [nvm](https://github.com/nvm-sh/nvm) for Node.js version management:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
# Restart your terminal or source your profile
nvm install 20
nvm use 20
```

## System Requirements

- **macOS Version**: macOS Sequoia (15.x) or later
- **Architecture**: Apple Silicon (M1, M2, M3, or M4)
- **RAM**: At least 8 GB (16 GB recommended for faster builds)
- **Disk Space**: At least 5 GB of free space
- **Xcode**: Version 15.0 or later (Command Line Tools)

## Installation Steps

### 1. Clone the Repository

```bash
git clone https://github.com/lbjlaq/Antigravity-Manager.git
cd Antigravity-Manager
```

### 2. Install Node.js Dependencies

```bash
npm install --legacy-peer-deps
```

> **Note**: The `--legacy-peer-deps` flag is required due to peer dependency conflicts in some packages.

### 3. Verify Rust Setup

Ensure all required Rust targets are installed:

```bash
rustup target list --installed
```

You should see `aarch64-apple-darwin` in the list. If not, add it:

```bash
rustup target add aarch64-apple-darwin
```

## Building the Application

### Option A: Build for M1/M2/M3 (ARM64) Only

This creates an optimized build specifically for Apple Silicon:

```bash
npm run tauri build -- --target aarch64-apple-darwin
```

The build process will:
1. Compile the React frontend with Vite
2. Compile the Rust backend with Cargo
3. Bundle everything into a macOS application

Build artifacts will be located at:
```
src-tauri/target/aarch64-apple-darwin/release/bundle/macos/Antigravity Tools.app
```

### Option B: Build Universal Binary (Intel + ARM)

To create a universal binary that runs on both Intel and Apple Silicon Macs:

```bash
# Add the Intel target
rustup target add x86_64-apple-darwin

# Build universal binary
npm run tauri build -- --target universal-apple-darwin
```

Build artifacts will be in:
```
src-tauri/target/universal-apple-darwin/release/bundle/macos/
```

### Option C: Quick Build (Development)

For faster builds during development (no optimizations):

```bash
npm run tauri build -- --debug --target aarch64-apple-darwin
```

## Running the Application

### From Build Artifacts

After building, you can run the application directly:

```bash
open src-tauri/target/aarch64-apple-darwin/release/bundle/macos/Antigravity\ Tools.app
```

Or install it to your Applications folder:

```bash
cp -r "src-tauri/target/aarch64-apple-darwin/release/bundle/macos/Antigravity Tools.app" /Applications/
```

### Open from Applications

Once installed, you can open it from:
- Spotlight: Press `Cmd + Space` and type "Antigravity Tools"
- Finder: Navigate to Applications and double-click "Antigravity Tools"
- Terminal: `open /Applications/Antigravity\ Tools.app`

## Development Mode

For active development with hot-reload:

### 1. Start the Development Server

```bash
npm run tauri dev
```

This will:
- Start the Vite development server for the frontend
- Compile and run the Tauri/Rust backend
- Open the application with hot-reload enabled
- Show debug logs in the terminal

### 2. Debug Mode with Enhanced Logging

Enable verbose Rust logging:

```bash
npm run tauri:debug
```

Or manually:
```bash
RUST_LOG=debug npm run tauri dev
```

## Troubleshooting

### Issue: "xcrun: error: unable to find utility"

**Solution**: Install or update Xcode Command Line Tools:
```bash
sudo rm -rf /Library/Developer/CommandLineTools
xcode-select --install
```

### Issue: "No prebuilt binaries found for arm64"

**Solution**: Some npm packages need to be rebuilt for ARM64:
```bash
npm rebuild
```

Or try using Rosetta 2 compatibility (not recommended):
```bash
arch -x86_64 npm install --legacy-peer-deps
```

### Issue: Build fails with "linking with `cc` failed"

**Solution**: Ensure you're targeting the correct architecture:
```bash
rustup target add aarch64-apple-darwin
rustup default stable
```

### Issue: "dyld: Library not loaded"

**Solution**: This usually indicates missing system libraries. Ensure Xcode Command Line Tools are properly installed:
```bash
sudo xcode-select --reset
xcode-select --install
```

### Issue: Permission denied when opening the app

**Solution**: macOS Gatekeeper might be blocking the app. You can:

1. Right-click the app and select "Open"
2. Or remove the quarantine attribute:
```bash
xattr -cr "/Applications/Antigravity Tools.app"
```

### Issue: npm install fails with peer dependency errors

**Solution**: Always use the `--legacy-peer-deps` flag:
```bash
npm install --legacy-peer-deps
```

### Issue: Rust compilation is very slow

**Solution**: 
1. Increase the parallelism:
```bash
# Add to ~/.cargo/config.toml
[build]
jobs = 8  # Adjust based on your CPU cores
```

2. Use a faster linker (mold for macOS):
```bash
brew install michaeleisel/zld/zld
```

Then add to `~/.cargo/config.toml`:
```toml
[target.aarch64-apple-darwin]
linker = "clang"
rustflags = ["-C", "link-arg=-fuse-ld=/opt/homebrew/bin/zld"]
```

### Issue: "Missing dependencies" during Tauri build

**Solution**: Clean and reinstall everything:
```bash
# Clean Cargo cache
cargo clean

# Remove node_modules
rm -rf node_modules package-lock.json

# Reinstall
npm install --legacy-peer-deps
```

### Issue: Frontend build fails

**Solution**: Ensure TypeScript compiles successfully first:
```bash
npm run build
```

If there are TypeScript errors, fix them before running `tauri build`.

## Additional Resources

- **Tauri Documentation**: https://tauri.app/
- **Rust Book**: https://doc.rust-lang.org/book/
- **React Documentation**: https://react.dev/
- **Project Repository**: https://github.com/lbjlaq/Antigravity-Manager

## Performance Tips

### 1. Enable Cargo Build Cache

Add to `~/.cargo/config.toml`:
```toml
[build]
incremental = true
```

### 2. Use sccache for Compilation Caching

```bash
brew install sccache
export RUSTC_WRAPPER=sccache
```

### 3. Optimize for M1 Specifically

Add to `src-tauri/Cargo.toml` under `[profile.release]`:
```toml
[profile.release]
opt-level = 3
lto = true
codegen-units = 1
```

## Build Time Estimates

On a MacBook Pro M1 with 16GB RAM:
- **First build**: 5-10 minutes
- **Incremental builds**: 1-3 minutes
- **Debug builds**: 2-5 minutes

## Next Steps

After successfully building the application:

1. Read the [main README](../README.md) for usage instructions
2. Check out the [API Reference](API_REFERENCE.md) for API integration
3. Explore the [advanced configuration](advanced_configuration.md) options

## Getting Help

If you encounter issues not covered in this guide:

1. Check existing [GitHub Issues](https://github.com/lbjlaq/Antigravity-Manager/issues)
2. Join the community discussions
3. Open a new issue with:
   - Your macOS version
   - Rust/Node.js versions
   - Complete error messages
   - Steps to reproduce

## Contributing

Contributions are welcome! If you've found better ways to build on M1 Macs, please submit a PR to improve this guide.
