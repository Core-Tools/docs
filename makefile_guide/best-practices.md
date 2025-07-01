# HSU Universal Makefile System - Best Practices

Guidelines and recommendations for optimal use of the HSU Universal Makefile System.

## 🎯 **Core Principles**

### **1. Embrace Auto-Detection**
✅ **DO**: Let the system auto-detect your repository structure and language support
```make
# Minimal configuration - let auto-detection work
PROJECT_NAME := my-project
PROJECT_DOMAIN := example
INCLUDE_PREFIX := make/
```

❌ **DON'T**: Override defaults unless necessary
```make
# Unnecessary - system will auto-detect these
ENABLE_GO := yes
ENABLE_PYTHON := yes
REPO_TYPE := single-language-go
```

### **2. Use Intelligent Defaults**
✅ **DO**: Rely on extensive built-in defaults
```make
# These defaults are automatically applied:
# DEFAULT_PORT := 50055
# GO_BUILD_FLAGS := -v
# PYTHON_BUILD_TOOL := pip
# NUITKA_BUILD_MODE := onefile
```

❌ **DON'T**: Specify configuration that matches defaults
```make
# Redundant - these are already the defaults
DEFAULT_PORT := 50055
GO_BUILD_FLAGS := -v
```

### **3. Maintain Clean Separation**
✅ **DO**: Keep system files separate from project configuration
```
project/
├── Makefile                # include make/HSU_MAKEFILE_ROOT.mk
├── Makefile.config         # Project-specific settings only
└── make/                   # System files (never modify)
    └── HSU_MAKEFILE_*.mk
```

❌ **DON'T**: Modify system files directly
```make
# Never modify make/HSU_MAKEFILE_*.mk files
# Use Makefile.config for customization instead
```

## 📁 **Directory Structure Best Practices**

### **1. Follow Language Conventions**
✅ **DO**: Use standard language directory structures
```
# Go Projects
cmd/cli/mycli/main.go       # CLI executables
cmd/srv/mysrv/main.go       # Server executables
pkg/mylib/                  # Shared libraries
api/proto/                  # API definitions

# Python Projects  
cli/run_client.py           # CLI scripts
srv/run_server.py           # Server scripts
lib/mylib/                  # Shared libraries
api/proto/                  # API definitions
```

❌ **DON'T**: Use non-standard directory names
```
# Avoid non-standard structures
applications/client/        # Use cli/ instead
services/server/           # Use srv/ instead
modules/shared/            # Use lib/ instead
```

### **2. Maintain Consistent Naming**
✅ **DO**: Use consistent naming across projects
```
# Standard naming patterns
hsu-example1-go            # Single-language Go
hsu-example1-py            # Single-language Python
hsu-example2               # Multi-language
hsu-example3-common        # Multi-repo shared
hsu-example3-srv-go        # Multi-repo implementation
```

❌ **DON'T**: Use inconsistent naming patterns
```
# Avoid inconsistent patterns
my_go_project              # Use hyphens, not underscores
ProjectNameCamelCase       # Use lowercase with hyphens
randomNaming               # Use descriptive, consistent names
```

### **3. Organize APIs Consistently**
✅ **DO**: Keep APIs in shared locations
```
# Single-repo projects
api/proto/service.proto

# Multi-language projects  
api/proto/service.proto     # Shared between go/ and python/

# Multi-repo projects
common-repo/api/proto/      # Shared across implementation repos
```

## ⚙️ **Configuration Best Practices**

### **1. Minimal Configuration Philosophy**
✅ **DO**: Start with minimal configuration and add only as needed
```make
# Start here - works for 90% of projects
PROJECT_NAME := my-project
PROJECT_DOMAIN := example
INCLUDE_PREFIX := make/

# Add only if you need to override defaults
# ENABLE_NUITKA := yes
# GO_BUILD_FLAGS := -v -race
```

✅ **DO**: Document why you're overriding defaults
```make
# Nuitka configuration for production binary deployment
ENABLE_NUITKA := yes
NUITKA_OUTPUT_NAME := production-server
NUITKA_BUILD_MODE := onefile

# Custom Go flags for development builds with race detection
GO_BUILD_FLAGS := -v -race -ldflags="-s -w"
```

### **2. Domain Import Configuration**
✅ **DO**: Use domain imports for repository portability
```make
PROJECT_DOMAIN := echo
DOMAIN_IMPORT_PREFIX := github.com/core-tools/hsu-$(PROJECT_DOMAIN)
DOMAIN_REPLACE_TARGET := .

# This enables the same import paths across different repository layouts
```

