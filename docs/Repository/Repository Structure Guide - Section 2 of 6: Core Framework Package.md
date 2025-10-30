# 📁 Python API Starter - Repository Structure Guide

**Section 2 of 6: Core Framework Package**

---

## 📑 Table of Contents

- [Core Framework Structure](#-core-framework-structure)
- [Core Module Documentation](#-core-module-documentation)
- [Models Package](#-models-package)
- [Utils Package](#-utils-package)
- [Plugins Package](#-plugins-package)

---

## 🏗️ Core Framework Structure

### Complete Core Package Tree

```
api_starter/
├── __init__.py                                # Package exports
├── __version__.py                             # Version management
├── py.typed                                   # Type checking marker
│
├── core/                                      # Core framework
│   ├── __init__.py
│   ├── application.py                         # Main application class
│   ├── context.py                             # Request context
│   ├── router.py                              # URL routing
│   ├── middleware.py                          # Middleware system
│   ├── plugin.py                              # Plugin architecture
│   ├── response.py                            # Response handlers
│   └── exceptions.py                          # Core exceptions
│
├── models/                                    # Base models
│   ├── __init__.py
│   ├── base.py                                # Base Pydantic models
│   ├── entity.py                              # Entity model
│   ├── response.py                            # Response models
│   └── exceptions.py                          # Model exceptions
│
├── utils/                                     # Utilities
│   ├── __init__.py
│   ├── logging.py                             # Logging utilities
│   ├── validation.py                          # Validation helpers
│   ├── serialization.py                       # JSON/serialization
│   ├── datetime.py                            # Date/time helpers
│   └── async_helpers.py                       # Async utilities
│
├── plugins/                                   # Built-in plugins
│   ├── __init__.py
│   ├── base.py                                # Plugin base class
│   ├── cors.py                                # CORS plugin
│   ├── timing.py                              # Timing middleware
│   └── error_handler.py                       # Error handling
│
├── middleware/                                # Middleware implementations
│   ├── __init__.py
│   ├── base.py                                # Middleware base
│   ├── authentication.py                      # Auth middleware
│   ├── rate_limit.py                          # Rate limiting
│   └── compression.py                         # Response compression
│
└── testing/                                   # Testing utilities
