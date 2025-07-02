# Quick Usage Guide: Warp Terminal & VS Code

## 🚀 Installed Applications

- **Warp Terminal**: AI-integrated terminal with modern features
- **Visual Studio Code**: Professional code editor with full extension support

---

## 🖥️ Warp Terminal Usage

### Basic Commands
```bash
# Launch Warp in current directory
warp

# Launch Warp in current directory (explicit)
warp .

# Launch Warp in specific directory
warp /path/to/directory

# Original command (also works)
warp-terminal
```

### Features
- **AI Integration**: Built-in AI assistance for commands
- **Smart Autocomplete**: Context-aware suggestions
- **Modern UI**: Beautiful, fast terminal interface
- **Command History**: Advanced history search and management

---

## 💻 VS Code Usage

### Basic Commands
```bash
# Launch VS Code
code

# Open current directory in VS Code
code .

# Open specific file
code filename.txt

# Open specific directory/project
code /path/to/project

# Check version
code --version
```

### Quick Tips
- **Extensions**: Install from marketplace for enhanced functionality
- **Integrated Terminal**: Access terminal within VS Code
- **Multi-root Workspaces**: Open multiple project folders
- **Command Palette**: `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac)

---

## 🔧 Workflow Examples

### Development Workflow
```bash
# Navigate to project
cd ~/my-project

# Open project in VS Code
code .

# Open terminal in same directory with Warp
warp .
```

### Quick File Editing
```bash
# Edit configuration file
code ~/.bashrc

# Edit project file
code src/main.py
```

### Terminal + Editor Combo
```bash
# Terminal work in Warp
warp ~/projects/myapp

# Edit files in VS Code
code ~/projects/myapp
```

---

## 📦 Installation Details

- **Installed via**: AUR (Arch User Repository)
- **Package Manager**: paru
- **Packages**:
  - `warp-terminal-bin` (v0.2025.06.25.08.12.stable_01-1)
  - `visual-studio-code-bin` (v1.101.2-1)

---

## 🛠️ Advanced Usage

### Warp Terminal
- Sign in for AI features and cloud sync
- Customize themes and appearance
- Set up shell integrations
- Configure workflows and scripts

### VS Code
- Install language extensions (Python, JavaScript, etc.)
- Configure settings sync across devices  
- Set up debugging configurations
- Use source control integration (Git)

---

## 📋 Verification Commands

```bash
# Check installations
which warp code

# Version information
warp-terminal --version 2>/dev/null | head -5
code --version

# Package status
pacman -Qi warp-terminal-bin visual-studio-code-bin
```

---

## 🎯 Quick Start Checklist

- [ ] Test `warp` command in terminal
- [ ] Test `code` command in terminal  
- [ ] Try `warp .` to open Warp in current directory
- [ ] Try `code .` to open VS Code in current directory
- [ ] Verify desktop launchers work
- [ ] Sign into Warp for AI features (optional)
- [ ] Install VS Code extensions as needed

**Installation Date**: July 3, 2025  
**Status**: ✅ Fully Operational