✅ **DO**: Keep domain imports consistent across related projects
```make
# All echo-related projects use the same domain
PROJECT_DOMAIN := echo        # hsu-example1-go
PROJECT_DOMAIN := echo        # hsu-example1-py  
PROJECT_DOMAIN := echo        # hsu-example2
PROJECT_DOMAIN := echo        # hsu-example3-*
```

### **3. Nuitka Configuration**
✅ **DO**: Use production-ready Nuitka configuration
```make
# Production binary configuration
ENABLE_NUITKA := yes
NUITKA_OUTPUT_NAME := my-server
NUITKA_ENTRY_POINT := srv/run_server.py
NUITKA_EXCLUDES_FILE := nuitka_excludes.txt
NUITKA_BUILD_MODE := onefile

# Specify dependencies explicitly
NUITKA_EXTRA_PACKAGES := my_package my_package_simple
NUITKA_EXTRA_MODULES := srv.domain.handler
NUITKA_EXTRA_FOLLOW_IMPORTS := hsu_core
```

✅ **DO**: Use exclusion files for heavy dependencies
```bash
# nuitka_excludes.txt - exclude heavy ML dependencies
tensorflow
torch
scipy
sklearn
pandas
numpy
matplotlib
```

❌ **DON'T**: Use editable packages for Nuitka builds
```bash
# This will compile but fail at runtime
pip install -e .
make py-nuitka-build

# Use this instead for production builds
pip install .
make py-nuitka-build
```

## 🚀 **Development Workflow Best Practices**

### **1. Standard Development Cycle**
✅ **DO**: Follow a consistent development workflow
```bash
# Initial setup (once)
make setup

# Development cycle (repeated)
make check      # lint + test (fast feedback)
make build      # compile/package
make test       # comprehensive testing

# Before committing
make clean      # clean artifacts
make all        # complete build and check
```

### **2. Language-Specific Workflows**

#### **Go Development**
```bash
# Go-specific development cycle
make go-format  # Format code
make go-lint    # Check for issues
make go-test    # Run tests
make go-build   # Build binaries

# Continuous development
make go-watch   # Watch for changes

# Debugging
make go-lint-diag  # Diagnose issues
make go-lint-fix   # Attempt fixes
```

#### **Python Development**
```bash
# Python-specific development cycle
make py-install # Install dependencies
make py-format  # Format code
make py-lint    # Check for issues
make py-test    # Run tests

# Binary compilation
make py-nuitka-build  # Create standalone executable

# Continuous development
make py-watch   # Watch for changes
```

### **3. Multi-Language Coordination**
✅ **DO**: Coordinate multi-language builds properly
```bash
# Build protobuf stubs first
make proto

# Then build all languages
make build      # Builds both Go and Python

# Test each language independently
make go-test
make py-test

# Or test everything
make test       # Tests all enabled languages
```

## 🔧 **Cross-Platform Best Practices**

### **1. Leverage Auto-Detection**
✅ **DO**: Trust the system's cross-platform auto-detection
```bash
# Same commands work on all platforms
make build      # Windows, macOS, Linux
make test       # Handles platform differences automatically
make clean      # Cleans appropriate artifacts
```

✅ **DO**: Use `make info` to verify platform detection
```bash
# Check platform-specific settings
make info

# Example output shows platform adaptations:
# SHELL_TYPE: powershell (Windows) or bash (Unix)
# EXECUTABLE_SUFFIX: .exe (Windows) or empty (Unix)
# PATH_SEPARATOR: \ (Windows) or / (Unix)
```

### **2. Windows-MSYS Compatibility**
✅ **DO**: Use the system's Windows-MSYS support
```bash
# System automatically detects and adapts to:
# - PowerShell vs MSYS environments
# - Path separator differences (/ vs \)
# - Executable extensions (.exe vs none)
# - Shell command compatibility
```

❌ **DON'T**: Hardcode platform-specific commands in custom targets
```make
# Bad - hardcoded Windows paths
custom-target:
	.\cmd\srv\mysrv.exe

# Good - use auto-detection
custom-target: go-build-srv
	$(MAKE) go-run-srv
```

## 📦 **Deployment Best Practices**

### **1. Master → Replica Deployment**
✅ **DO**: Use true file replication for deployments
```bash
# Deploy from master location
cp docs/make/HSU_MAKEFILE_*.mk project/make/

# Or copy from any project (files are identical)
cp existing-project/make/HSU_MAKEFILE_*.mk new-project/make/
```

