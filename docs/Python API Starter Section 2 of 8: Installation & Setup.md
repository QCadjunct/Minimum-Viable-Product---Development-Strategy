# ⚙️ Python API Starter - Complete Implementation Guide

**Section 2 of 8: Installation & Setup**

---

## 📑 Table of Contents

- [⚙️ Installation & Setup](#️-installation--setup)
  - [System Prerequisites](#-system-prerequisites)
  - [Environment Preparation](#-environment-preparation)
  - [Installation Methods](#-installation-methods)
  - [Dependency Installation Flow](#-dependency-installation-flow)
  - [Minimal Installation (HTTP Only)](#-minimal-installation-http-only)
  - [Full Installation (All Features)](#-full-installation-all-features)
  - [Custom Installation (Pick Features)](#-custom-installation-pick-features)
  - [Verification & Testing](#-verification--testing)
  - [First Application Run](#-first-application-run)
  - [Configuration Setup](#-configuration-setup)
  - [Development Tools Setup](#-development-tools-setup)
  - [Common Installation Issues](#-common-installation-issues)
  - [IDE Configuration](#-ide-configuration)

---

## 🔍 System Prerequisites

### Minimum Requirements

| Component | Requirement | Recommended | Why |
|-----------|-------------|-------------|-----|
| **Python** | 3.10+ | 3.11 or 3.12 | UUID7 support, async improvements |
| **pip** | 21.0+ | Latest | Modern dependency resolution |
| **OS** | Linux/macOS/Windows | Linux/macOS | Best ASGI performance |
| **RAM** | 512MB | 2GB+ | Comfortable development |
| **Disk** | 100MB | 500MB+ | Dependencies and cache |

### Check Your System

```bash
# Check Python version
python --version
# Should show: Python 3.10.x or higher

# Check pip version
pip --version
# Should show: pip 21.0 or higher

# Verify UUID7 support (Python 3.10+ only)
python -c "import uuid; print(uuid.uuid7())"
# Should print a UUID without error
```

### ✅ Prerequisites Checklist

Before proceeding, ensure:

- [ ] Python 3.10 or higher installed
- [ ] pip package manager available
- [ ] Virtual environment tool (venv or virtualenv)
- [ ] Git installed (for cloning repository)
- [ ] Text editor or IDE ready
- [ ] Terminal/command prompt access
- [ ] Internet connection for downloading packages

[↑ Back to TOC](#-table-of-contents)

---

## 🌍 Environment Preparation

### Why Use Virtual Environments?

Virtual environments isolate project dependencies, preventing conflicts between different projects.

```
Without venv:          With venv:
┌─────────────┐        ┌──────────┐  ┌──────────┐
│   System    │        │ Project  │  │ Project  │
│   Python    │        │  Venv A  │  │  Venv B  │
│             │        │          │  │          │
│ Package A v1│        │ Pkg A v1 │  │ Pkg A v2 │
│ Package B v2│        │ Pkg B v2 │  │ Pkg B v1 │
│             │        │          │  │          │
│ CONFLICTS!  │        │ ✅ Clean │  │ ✅ Clean │
└─────────────┘        └──────────┘  └──────────┘
```

### Create Virtual Environment

**Linux/macOS:**
```bash
# Navigate to your project directory
cd ~/projects

# Clone the repository (or create new directory)
git clone https://github.com/yourusername/python-api-starter.git
cd python-api-starter

# Create virtual environment
python -m venv venv

# Activate virtual environment
source venv/bin/activate

# Verify activation (should show venv path)
which python
```

**Windows:**
```cmd
# Navigate to your project directory
cd C:\projects

# Clone the repository (or create new directory)
git clone https://github.com/yourusername/python-api-starter.git
cd python-api-starter

# Create virtual environment
python -m venv venv

# Activate virtual environment
venv\Scripts\activate

# Verify activation
where python
```

### 🔄 Virtual Environment Commands

```bash
# Activate
source venv/bin/activate        # Linux/macOS
venv\Scripts\activate           # Windows

# Deactivate (when done)
deactivate

# Remove virtual environment
rm -rf venv                     # Linux/macOS
rmdir /s venv                   # Windows

# Upgrade pip in venv
pip install --upgrade pip
```

[↑ Back to TOC](#-table-of-contents)

---

## 📦 Installation Methods

Python API Starter offers three installation approaches based on your needs:

### Installation Strategy Decision Tree

```mermaid
%%{init: {'theme':'base', 'themeVariables': {'primaryColor':'#e3f2fd','primaryTextColor':'#0d47a1','primaryBorderColor':'#1976d2','lineColor':'#42a5f5','fontSize':'16px'}}}%%
graph TB
    subgraph START ["🎯 Installation Decision Point"]
        S1["🚀 <b>Ready to Install</b><br/>Python API Starter"]
    end

    subgraph QUESTIONS ["❓ Requirements Assessment"]
        Q1{"📊 <b>What features</b><br/>do you need?"}
        Q2{"💻 <b>Development or</b><br/>Production?"}
        Q3{"🔌 <b>Which protocols</b><br/>will you use?"}
    end

    subgraph MINIMAL ["🎯 Minimal Installation Path • FASTEST ⚡"]
        M1["✅ <b>HTTP REST only</b><br/>Learning/Testing"]
        M2["📦 <b>3 packages</b><br/>~50MB download"]
        M3["⚡ <b>30 second install</b><br/>Ready immediately"]
        M4["🎓 <b>Perfect for</b><br/>tutorials/learning"]
    end

    subgraph CUSTOM ["🔧 Custom Installation Path • BALANCED ⚖️"]
        C1["✅ <b>Choose specific features</b><br/>GraphQL OR gRPC"]
        C2["📦 <b>5-7 packages</b><br/>~150MB download"]
        C3["⚡ <b>2 minute install</b><br/>Targeted features"]
        C4["🎯 <b>Production apps</b><br/>Known requirements"]
    end

    subgraph FULL ["🚀 Full Installation Path • COMPLETE 💎"]
        F1["✅ <b>All features enabled</b><br/>HTTP+GraphQL+gRPC"]
        F2["📦 <b>15+ packages</b><br/>~300MB download"]
        F3["⚡ <b>5 minute install</b><br/>Complete ecosystem"]
        F4["🏢 <b>Enterprise apps</b><br/>Maximum flexibility"]
    end

    subgraph INSTALL ["💾 Installation Commands"]
        I1["<b>Minimal:</b><br/>pip install -r<br/>requirements-minimal.txt"]
        I2["<b>Custom:</b><br/>pip install -e<br/>graphql or grpc"]
        I3["<b>Full:</b><br/>pip install -r<br/>requirements.txt"]
    end

    subgraph VERIFY ["✅ Verification Steps"]
        V1["🧪 <b>Run basic tests</b><br/>Import packages"]
        V2["🎯 <b>Test examples</b><br/>Start dev server"]
        V3["📊 <b>Check dependencies</b><br/>pip list"]
    end

    S1 ==>|start| Q1
    Q1 ==>|assess| Q2
    Q2 ==>|decide| Q3
    Q3 ==>|minimal| M1
    Q3 ==>|custom| C1
    Q3 ==>|full| F1
    M1 --> M2
    M2 --> M3
    M3 --> M4
    C1 --> C2
    C2 --> C3
    C3 --> C4
    F1 --> F2
    F2 --> F3
    F3 --> F4
    M4 ==>|execute| I1
    C4 ==>|execute| I2
    F4 ==>|execute| I3
    I1 ==>|verify| V1
    I2 ==>|verify| V1
    I3 ==>|verify| V1
    V1 --> V2
    V2 --> V3

    classDef startClass fill:#1976d2,stroke:#0d47a1,stroke-width:5px,color:#ffffff
    classDef questionClass fill:#9c27b0,stroke:#6a1b9a,stroke-width:4px,color:#ffffff
    classDef minimalClass fill:#00897b,stroke:#00695c,stroke-width:3px,color:#ffffff
    classDef customClass fill:#1565c0,stroke:#0d47a1,stroke-width:3px,color:#ffffff
    classDef fullClass fill:#c62828,stroke:#b71c1c,stroke-width:3px,color:#ffffff
    classDef installClass fill:#f57c00,stroke:#e65100,stroke-width:4px,color:#ffffff
    classDef verifyClass fill:#5e35b1,stroke:#4527a0,stroke-width:5px,color:#ffffff
    
    class S1 startClass
    class Q1,Q2,Q3 questionClass
    class M1,M2,M3,M4 minimalClass
    class C1,C2,C3,C4 customClass
    class F1,F2,F3,F4 fullClass
    class I1,I2,I3 installClass
    class V1,V2,V3 verifyClass

```

### 📊 Installation Comparison

| Feature | Minimal | Custom | Full |
|---------|---------|--------|------|
| **Protocols** | HTTP only | HTTP + 1-2 others | HTTP + GraphQL + gRPC |
| **Download Size** | ~50MB | ~150MB | ~300MB |
| **Install Time** | 30 seconds | 2 minutes | 5 minutes |
| **Disk Space** | ~100MB | ~250MB | ~500MB |
| **Use Case** | Learning, MVP | Production (known needs) | Enterprise, exploration |
| **Plugins** | Caching only | Selected | All available |

[↑ Back to TOC](#-table-of-contents)

---

## 🎯 Dependency Installation Flow

### Package Dependency Tree

```mermaid
graph TB
    subgraph CORE ["🧠    Core    Framework    Dependencies"]
        C1[📦 starlette<br/>ASGI Framework<br/>~1MB]
        C2[📦 pydantic<br/>Validation Engine<br/>~2MB]
        C3[📦 uvicorn<br/>ASGI Server<br/>~1MB]
    end

    subgraph STARLETTE ["🔷    Starlette    Dependencies"]
        S1[anyio<br/>Async I/O<br/>~500KB]
        S2[typing-extensions<br/>Type hints<br/>~100KB]
    end

    subgraph PYDANTIC ["🔷    Pydantic    Dependencies"]
        P1[pydantic-core<br/>Rust validation<br/>~5MB]
        P2[annotated-types<br/>Metadata<br/>~50KB]
    end

    subgraph UVICORN ["🔷    Uvicorn    Dependencies"]
        U1[click<br/>CLI tool<br/>~500KB]
        U2[h11<br/>HTTP/1.1<br/>~300KB]
        U3[httptools<br/>Fast parser<br/>~200KB]
    end

    subgraph GRAPHQL ["🟣    GraphQL    Optional    Dependencies"]
        G1[📦 strawberry-graphql<br/>Schema engine<br/>~5MB]
        G2[graphql-core<br/>Query parsing<br/>~2MB]
        G3[python-dateutil<br/>Date handling<br/>~500KB]
    end

    subgraph GRPC ["🟠    gRPC    Optional    Dependencies"]
        R1[📦 grpcio<br/>Core library<br/>~10MB]
        R2[📦 grpcio-tools<br/>Code generation<br/>~5MB]
        R3[protobuf<br/>Serialization<br/>~2MB]
    end

    subgraph REDIS ["🔴    Redis    Optional    Dependencies"]
        RD1[📦 redis<br/>Client library<br/>~1MB]
        RD2[async-timeout<br/>Async utilities<br/>~50KB]
    end

    subgraph OBSERV ["📊    Observability    Optional    Dependencies"]
        O1[📦 opentelemetry-api<br/>Tracing API<br/>~500KB]
        O2[📦 prometheus-client<br/>Metrics<br/>~300KB]
        O3[opentelemetry-sdk<br/>Implementation<br/>~1MB]
    end

    %% Core to sub-dependencies - Blue
    C1 --> S1
    C1 --> S2
    C2 --> P1
    C2 --> P2
    C3 --> U1
    C3 --> U2
    C3 --> U3
    linkStyle 0 stroke:#1976d2,stroke-width:3px
    linkStyle 1 stroke:#1976d2,stroke-width:3px
    linkStyle 2 stroke:#1976d2,stroke-width:3px
    linkStyle 3 stroke:#1976d2,stroke-width:3px
    linkStyle 4 stroke:#1976d2,stroke-width:3px
    linkStyle 5 stroke:#1976d2,stroke-width:3px
    linkStyle 6 stroke:#1976d2,stroke-width:3px

    %% GraphQL dependencies - Purple
    G1 --> G2
    G1 --> G3
    linkStyle 7 stroke:#7b1fa2,stroke-width:3px
    linkStyle 8 stroke:#7b1fa2,stroke-width:3px

    %% gRPC dependencies - Orange
    R1 --> R3
    R2 --> R3
    linkStyle 9 stroke:#f57c00,stroke-width:3px
    linkStyle 10 stroke:#f57c00,stroke-width:3px

    %% Redis dependencies - Teal
    RD1 --> RD2
    linkStyle 11 stroke:#00695c,stroke-width:3px

    %% Observability dependencies - Green
    O1 --> O3
    linkStyle 12 stroke:#388e3c,stroke-width:3px

    %% Subgraph styling
    style CORE fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style STARLETTE fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style PYDANTIC fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style UVICORN fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style GRAPHQL fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style GRPC fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style REDIS fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style OBSERV fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000

    %% Node styling - Core
    style C1 fill:#e3f2fd,stroke:#1976d2,stroke-width:3px,color:#000
    style C2 fill:#e3f2fd,stroke:#1976d2,stroke-width:3px,color:#000
    style C3 fill:#e3f2fd,stroke:#1976d2,stroke-width:3px,color:#000
    
    style S1 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    style S2 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    
    style P1 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    style P2 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    
    style U1 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    style U2 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    style U3 fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:#000
    
    style G1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style G2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style G3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    
    style R1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style R2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style R3 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    
    style RD1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style RD2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    
    style O1 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style O2 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style O3 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
```

[↑ Back to TOC](#-table-of-contents)

---

## 🎯 Minimal Installation (HTTP Only)

### Perfect For: Learning, MVPs, HTTP-Only APIs

**What You Get:**
- ✅ HTTP REST endpoints
- ✅ Pydantic V2 validation
- ✅ UUID7 support
- ✅ In-memory caching
- ✅ OpenAPI documentation
- ✅ Request context

**What's Excluded:**
- ❌ GraphQL support
- ❌ gRPC support
- ❌ Redis caching
- ❌ Observability plugins

### Installation Steps

```bash
# 1. Ensure virtual environment is activated
source venv/bin/activate  # or venv\Scripts\activate on Windows

# 2. Upgrade pip (recommended)
pip install --upgrade pip

# 3. Install minimal dependencies
pip install -r requirements-minimal.txt

# Expected output:
# Collecting starlette>=0.35.0
# Collecting pydantic>=2.5.0
# Collecting uvicorn[standard]>=0.25.0
# ...
# Successfully installed starlette-0.35.1 pydantic-2.5.2 uvicorn-0.25.0 ...
```

### Verify Installation

```bash
# Check installed packages
pip list | grep -E "starlette|pydantic|uvicorn"

# Expected output:
# pydantic         2.5.2
# starlette        0.35.1
# uvicorn          0.25.0

# Test imports
python -c "from api_starter import APIStarter; print('✅ Success')"

# Expected output:
# ✅ Success
```

### Quick Test

```bash
# Run basic example
python examples/basic_http_api.py

# Expected output:
# ============================================================
# 🚀 Starting Basic HTTP API Example
# ============================================================
# 
# Endpoints:
#   GET    http://localhost:8000/
#   GET    http://localhost:8000/users
#   POST   http://localhost:8000/users
#   ...
# INFO:     Started server process [12345]
# INFO:     Waiting for application startup.
# INFO:     Application startup complete.
# INFO:     Uvicorn running on http://0.0.0.0:8000
```

[↑ Back to TOC](#-table-of-contents)

---

## 🚀 Full Installation (All Features)

### Perfect For: Enterprise Apps, Exploration, Maximum Flexibility

**What You Get:**
- ✅ Everything in minimal install
- ✅ GraphQL with Strawberry
- ✅ gRPC with Protocol Buffers
- ✅ Redis caching
- ✅ OpenTelemetry tracing
- ✅ Prometheus metrics
- ✅ Development tools (pytest, black, ruff, mypy)

### Installation Steps

```bash
# 1. Ensure virtual environment is activated
source venv/bin/activate

# 2. Upgrade pip
pip install --upgrade pip

# 3. Install all dependencies
pip install -r requirements.txt

# This will take 3-5 minutes and install 15+ packages
# Expected download: ~300MB
```

### Verify Installation

```bash
# Check all packages installed
pip list | grep -E "starlette|pydantic|uvicorn|strawberry|grpc|redis|opentelemetry|prometheus"

# Test all imports
python -c "
from api_starter import APIStarter
from api_starter.plugins import CachingPlugin, GraphQLPlugin, GRPCPlugin
print('✅ All imports successful')
"

# Expected output:
# ✅ All imports successful
```

### What Gets Installed

```
Core Framework (3 packages):
  ✅ starlette, pydantic, uvicorn

GraphQL Support (3 packages):
  ✅ strawberry-graphql, graphql-core, python-dateutil

gRPC Support (3 packages):
  ✅ grpcio, grpcio-tools, protobuf

Caching (2 packages):
  ✅ redis, async-timeout

Observability (4 packages):
  ✅ opentelemetry-api, opentelemetry-sdk,
  ✅ opentelemetry-instrumentation-starlette, prometheus-client

Development (5 packages):
  ✅ pytest, pytest-asyncio, httpx, black, ruff, mypy
```

[↑ Back to TOC](#-table-of-contents)

---

## 🔧 Custom Installation (Pick Features)

### Perfect For: Production Apps with Known Requirements

Install only the features you need using optional dependencies defined in `pyproject.toml`.

### Available Feature Sets

```bash
# GraphQL only
pip install -e ".[graphql]"

# gRPC only
pip install -e ".[grpc]"

# Redis caching
pip install -e ".[redis]"

# Observability (metrics + tracing)
pip install -e ".[observability]"

# Development tools
pip install -e ".[dev]"

# Combine multiple features
pip install -e ".[graphql,redis,dev]"

# All optional features
pip install -e ".[all]"
```

### Example: GraphQL + Redis Setup

```bash
# 1. Activate virtual environment
source venv/bin/activate

# 2. Install base + GraphQL + Redis
pip install -e ".[graphql,redis]"

# 3. Verify
python -c "
from api_starter.plugins import GraphQLPlugin, CachingPlugin
print('✅ GraphQL and Caching available')
"
```

### Example: Production Setup (HTTP + Redis + Observability)

```bash
# Install for production without GraphQL/gRPC
pip install -e ".[redis,observability]"

# Verify
python -c "
from api_starter.plugins import CachingPlugin
import opentelemetry
import prometheus_client
print('✅ Production dependencies ready')
"
```

[↑ Back to TOC](#-table-of-contents)

---

## ✅ Verification & Testing

### Comprehensive Verification Process

```mermaid
graph TB
    subgraph START ["🎯    Verification    Start    Point"]
        V1[🚀 Installation Complete<br/>Ready to Verify]
    end

    subgraph IMPORTS ["📥    Import    Tests    Stage"]
        I1[🔍 Test core imports<br/>api_starter package]
        I2[🔍 Test plugin imports<br/>Caching, GraphQL, gRPC]
        I3[🔍 Test model imports<br/>BaseModel, EntityModel]
        I4[✅ All imports pass<br/>No errors]
    end

    subgraph DEPS ["📦    Dependency    Check    Stage"]
        D1[📋 List installed packages<br/>pip list]
        D2[🔍 Verify versions<br/>Match requirements]
        D3[🔍 Check for conflicts<br/>pip check]
        D4[✅ Dependencies valid<br/>No conflicts]
    end

    subgraph EXAMPLES ["🧪    Example    Execution    Stage"]
        E1[▶️ Run basic HTTP example<br/>basic_http_api.py]
        E2[🌐 Server starts successfully<br/>Port 8000 listening]
        E3[📡 Test HTTP endpoints<br/>curl requests]
        E4[✅ Responses correct<br/>All endpoints work]
    end

    subgraph TESTS ["🧪    Test    Suite    Stage"]
        T1[▶️ Run pytest suite<br/>tests/test_basic.py]
        T2[✅ All tests pass<br/>No failures]
        T3[📊 Check coverage<br/>Core functionality]
        T4[✅ >80% coverage<br/>Well tested]
    end

    subgraph SUCCESS ["🎉    Verification    Complete"]
        S1[✅ Installation verified<br/>All systems go]
        S2[📚 Ready for development<br/>Start building]
        S3[🚀 Proceed to tutorials<br/>Section 3 & 4]
    end

    subgraph ERROR ["⚠️    Error    Handling    Path"]
        ERR1[❌ Import error detected]
        ERR2[🔧 Check Python version<br/>Must be 3.10+]
        ERR3[🔧 Reinstall dependencies<br/>pip install -r requirements.txt]
        ERR4[🔧 Check virtual env<br/>Ensure activated]
    end

    %% Main flow - Blue
    V1 --> I1
    linkStyle 0 stroke:#1976d2,stroke-width:3px

    %% Import tests - Purple
    I1 --> I2
    I2 --> I3
    I3 --> I4
    linkStyle 1 stroke:#7b1fa2,stroke-width:3px
    linkStyle 2 stroke:#7b1fa2,stroke-width:3px
    linkStyle 3 stroke:#7b1fa2,stroke-width:3px

    %% Dependency checks - Teal
    I4 --> D1
    D1 --> D2
    D2 --> D3
    D3 --> D4
    linkStyle 4 stroke:#00695c,stroke-width:3px
    linkStyle 5 stroke:#00695c,stroke-width:3px
    linkStyle 6 stroke:#00695c,stroke-width:3px
    linkStyle 7 stroke:#00695c,stroke-width:3px

    %% Example execution - Orange
    D4 --> E1
    E1 --> E2
    E2 --> E3
    E3 --> E4
    linkStyle 8 stroke:#f57c00,stroke-width:3px
    linkStyle 9 stroke:#f57c00,stroke-width:3px
    linkStyle 10 stroke:#f57c00,stroke-width:3px
    linkStyle 11 stroke:#f57c00,stroke-width:3px

    %% Test suite - Green
    E4 --> T1
    T1 --> T2
    T2 --> T3
    T3 --> T4
    linkStyle 12 stroke:#388e3c,stroke-width:3px
    linkStyle 13 stroke:#388e3c,stroke-width:3px
    linkStyle 14 stroke:#388e3c,stroke-width:3px
    linkStyle 15 stroke:#388e3c,stroke-width:3px

    %% Success - Indigo (final)
    T4 --> S1
    S1 --> S2
    S2 --> S3
    linkStyle 16 stroke:#3f51b5,stroke-width:4px
    linkStyle 17 stroke:#3f51b5,stroke-width:4px
    linkStyle 18 stroke:#3f51b5,stroke-width:4px

    %% Error paths - Pink (dashed)
    I1 -.-> ERR1
    I2 -.-> ERR1
    I3 -.-> ERR1
    ERR1 -.-> ERR2
    ERR2 -.-> ERR3
    ERR3 -.-> ERR4
    ERR4 -.-> V1
    linkStyle 19 stroke:#c2185b,stroke-width:2px
    linkStyle 20 stroke:#c2185b,stroke-width:2px
    linkStyle 21 stroke:#c2185b,stroke-width:2px
    linkStyle 22 stroke:#c2185b,stroke-width:2px
    linkStyle 23 stroke:#c2185b,stroke-width:2px
    linkStyle 24 stroke:#c2185b,stroke-width:2px
    linkStyle 25 stroke:#c2185b,stroke-width:2px

    %% Subgraph styling
    style START fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style IMPORTS fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style DEPS fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style EXAMPLES fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style TESTS fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    style SUCCESS fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style ERROR fill:#fef7f7,stroke:#c2185b,stroke-width:3px,color:#000

    %% Node styling
    style V1 fill:#e3f2fd,stroke:#1976d2,stroke-width:3px,color:#000
    
    style I1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style I2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style I3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style I4 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:3px,color:#000
    
    style D1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style D2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style D3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style D4 fill:#e0f2f1,stroke:#00695c,stroke-width:3px,color:#000
    
    style E1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style E2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style E3 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style E4 fill:#fff8e1,stroke:#f57c00,stroke-width:3px,color:#000
    
    style T1 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style T2 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style T3 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style T4 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    
    style S1 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style S2 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style S3 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    
    style ERR1 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style ERR2 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style ERR3 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style ERR4 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
```

### Step-by-Step Verification

#### 1. Test Core Imports

```bash
# Test basic framework import
python -c "
from api_starter import APIStarter, RequestContext, BaseModel
from api_starter.models import generate_id, EntityModel
print('✅ Core imports successful')
print(f'✅ Generated UUID7: {generate_id()}')
"

# Expected output:
# ✅ Core imports successful
# ✅ Generated UUID7: 018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f
```

#### 2. Verify Dependencies

```bash
# List all installed packages
pip list

# Check for dependency conflicts
pip check

# Expected output:
# No broken requirements found.

# View specific package versions
pip show starlette pydantic uvicorn
```

#### 3. Run Test Suite

```bash
# Install test dependencies if needed
pip install pytest pytest-asyncio

# Run all tests
pytest tests/ -v

# Expected output:
# tests/test_basic.py::TestAPIStarter::test_create_app PASSED
# tests/test_basic.py::TestAPIStarter::test_endpoint_decorator PASSED
# tests/test_basic.py::TestModels::test_base_model PASSED
# tests/test_basic.py::TestModels::test_entity_model PASSED
# tests/test_basic.py::TestModels::test_uuid7_generation PASSED
# tests/test_basic.py::TestValidation::test_field_validation PASSED
# tests/test_basic.py::TestValidation::test_email_validation PASSED
# tests/test_basic.py::TestPlugins::test_caching_plugin PASSED
# ==================== 8 passed in 2.15s ====================
```

[↑ Back to TOC](#-table-of-contents)

---

## 🚀 First Application Run

### Running the Basic Example

```bash
# Terminal 1: Start the server
python examples/basic_http_api.py

# You should see:
# ============================================================
# 🚀 Starting Basic HTTP API Example
# ============================================================
# 
# Endpoints:
#   GET    http://localhost:8000/
#   GET    http://localhost:8000/users
#   POST   http://localhost:8000/users
#   GET    http://localhost:8000/users/{user_id}
#   DELETE http://localhost:8000/users/{user_id}
# 
# Try these commands:
#   curl http://localhost:8000/
#   curl http://localhost:8000/users
#   curl -X POST http://localhost:8000/users -H "Content-Type: application/json" -d '{"name":"Alice","email":"alice@example.com","age":25}'
# 
# ============================================================
# 
# INFO:     Started server process [12345]
# INFO:     Waiting for application startup.
# INFO:     Application startup complete.
# INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

### Testing Endpoints

```bash
# Terminal 2: Test endpoints

# 1. Test root endpoint
curl http://localhost:8000/

# Expected response:
# {
#   "message": "Welcome to API Starter!",
#   "version": "1.0.0",
#   "endpoints": {
#     "users": "/users",
#     "health": "/health",
#     "docs": "/openapi.json"
#   }
# }

# 2. Create a user
curl -X POST http://localhost:8000/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Alice Smith","email":"alice@example.com","age":25}'

# Expected response:
# {
#   "message": "User created successfully",
#   "user": {
#     "id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f",
#     "name": "Alice Smith",
#     "email": "alice@example.com",
#     "age": 25,
#     "created_at": "2025-01-15T10:30:00.123456",
#     "updated_at": "2025-01-15T10:30:00.123456"
#   }
# }

# 3. List all users
curl http://localhost:8000/users

# Expected response:
# {
#   "items": [
#     {
#       "id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f",
#       "name": "Alice Smith",
#       "email": "alice@example.com",
#       "age": 25,
#       "created_at": "2025-01-15T10:30:00.123456",
#       "updated_at": "2025-01-15T10:30:00.123456"
#     }
#   ],
#   "total": 1,
#   "page": 1,
#   "page_size": 10,
#   "has_next": false
# }

# 4. Get specific user (use ID from create response)
curl http://localhost:8000/users/018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f

# 5. Health check
curl http://localhost:8000/health

# Expected response:
# {
#   "status": "healthy",
#   "version": "1.0.0"
# }

# 6. OpenAPI documentation
curl http://localhost:8000/openapi.json
```

### ✅ Success Indicators

If you see these, installation is successful:

- ✅ Server starts without errors
- ✅ Endpoints respond with JSON
- ✅ UUID7 IDs are generated
- ✅ Pydantic validation works (try invalid data)
- ✅ No import errors or warnings

[↑ Back to TOC](#-table-of-contents)

---

## ⚙️ Configuration Setup

### Basic Configuration File

Create a `config.yaml` for project-wide settings:

```yaml
# config.yaml
app:
  title: "My API"
  version: "1.0.0"
  debug: false
  description: "Production API with Python API Starter"

server:
  host: "0.0.0.0"
  port: 8000
  workers: 4
  reload: false  # Set to true for development

plugins:
  caching:
    enabled: true
    redis_url: null  # Use "redis://localhost:6379" for Redis
    default_ttl: 300
    
  graphql:
    enabled: false
    path: "/graphql"
    playground: true
  
  grpc:
    enabled: false
    port: 50051
    max_workers: 10

logging:
  level: "INFO"
  format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
```

### Load Configuration

```python
# app.py
from api_starter import APIStarter

# Load from config file
app = APIStarter.from_config("config.yaml")

# Or configure programmatically
app = APIStarter(
    title="My API",
    version="1.0.0",
    debug=False
)
```

### Environment Variables

Create a `.env` file for secrets:

```bash
# .env (do not commit to git!)
DATABASE_URL=postgresql://user:pass@localhost/mydb
REDIS_URL=redis://localhost:6379
SECRET_KEY=your-secret-key-here
API_KEY=your-api-key-here

# Development settings
DEBUG=true
LOG_LEVEL=DEBUG
```

Load with python-dotenv:

```bash
# Install dotenv
pip install python-dotenv

# In your code
from dotenv import load_dotenv
import os

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")
REDIS_URL = os.getenv("REDIS_URL")
```

[↑ Back to TOC](#-table-of-contents)

---

## 🛠️ Development Tools Setup

### Code Formatting with Black

```bash
# Install Black
pip install black

# Format all Python files
black api_starter/ examples/ tests/

# Check what would be formatted
black --check api_starter/

# Configure in pyproject.toml (already done)
# [tool.black]
# line-length = 100
# target-version = ["py310", "py311", "py312"]
```

### Linting with Ruff

```bash
# Install Ruff
pip install ruff

# Lint all files
ruff check api_starter/ examples/ tests/

# Auto-fix issues
ruff check api_starter/ --fix

# Configure in pyproject.toml (already done)
# [tool.ruff]
# line-length = 100
# select = ["E", "W", "F", "I", "B", "C4", "UP"]
```

### Type Checking with MyPy

```bash
# Install MyPy
pip install mypy

# Check types
mypy api_starter/

# Configure in pyproject.toml (already done)
# [tool.mypy]
# python_version = "3.10"
# warn_return_any = true
```

### Pre-commit Hooks

```bash
# Install pre-commit
pip install pre-commit

# Create .pre-commit-config.yaml
cat > .pre-commit-config.yaml << EOF
repos:
  - repo: https://github.com/psf/black
    rev: 23.12.0
    hooks:
      - id: black
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.9
    hooks:
      - id: ruff
        args: [--fix]
EOF

# Install hooks
pre-commit install

# Now Black and Ruff run automatically on git commit
```

[↑ Back to TOC](#-table-of-contents)

---

## ⚠️ Common Installation Issues

### Issue 1: Python Version Too Old

**Symptom:**
```
AttributeError: module 'uuid' has no attribute 'uuid7'
```

**Solution:**
```bash
# Check Python version
python --version

# Must be 3.10 or higher
# Install Python 3.10+ from python.org

# On macOS with Homebrew:
brew install python@3.11

# On Ubuntu:
sudo apt update
sudo apt install python3.11
```

### Issue 2: pip Not Found

**Symptom:**
```
bash: pip: command not found
```

**Solution:**
```bash
# Use python -m pip instead
python -m pip install --upgrade pip

# Or install pip
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

### Issue 3: Permission Denied

**Symptom:**
```
ERROR: Could not install packages due to an OSError: [Errno 13] Permission denied
```

**Solution:**
```bash
# DON'T use sudo pip install!
# Instead, use virtual environment:
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Or install in user directory:
pip install --user -r requirements.txt
```

### Issue 4: Port Already in Use

**Symptom:**
```
OSError: [Errno 48] Address already in use
```

**Solution:**
```bash
# Find process using port 8000
lsof -i :8000  # macOS/Linux
netstat -ano | findstr :8000  # Windows

# Kill the process
kill -9 <PID>  # macOS/Linux
taskkill /PID <PID> /F  # Windows

# Or use a different port
python examples/basic_http_api.py --port 8001
```

### Issue 5: Import Errors After Installation

**Symptom:**
```
ModuleNotFoundError: No module named 'api_starter'
```

**Solution:**
```bash
# Ensure you're in the project directory
cd python-api-starter

# Ensure virtual environment is activated
source venv/bin/activate  # Should see (venv) in prompt

# Install in development mode
pip install -e .

# Or add to PYTHONPATH
export PYTHONPATH="${PYTHONPATH}:$(pwd)"
```

### Issue 6: SSL Certificate Errors

**Symptom:**
```
SSL: CERTIFICATE_VERIFY_FAILED
```

**Solution:**
```bash
# macOS: Install certificates
/Applications/Python\ 3.11/Install\ Certificates.command

# Or temporarily disable SSL verification (not recommended for production)
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org -r requirements.txt
```

### Issue 7: Build Tools Missing (Windows)

**Symptom:**
```
error: Microsoft Visual C++ 14.0 or greater is required
```

**Solution:**
```
1. Download Visual Studio Build Tools from:
   https://visualstudio.microsoft.com/downloads/

2. Install "Desktop development with C++" workload

3. Restart terminal and try again
```

### Issue 8: Redis Connection Failed

**Symptom:**
```
redis.exceptions.ConnectionError: Error connecting to Redis
```

**Solution:**
```bash
# Option 1: Install and start Redis
# macOS:
brew install redis
brew services start redis

# Ubuntu:
sudo apt install redis-server
sudo systemctl start redis

# Option 2: Use in-memory caching instead
# In your code:
app.add_plugin(CachingPlugin())  # No redis_url = memory cache
```

[↑ Back to TOC](#-table-of-contents)

---

## 💻 IDE Configuration

### Visual Studio Code

**Install Extensions:**
1. Python (Microsoft)
2. Pylance (Microsoft)
3. Black Formatter
4. Ruff

**Settings (.vscode/settings.json):**
```json
{
  "python.defaultInterpreterPath": "${workspaceFolder}/venv/bin/python",
  "python.linting.enabled": true,
  "python.linting.ruffEnabled": true,
  "python.formatting.provider": "black",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true
  },
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.rulers": [100]
  }
}
```

### PyCharm

**Configure Interpreter:**
1. File → Settings → Project → Python Interpreter
2. Click gear icon → Add
3. Select "Existing environment"
4. Choose `venv/bin/python`

**Configure Black:**
1. File → Settings → Tools → Black
2. Check "On code reformat"
3. Check "On save"

**Configure Ruff:**
1. File → Settings → Tools → External Tools
2. Add new tool: "Ruff"
3. Program: `$ProjectFileDir$/venv/bin/ruff`
4. Arguments: `check $FilePath$`

[↑ Back to TOC](#-table-of-contents)

---

## 🎓 Installation Complete!

You now have Python API Starter fully installed and configured. Let's recap what you've accomplished:

### ✅ What You've Set Up

- ✅ Python 3.10+ environment verified
- ✅ Virtual environment created and activated
- ✅ Dependencies installed (minimal, custom, or full)
- ✅ Framework imports working correctly
- ✅ Test suite passing
- ✅ Example application running
- ✅ Development tools configured
- ✅ IDE set up for Python development

### 📊 Installation Summary

| Component | Status | Version |
|-----------|--------|---------|
| Python | ✅ Installed | 3.10+ |
| Virtual Environment | ✅ Active | venv |
| Starlette | ✅ Installed | 0.35+ |
| Pydantic V2 | ✅ Installed | 2.5+ |
| Uvicorn | ✅ Installed | 0.25+ |
| Optional Plugins | ⚙️ As needed | - |

### 🚀 Next Steps

**Section 3: Core Architecture Deep Dive**
- Understand how the framework works internally
- Learn the plugin system
- Master request lifecycle

**Section 4: Building Your First API**
- Create a real-world application
- Implement CRUD operations
- Add validation and error handling

**Section 5: Adding Enterprise Plugins**
- Enable caching for performance
- Add GraphQL for flexible queries
- Use gRPC for high-performance services

### 📚 Quick Reference Commands

```bash
# Activate environment
source venv/bin/activate  # or venv\Scripts\activate

# Run examples
python examples/basic_http_api.py
python examples/blog_api.py

# Run tests
pytest tests/ -v

# Format code
black api_starter/ examples/ tests/

# Lint code
ruff check api_starter/ --fix

# Check types
mypy api_starter/
```

### 🆘 Need Help?

If you encounter issues:
1. Check [Common Installation Issues](#-common-installation-issues) section above
2. Verify Python version: `python --version`
3. Check virtual environment: `which python`
4. View installed packages: `pip list`
5. Run diagnostics: `pip check`

[↑ Back to TOC](#-table-of-contents)

---

**📌 Note**: This is Section 2 of 8. Continue to Section 3 for a deep dive into the framework architecture.

**Ready to learn how the framework works?** The next section covers the core architecture, plugin system, and request lifecycle in detail.