✅ **DO**: Verify deployment after copying
```bash
# Test system functionality
make help       # Verify system loads
make info       # Check configuration
make build      # Test build process
```

❌ **DON'T**: Modify system files during deployment
```bash
# Never do this - system files should remain pure
sed -i 's/old/new/' make/HSU_MAKEFILE_*.mk
```

### **2. Version Control Integration**
✅ **DO**: Version control project configuration, not system files
```bash
# Commit these files
git add Makefile
git add Makefile.config
git add nuitka_excludes.txt

# Don't commit system files (they're replicas)
echo "make/HSU_MAKEFILE_*.mk" >> .gitignore
```

✅ **DO**: Document system file versions in your project
```make
# Makefile.config
# HSU Universal Makefile System v1.1.0
# Last updated: 2024-12-29
# Source: docs/make/

PROJECT_NAME := my-project
PROJECT_DOMAIN := example
INCLUDE_PREFIX := make/
```

## 🧪 **Testing Best Practices**

### **1. Comprehensive Testing Strategy**
✅ **DO**: Use the system's multi-level testing
```bash
# Quick feedback during development
make check      # lint + basic tests

# Comprehensive testing before release
make test       # full test suite
make build      # ensure everything compiles
make clean      # verify clean builds work
```

### **2. Binary Testing**
✅ **DO**: Test binary builds in production-like environments
```bash
# Build production binary
pip install .          # Non-editable installation
make py-nuitka-build   # Create standalone executable

# Test the binary
./my-server.exe --help # Verify it runs
./my-server.exe &      # Start server
curl localhost:50055   # Test functionality
```

## 🎯 **Repository Portability Best Practices**

### **1. Maintain Implementation Independence**
✅ **DO**: Keep core business logic independent of repository structure
```
# Implementation code should be identical across approaches
srv/domain/handler.py    # Same in all repository layouts
cli/client.py           # Same in all repository layouts
lib/utils.py            # Same in all repository layouts
```

✅ **DO**: Use the same HSU makefile system across all approaches
```
# Same system files work in all repository layouts
make/HSU_MAKEFILE_*.mk  # Identical across all projects
```

### **2. Configuration Portability**
✅ **DO**: Use portable configuration patterns
```make
# These settings work across all repository approaches
PROJECT_DOMAIN := echo
DOMAIN_IMPORT_PREFIX := github.com/core-tools/hsu-$(PROJECT_DOMAIN)
DOMAIN_REPLACE_TARGET := .
```

## 🚨 **Common Pitfalls to Avoid**

### **1. Configuration Over-Specification**
❌ **DON'T**: Configure settings that auto-detection handles
```make
# Unnecessary - system auto-detects these
REPO_TYPE := single-language-go
GO_DIR := .
ENABLE_GO := yes
CLI_TARGETS := cmd/cli/mycli
SRV_TARGETS := cmd/srv/mysrv
```

### **2. System File Modification**
❌ **DON'T**: Modify system files directly
```make
# Never edit make/HSU_MAKEFILE_*.mk files
# They should remain pure replicas from master
```

### **3. Hardcoded Platform Dependencies**
❌ **DON'T**: Hardcode platform-specific paths or commands
```make
# Bad - hardcoded paths
custom-target:
	C:\Program Files\Go\bin\go.exe build

# Good - use system variables
custom-target:
	$(MAKE) go-build
```

### **4. Editable Packages with Nuitka**
❌ **DON'T**: Use editable packages for Nuitka builds
```bash
# This will cause runtime failures
pip install -e .
make py-nuitka-build

# This works correctly
pip install .
make py-nuitka-build
```

## 🏆 **Success Metrics**

### **1. Configuration Simplicity**
✅ Your `Makefile.config` should typically be under 10 lines for standard projects
✅ Most settings should use defaults (no explicit configuration needed)
✅ Override only when you have specific requirements that differ from defaults

### **2. Build Consistency**
✅ Same commands work identically across all developers and platforms
✅ `make build` succeeds on Windows, macOS, and Linux without changes
✅ No platform-specific documentation or setup required

### **3. Repository Portability**
✅ Implementation code remains unchanged when switching between HSU approaches
✅ Same build commands work regardless of repository layout
✅ Migration between approaches requires only configuration changes

---

**Navigation**:
- **Previous**: [Integration Examples](examples.md)
- **Next**: [Advanced Usage](advanced.md)
- **Index**: [Documentation Index](index.md) 