# 📁 Python API Starter - Repository Structure Guide

**Section 4 of 6: Documentation Structure**

---

## 📑 Table of Contents

- [Documentation Directory Structure](#-documentation-directory-structure)
- [Sphinx Configuration](#-sphinx-configuration)
- [API Reference Documentation](#-api-reference-documentation)
- [User Guide & Tutorials](#-user-guide--tutorials)
- [README Templates](#-readme-templates)
- [Contributing Guidelines](#-contributing-guidelines)

---

## 📚 Documentation Directory Structure

### Complete Documentation Tree

```
docs/
├── index.md                                   # Documentation home
├── requirements.txt                           # Docs dependencies
├── conf.py                                    # Sphinx configuration
├── mkdocs.yml                                 # MkDocs configuration (alternative)
│
├── getting-started/                           # Getting started guide
│   ├── installation.md
│   ├── quickstart.md
│   ├── first-api.md
│   └── configuration.md
│
├── user-guide/                                # User documentation
│   ├── index.md
│   ├── application.md                        # Application setup
│   ├── routing.md                            # URL routing
│   ├── middleware.md                         # Middleware guide
│   ├── plugins.md                            # Plugin system
│   ├── request-handling.md                   # Request processing
│   ├── response-handling.md                  # Response formatting
│   ├── validation.md                         # Data validation
│   ├── error-handling.md                     # Error management
│   └── testing.md                            # Testing guide
│
├── advanced/                                  # Advanced topics
│   ├── index.md
│   ├── async-patterns.md                     # Async programming
│   ├── database-integration.md               # Database setup
│   ├── caching.md                            # Caching strategies
│   ├── authentication.md                     # Auth implementation
│   ├── authorization.md                      # RBAC & permissions
│   ├── websockets.md                         # WebSocket support
│   ├── background-tasks.md                   # Celery integration
│   ├── monitoring.md                         # Observability
│   └── deployment.md                         # Production deployment
│
├── api-reference/                             # API documentation
│   ├── index.md
│   ├── core/
│   │   ├── application.md
│   │   ├── context.md
│   │   ├── router.md
│   │   ├── middleware.md
│   │   └── plugin.md
│   ├── models/
│   │   ├── base.md
│   │   └── entity.md
│   ├── utils/
│   │   ├── logging.md
│   │   └── validation.md
│   └── plugins/
│       ├── cors.md
│       ├── timing.md
│       └── database.md
│
├── tutorials/                                 # Step-by-step tutorials
│   ├── index.md
│   ├── todo-api/                             # Complete Todo API
│   │   ├── 01-setup.md
│   │   ├── 02-models.md
│   │   ├── 03-database.md
│   │   ├── 04-endpoints.md
│   │   ├── 05-authentication.md
│   │   ├── 06-testing.md
│   │   └── 07-deployment.md
│   ├── rest-api-design.md                    # REST best practices
│   ├── graphql-integration.md                # GraphQL tutorial
│   └── microservices.md                      # Microservices guide
│
├── examples/                                  # Code examples
│   ├── basic-crud.md
│   ├── authentication.md
│   ├── file-upload.md
│   ├── streaming.md
│   └── websockets.md
│
├── architecture/                              # Architecture docs
│   ├── overview.md                           # System architecture
│   ├── design-decisions.md                   # Design rationale
│   ├── performance.md                        # Performance guide
│   └── security.md                           # Security architecture
│
├── contributing/                              # Contribution guide
│   ├── index.md
│   ├── development-setup.md
│   ├── code-style.md
│   ├── testing-guide.md
│   └── pull-requests.md
│
└── changelog/                                 # Version history
    ├── index.md
    ├── v1.0.0.md
    └── unreleased.md
```

---

## 🔧 Sphinx Configuration

### `docs/conf.py`

```python
"""
Sphinx configuration for API Starter documentation.

This configuration sets up the documentation build process including:
- Theme configuration
- Extension setup
- API documentation generation
- Code highlighting

Build docs with: sphinx-build -b html docs docs/_build
"""

import os
import sys
from datetime import datetime

# Add parent directory to path for autodoc
sys.path.insert(0, os.path.abspath('..'))

# -- Project information -----------------------------------------------------

project = 'API Starter'
copyright = f'{datetime.now().year}, Your Name'
author = 'Your Name'

# The full version, including alpha/beta/rc tags
release = '1.0.0'
version = '1.0'

# -- General configuration ---------------------------------------------------

# Add any Sphinx extension module names here
extensions = [
    'sphinx.ext.autodoc',           # Auto-generate API docs
    'sphinx.ext.napoleon',          # Google/NumPy docstring support
    'sphinx.ext.viewcode',          # Add source code links
    'sphinx.ext.intersphinx',       # Link to other projects
    'sphinx.ext.todo',              # TODO notes
    'sphinx_autodoc_typehints',     # Type hint support
    'myst_parser',                  # Markdown support
    'sphinx_copybutton',            # Copy button for code blocks
    'sphinx_tabs.tabs',             # Tabbed content
]

# Add any paths that contain templates here
templates_path = ['_templates']

# List of patterns to ignore when looking for source files
exclude_patterns = ['_build', 'Thumbs.db', '.DS_Store']

# The suffix(es) of source filenames
source_suffix = {
    '.rst': 'restructuredtext',
    '.md': 'markdown',
}

# The master toctree document
master_doc = 'index'

# -- Options for HTML output -------------------------------------------------

# The theme to use for HTML and HTML Help pages
html_theme = 'furo'  # Modern, clean theme

# Theme options
html_theme_options = {
    "sidebar_hide_name": False,
    "light_logo": "logo-light.png",
    "dark_logo": "logo-dark.png",
    "light_css_variables": {
        "color-brand-primary": "#2962ff",
        "color-brand-content": "#2962ff",
    },
    "dark_css_variables": {
        "color-brand-primary": "#448aff",
        "color-brand-content": "#448aff",
    },
}

# Add any paths that contain custom static files
html_static_path = ['_static']

# Custom CSS files
html_css_files = [
    'custom.css',
]

# Logo
html_logo = "_static/logo.png"
html_favicon = "_static/favicon.ico"

# -- Extension configuration -------------------------------------------------

# Autodoc settings
autodoc_default_options = {
    'members': True,
    'member-order': 'bysource',
    'special-members': '__init__',
    'undoc-members': True,
    'exclude-members': '__weakref__',
    'show-inheritance': True,
}

autodoc_typehints = 'description'
autodoc_class_signature = 'separated'

# Napoleon settings (for Google/NumPy style docstrings)
napoleon_google_docstring = True
napoleon_numpy_docstring = True
napoleon_include_init_with_doc = True
napoleon_include_private_with_doc = False
napoleon_include_special_with_doc = True
napoleon_use_admonition_for_examples = True
napoleon_use_admonition_for_notes = True
napoleon_use_admonition_for_references = True
napoleon_use_ivar = False
napoleon_use_param = True
napoleon_use_rtype = True
napoleon_preprocess_types = True
napoleon_type_aliases = None
napoleon_attr_annotations = True

# Intersphinx mapping
intersphinx_mapping = {
    'python': ('https://docs.python.org/3', None),
    'starlette': ('https://www.starlette.io/', None),
    'pydantic': ('https://docs.pydantic.dev/latest/', None),
}

# MyST Parser settings
myst_enable_extensions = [
    "colon_fence",      # ::: fences
    "deflist",          # Definition lists
    "substitution",     # Variable substitution
    "tasklist",         # Task lists
]

# Copy button settings
copybutton_prompt_text = r">>> |\.\.\. |\$ |In \[\d*\]: | {2,5}\.\.\.: | {5,8}: "
copybutton_prompt_is_regexp = True

# Todo extension settings
todo_include_todos = True
```

### `docs/requirements.txt`

```txt
# Documentation dependencies
sphinx>=7.0.0
sphinx-autodoc-typehints>=1.24.0
sphinx-copybutton>=0.5.2
sphinx-tabs>=3.4.1
furo>=2023.9.10              # Modern Sphinx theme
myst-parser>=2.0.0           # Markdown support
```

---

## 📖 API Reference Documentation

### `docs/api-reference/core/application.md`

```markdown
# Application

The core application class for the API Starter framework.

## Overview

The `APIStarter` class is the main entry point for building APIs with the framework. It provides a FastAPI-inspired interface built on ASGI/Starlette.

## Class Reference

::: api_starter.core.application.APIStarter
    options:
      show_root_heading: true
      show_source: true
      members:
        - __init__
        - endpoint
        - add_middleware
        - add_plugin
        - on_startup
        - on_shutdown
        - run

## Basic Usage

```python
from api_starter import APIStarter, RequestContext

# Create application
app = APIStarter(
    title="My API",
    version="1.0.0",
    description="A production-ready API"
)

# Define endpoint
@app.endpoint("/hello", methods=["GET"])
async def hello(ctx: RequestContext):
    """Simple hello endpoint."""
    return {"message": "Hello, World!"}

# Run application
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

## Configuration

### Application Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `title` | `str` | `"API Starter"` | API title for documentation |
| `version` | `str` | `"1.0.0"` | API version (semantic versioning) |
| `description` | `str` | `"API built with..."` | API description |
| `debug` | `bool` | `False` | Enable debug mode |
| `docs_url` | `str` | `"/docs"` | Swagger UI URL (None to disable) |
| `redoc_url` | `str` | `"/redoc"` | ReDoc URL (None to disable) |
| `openapi_url` | `str` | `"/openapi.json"` | OpenAPI schema URL |

## Examples

### Full Application Setup

```python
from api_starter import APIStarter
from api_starter.middleware import TimingMiddleware
from api_starter.plugins import DatabasePlugin, CachePlugin

# Initialize application
app = APIStarter(
    title="Task Management API",
    version="2.0.0",
    description="Comprehensive task management system",
    debug=False
)

# Add middleware
app.add_middleware(TimingMiddleware())

# Add plugins
app.add_plugin(DatabasePlugin(
    database_url="postgresql://localhost/mydb"
))
app.add_plugin(CachePlugin(
    redis_url="redis://localhost:6379/0"
))

# Startup handler
@app.on_startup
async def startup():
    print("Application starting...")

# Shutdown handler
@app.on_shutdown
async def shutdown():
    print("Application shutting down...")
```

### Multiple Endpoints

```python
from pydantic import BaseModel

class Task(BaseModel):
    title: str
    completed: bool = False

@app.endpoint("/tasks", methods=["GET"])
async def list_tasks(ctx: RequestContext):
    """List all tasks."""
    return {"tasks": []}

@app.endpoint("/tasks", methods=["POST"])
async def create_task(ctx: RequestContext, task: Task):
    """Create a new task."""
    return {"task": task.model_dump()}, 201

@app.endpoint("/tasks/{task_id}", methods=["GET"])
async def get_task(ctx: RequestContext, task_id: str):
    """Get task by ID."""
    return {"task_id": task_id}
```

## Lifecycle Hooks

The application supports lifecycle hooks for initialization and cleanup:

```python
@app.on_startup
async def startup():
    """Called when application starts."""
    # Initialize database connections
    await db.connect()
    # Warm up caches
    await cache.warm()

@app.on_shutdown
async def shutdown():
    """Called when application stops."""
    # Close database connections
    await db.disconnect()
    # Flush caches
    await cache.flush()
```

## See Also

- [RequestContext](context.md) - Request context management
- [Middleware](middleware.md) - Middleware system
- [Plugins](plugin.md) - Plugin architecture
- [Router](router.md) - URL routing
```

### `docs/api-reference/core/context.md`

```markdown
# Request Context

Request context for state management throughout the request lifecycle.

## Overview

The `RequestContext` class carries information about the current request through middleware, plugins, and endpoint handlers. It provides convenient access to request data and allows storing custom state.

## Class Reference

::: api_starter.core.context.RequestContext
    options:
      show_root_heading: true
      show_source: true

## Basic Usage

```python
@app.endpoint("/users/{user_id}", methods=["GET"])
async def get_user(ctx: RequestContext, user_id: str):
    # Access request properties
    method = ctx.method          # "GET"
    path = ctx.path              # "/users/123"
    
    # Get headers
    auth_header = ctx.headers.get("authorization")
    
    # Get query parameters
    page = ctx.query.get("page", "1")
    
    # Store custom data
    ctx.set("start_time", time.time())
    
    return {"user_id": user_id}
```

## Properties

### HTTP Request Properties

| Property | Type | Description |
|----------|------|-------------|
| `method` | `str` | HTTP method (GET, POST, etc.) |
| `path` | `str` | Request path without query string |
| `headers` | `Headers` | Request headers (case-insensitive) |
| `query` | `QueryParams` | Query parameters (multi-dict) |
| `path_params` | `dict` | Path parameters from route |
| `client_ip` | `str` | Client IP address |
| `url` | `str` | Complete request URL |

### State Management

```python
# Store data
ctx.set("user_id", "123")
ctx.set("cache", redis_client)

# Retrieve data
user_id = ctx.get("user_id")
cache = ctx.get("cache", default_cache)

# Check existence
if ctx.has("user_id"):
    user_id = ctx.get("user_id")
```

## Methods

### Accessing Request Body

#### JSON Body

```python
@app.endpoint("/api/data", methods=["POST"])
async def handle_json(ctx: RequestContext):
    """Handle JSON request body."""
    data = await ctx.json()
    name = data.get("name")
    return {"received": name}
```

#### Form Data

```python
@app.endpoint("/submit", methods=["POST"])
async def handle_form(ctx: RequestContext):
    """Handle form submission."""
    form = await ctx.form()
    username = form.get("username")
    return {"username": username}
```

#### Raw Body

```python
@app.endpoint("/upload", methods=["POST"])
async def handle_upload(ctx: RequestContext):
    """Handle raw body data."""
    body = await ctx.body()
    size = len(body)
    return {"size": size}
```

## Examples

### Authentication Middleware

```python
class AuthMiddleware(Middleware):
    """Add user info to context."""
    
    async def process_request(self, ctx: RequestContext):
        # Get token from header
        token = ctx.headers.get("authorization")
        
        if token:
            # Verify and extract user
            user_id = verify_token(token)
            
            # Store in context
            ctx.set("user_id", user_id)
            ctx.set("authenticated", True)
        else:
            ctx.set("authenticated", False)
```

### Using Context State

```python
@app.endpoint("/profile", methods=["GET"])
async def get_profile(ctx: RequestContext):
    """Get user profile (requires auth)."""
    
    # Check authentication
    if not ctx.get("authenticated", False):
        return {"error": "Unauthorized"}, 401
    
    # Get user ID from context
    user_id = ctx.get("user_id")
    
    # Fetch profile
    profile = await user_service.get_profile(user_id)
    
    return {"profile": profile}
```

### Timing Requests

```python
class TimingMiddleware(Middleware):
    """Track request duration."""
    
    async def process_request(self, ctx: RequestContext):
        ctx.set("start_time", time.time())
    
    async def process_response(self, ctx: RequestContext, response):
        start_time = ctx.get("start_time")
        duration = time.time() - start_time
        
        # Add header
        response.headers["X-Process-Time"] = f"{duration:.3f}"
        
        return response
```

## See Also

- [Application](application.md) - Main application class
- [Middleware](middleware.md) - Middleware system
- [Router](router.md) - URL routing
```

---

## 📚 User Guide & Tutorials

### `docs/user-guide/quickstart.md`

```markdown
# Quick Start Guide

Get up and running with API Starter in 5 minutes.

## Installation

### Using pip

```bash
# Install from PyPI
pip install api-starter

# Or install with all extras
pip install api-starter[all]

# Or install from source
git clone https://github.com/yourusername/api-starter.git
cd api-starter
pip install -e .
```

### Requirements

- Python 3.11 or higher
- pip 23.0 or higher

## Your First API

### 1. Create a Simple Application

Create a file named `main.py`:

```python
"""
Simple API example.

Run with: python main.py
"""

from api_starter import APIStarter, RequestContext
from pydantic import BaseModel

# Create application
app = APIStarter(
    title="My First API",
    version="1.0.0"
)

# Define a model
class Message(BaseModel):
    text: str

# Create endpoints
@app.endpoint("/", methods=["GET"])
async def root(ctx: RequestContext):
    """Root endpoint."""
    return {
        "message": "Welcome to API Starter!",
        "version": "1.0.0"
    }

@app.endpoint("/hello/{name}", methods=["GET"])
async def hello(ctx: RequestContext, name: str):
    """Greet someone by name."""
    return {"message": f"Hello, {name}!"}

@app.endpoint("/echo", methods=["POST"])
async def echo(ctx: RequestContext, message: Message):
    """Echo back a message."""
    return {
        "echo": message.text,
        "length": len(message.text)
    }

# Run the application
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

### 2. Run Your API

```bash
# Run directly
python main.py

# Or use Uvicorn
uvicorn main:app --reload
```

### 3. Test Your API

Open your browser or use curl:

```bash
# Test root endpoint
curl http://localhost:8000/

# Test hello endpoint
curl http://localhost:8000/hello/World

# Test echo endpoint
curl -X POST http://localhost:8000/echo \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello, API!"}'
```

## Adding Features

### Database Integration

```python
from api_starter.plugins import DatabasePlugin

# Add database plugin
app.add_plugin(DatabasePlugin(
    database_url="postgresql://localhost/mydb"
))

@app.endpoint("/users", methods=["GET"])
async def list_users(ctx: RequestContext):
    """List users from database."""
    async with ctx.get_db_session() as session:
        users = await session.execute("SELECT * FROM users")
        return {"users": users.all()}
```

### Authentication

```python
from api_starter.middleware import AuthenticationMiddleware
from api_starter.security import require_auth

# Add auth middleware
app.add_middleware(AuthenticationMiddleware())

@app.endpoint("/protected", methods=["GET"])
@require_auth
async def protected_endpoint(ctx: RequestContext):
    """Protected endpoint requiring authentication."""
    user_id = ctx.get("user_id")
    return {"message": f"Hello, user {user_id}!"}
```

### Caching

```python
from api_starter.plugins import CachePlugin

# Add cache plugin
app.add_plugin(CachePlugin(
    redis_url="redis://localhost:6379/0"
))

@app.endpoint("/data", methods=["GET"])
async def get_data(ctx: RequestContext):
    """Get data with caching."""
    cache = ctx.get("cache")
    
    # Try cache first
    cached = await cache.get("data_key")
    if cached:
        return {"data": cached, "source": "cache"}
    
    # Fetch from database
    data = await fetch_from_db()
    
    # Cache for 5 minutes
    await cache.set("data_key", data, ttl=300)
    
    return {"data": data, "source": "database"}
```

## Project Structure

Organize your project like this:

```
my-api/
├── app/
│   ├── __init__.py
│   ├── main.py              # Application entry
│   ├── models.py            # Pydantic models
│   ├── endpoints/           # API endpoints
│   │   ├── users.py
│   │   └── tasks.py
│   ├── services/            # Business logic
│   │   └── user_service.py
│   └── database/            # Database setup
│       └── models.py
├── tests/                   # Test suite
│   └── test_api.py
├── requirements.txt
└── README.md
```

## Next Steps

- Read the [User Guide](index.md) for detailed documentation
- Check out [Examples](../examples/basic-crud.md) for common patterns
- Learn about [Advanced Features](../advanced/index.md)
- Explore [API Reference](../api-reference/index.md)

## Getting Help

- 📚 [Documentation](https://api-starter.readthedocs.io)
- 💬 [Discord Community](https://discord.gg/api-starter)
- 🐛 [Issue Tracker](https://github.com/yourusername/api-starter/issues)
- 📧 [Email Support](mailto:support@api-starter.dev)
```

### `docs/tutorials/todo-api/01-setup.md`

```markdown
# Tutorial: Building a Todo API - Part 1: Setup

In this tutorial series, you'll build a complete Todo API with authentication, database integration, and testing.

## What You'll Build

By the end of this tutorial, you'll have:

- ✅ RESTful API with CRUD operations
- ✅ PostgreSQL database integration
- ✅ JWT authentication
- ✅ Role-based authorization
- ✅ Comprehensive test suite
- ✅ Docker deployment

## Prerequisites

- Python 3.11+
- PostgreSQL installed
- Basic understanding of async Python
- Familiarity with REST APIs

## Part 1: Project Setup

### Step 1: Create Project Structure

```bash
# Create project directory
mkdir todo-api
cd todo-api

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install API Starter
pip install api-starter[all]
```

### Step 2: Initialize Project

Create the following structure:

```
todo-api/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── config/
│   │   ├── __init__.py
│   │   └── settings.py
│   ├── models/
│   │   ├── __init__.py
│   │   └── task.py
│   ├── endpoints/
│   │   ├── __init__.py
│   │   └── tasks.py
│   ├── services/
│   │   ├── __init__.py
│   │   └── task_service.py
│   └── database/
│       ├── __init__.py
│       └── models.py
├── tests/
│   └── __init__.py
├── requirements.txt
├── .env
└── README.md
```

### Step 3: Create Configuration

#### `app/config/settings.py`

```python
"""
Application configuration.

Loads settings from environment variables.
"""

from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    """Application settings."""
    
    # Application
    app_name: str = "Todo API"
    app_version: str = "1.0.0"
    debug: bool = False
    
    # Database
    database_url: str = "postgresql://localhost/tododb"
    
    # Redis
    redis_url: str = "redis://localhost:6379/0"
    
    # Security
    secret_key: str = "your-secret-key-change-in-production"
    jwt_algorithm: str = "HS256"
    access_token_expire_minutes: int = 30
    
    # CORS
    cors_origins: list[str] = ["http://localhost:3000"]
    
    class Config:
        env_file = ".env"


# Global settings instance
settings = Settings()
```

#### `.env`

```bash
# Application
APP_NAME="Todo API"
DEBUG=true

# Database
DATABASE_URL=postgresql://user:password@localhost/tododb

# Redis
REDIS_URL=redis://localhost:6379/0

# Security (CHANGE IN PRODUCTION!)
SECRET_KEY=your-super-secret-key-change-this
```

### Step 4: Create Main Application

#### `app/main.py`

```python
"""
Todo API main application.

Run with: uvicorn app.main:app --reload
"""

from api_starter import APIStarter
from api_starter.middleware import CORSMiddleware, TimingMiddleware
from api_starter.plugins import DatabasePlugin, CachePlugin

from app.config.settings import settings

# Create application
app = APIStarter(
    title=settings.app_name,
    version=settings.app_version,
    description="A simple but complete Todo API",
    debug=settings.debug
)

# Add middleware
app.add_middleware(CORSMiddleware(
    allowed_origins=settings.cors_origins
))
app.add_middleware(TimingMiddleware())

# Add plugins
app.add_plugin(DatabasePlugin(
    database_url=settings.database_url
))
app.add_plugin(CachePlugin(
    redis_url=settings.redis_url
))

# Startup handler
@app.on_startup
async def startup():
    """Initialize application."""
    print(f"🚀 Starting {settings.app_name} v{settings.app_version}")

# Shutdown handler
@app.on_shutdown
async def shutdown():
    """Cleanup on shutdown."""
    print("👋 Shutting down gracefully")

# Health check
@app.endpoint("/health", methods=["GET"])
async def health_check(ctx):
    """Health check endpoint."""
    return {
        "status": "healthy",
        "version": settings.app_version
    }
```

### Step 5: Install Dependencies

#### `requirements.txt`

```txt
# Core framework
api-starter[all]>=1.0.0

# Database
sqlalchemy>=2.0.0
asyncpg>=0.29.0
alembic>=1.12.0

# Redis
redis>=5.0.0
hiredis>=2.2.0

# Security
python-jose[cryptography]>=3.3.0
passlib[bcrypt]>=1.7.4
python-multipart>=0.0.6

# Configuration
pydantic-settings>=2.0.0
python-dotenv>=1.0.0

# Testing
pytest>=7.4.0
pytest-asyncio>=0.21.0
httpx>=0.25.0

# Development
black>=23.0.0
ruff>=0.1.0
mypy>=1.7.0
```

Install dependencies:

```bash
pip install -r requirements.txt
```

### Step 6: Run the Application

```bash
# Run with auto-reload
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Visit `http://localhost:8000/health` to verify it's running.

### Step 7: Test the Setup

Create `tests/test_setup.py`:

```python
"""Test application setup."""

import pytest
from httpx import AsyncClient

from app.main import app


@pytest.mark.asyncio
async def test_health_check():
    """Test health check endpoint."""
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.get("/health")
        
        assert response.status_code == 200
        data = response.json()
        assert data["status"] == "healthy"
        assert "version" in data
```

Run tests:

```bash
pytest tests/ -v
```

## What's Next?

In the next part, we'll:

- Define data models
- Set up the database
- Create CRUD endpoints

[Continue to Part 2: Models and Database →](02-models.md)

## Summary

You've successfully:

- ✅ Created project structure
- ✅ Configured the application
- ✅ Set up middleware and plugins
- ✅ Created health check endpoint
- ✅ Written first test

## Resources

- [Configuration Guide](../../user-guide/configuration.md)
- [Middleware Documentation](../../user-guide/middleware.md)
- [Plugin System](../../user-guide/plugins.md)
```

---

## 📄 README Templates

### Root `README.md`

```markdown
# API Starter Framework

<p align="center">
  <img src="docs/_static/logo.png" alt="API Starter Logo" width="200">
</p>

<p align="center">
  <strong>A production-ready, FastAPI-inspired web framework built on ASGI</strong>
</p>

<p align="center">
  <a href="https://github.com/yourusername/api-starter/actions">
    <img src="https://github.com/yourusername/api-starter/workflows/CI/badge.svg" alt="CI Status">
  </a>
  <a href="https://codecov.io/gh/yourusername/api-starter">
    <img src="https://codecov.io/gh/yourusername/api-starter/branch/main/graph/badge.svg" alt="Coverage">
  </a>
  <a href="https://pypi.org/project/api-starter/">
    <img src="https://img.shields.io/pypi/v/api-starter.svg" alt="PyPI Version">
  </a>
  <a href="https://pypi.org/project/api-starter/">
    <img src="https://img.shields.io/pypi/pyversions/api-starter.svg" alt="Python Versions">
  </a>
  <a href="https://github.com/yourusername/api-starter/blob/main/LICENSE">
    <img src="https://img.shields.io/github/license/yourusername/api-starter.svg" alt="License">
  </a>
</p>

---

## ✨ Features

- 🚀 **Fast**: Built on ASGI with full async/await support
- 🔒 **Type Safe**: Comprehensive type hints and Pydantic validation
- 🧩 **Extensible**: Plugin architecture for easy customization
- 📊 **Observable**: Built-in metrics, tracing, and logging
- 🛡️ **Secure**: Authentication, authorization, and rate limiting
- 📚 **Well Documented**: Comprehensive guides and API reference
- ✅ **Production Ready**: Battle-tested patterns and best practices

## 🚀 Quick Start

### Installation

```bash
pip install api-starter
```

### Hello World

```python
from api_starter import APIStarter, RequestContext

app = APIStarter(title="My API", version="1.0.0")

@app.endpoint("/hello/{name}", methods=["GET"])
async def hello(ctx: RequestContext, name: str):
    return {"message": f"Hello, {name}!"}

if __name__ == "__main__":
    app.run()
```

Run the application:

```bash
python main.py
```

Visit `http://localhost:8000/hello/World`

## 📚 Documentation

- **[User Guide](https://api-starter.readthedocs.io/user-guide/)** - Learn how to use the framework
- **[API Reference](https://api-starter.readthedocs.io/api-reference/)** - Detailed API documentation
- **[Tutorials](https://api-starter.readthedocs.io/tutorials/)** - Step-by-step tutorials
- **[Examples](https://github.com/yourusername/api-starter/tree/main/examples)** - Code examples

## 🎯 Use Cases

API Starter is perfect for:

- RESTful APIs
- GraphQL APIs
- WebSocket servers
- Microservices
- Backend for mobile/web apps
- Internal tools and services

## 💡 Examples

### CRUD API

```python
from pydantic import BaseModel

class Task(BaseModel):
    title: str
    completed: bool = False

@app.endpoint("/tasks", methods=["GET"])
async def list_tasks(ctx: RequestContext):
    return {"tasks": await task_service.list()}

@app.endpoint("/tasks", methods=["POST"])
async def create_task(ctx: RequestContext, task: Task):
    created = await task_service.create(task)
    return {"task": created.model_dump()}, 201
```

### Authentication

```python
from api_starter.security import require_auth

@app.endpoint("/profile", methods=["GET"])
@require_auth
async def get_profile(ctx: RequestContext):
    user_id = ctx.get("user_id")
    profile = await user_service.get_profile(user_id)
    return {"profile": profile}
```

### Database Integration

```python
from api_starter.plugins import DatabasePlugin

app.add_plugin(DatabasePlugin(
    database_url="postgresql://localhost/mydb"
))

@app.endpoint("/users", methods=["GET"])
async def list_users(ctx: RequestContext):
    async with ctx.get_db_session() as session:
        users = await user_repository.list(session)
        return {"users": [u.model_dump() for u in users]}
```

## 🏗️ Architecture

```
┌─────────────┐
│   Client    │
└─────┬───────┘
      │
┌─────▼───────────────────────┐
│   API Starter Framework     │
│  ┌──────────────────────┐   │
│  │    Application       │   │
│  ├──────────────────────┤   │
│  │    Middleware        │   │
│  ├──────────────────────┤   │
│  │    Plugins           │   │
│  ├──────────────────────┤   │
│  │    Router            │   │
│  └──────────────────────┘   │
└─────┬───────────────────────┘
      │
┌─────▼───────────────────────┐
│    Your Application         │
│  ┌──────────────────────┐   │
│  │    Endpoints         │   │
│  ├──────────────────────┤   │
│  │    Services          │   │
│  ├──────────────────────┤   │
│  │    Repositories      │   │
│  └──────────────────────┘   │
└─────────────────────────────┘
```

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Development Setup

```bash
# Clone repository
git clone https://github.com/yourusername/api-starter.git
cd api-starter

# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install dev dependencies
pip install -e ".[dev]"

# Run tests
pytest

# Run linters
black .
ruff check .
mypy api_starter
```

## 📝 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by [FastAPI](https://fastapi.tiangolo.com/)
- Built on [Starlette](https://www.starlette.io/)
- Validation by [Pydantic](https://docs.pydantic.dev/)

## 📧 Contact

- **Documentation**: https://api-starter.readthedocs.io
- **Issues**: https://github.com/yourusername/api-starter/issues
- **Discussions**: https://github.com/yourusername/api-starter/discussions
- **Email**: support@api-starter.dev

---

<p align="center">
  Made with ❤️ by the API Starter team
</p>
```

---

## 🤝 Contributing Guidelines

### `CONTRIBUTING.md`

```markdown
# Contributing to API Starter

Thank you for your interest in contributing to API Starter! This document provides guidelines and instructions for contributing.

## Code of Conduct

This project adheres to a [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## How to Contribute

### Reporting Bugs

Before creating a bug report:

1. Check the [issue tracker](https://github.com/yourusername/api-starter/issues) for existing issues
2. Update to the latest version to see if the issue persists

When creating a bug report, include:

- Python version
- API Starter version
- Minimal code to reproduce the issue
- Expected vs actual behavior
- Error messages and stack traces

### Suggesting Features

Feature requests are welcome! Please:

1. Check existing feature requests first
2. Clearly describe the feature and its use case
3. Explain why it would be useful to most users
4. Consider if it could be implemented as a plugin

### Pull Requests

1. **Fork the repository**

```bash
git clone https://github.com/yourusername/api-starter.git
cd api-starter
git remote add upstream https://github.com/yourusername/api-starter.git
```

2. **Create a branch**

```bash
git checkout -b feature/your-feature-name
```

3. **Make your changes**

- Write clear, commented code
- Follow the code style guide
- Add tests for new features
- Update documentation

4. **Run tests**

```bash
# Run test suite
pytest

# Run with coverage
pytest --cov=api_starter --cov-report=html

# Run linters
black .
ruff check .
mypy api_starter
```

5. **Commit your changes**

```bash
git add .
git commit -m "feat: add amazing feature"
```

Follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `test:` Test changes
- `refactor:` Code refactoring
- `perf:` Performance improvements
- `chore:` Maintenance tasks

6. **Push and create PR**

```bash
git push origin feature/your-feature-name
```

Then create a Pull Request on GitHub.

## Development Setup

### Prerequisites

- Python 3.11+
- PostgreSQL (for database tests)
- Redis (for cache tests)

### Setup

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install dependencies
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install
```

### Project Structure

```
api-starter/
├── api_starter/          # Main package
│   ├── core/            # Core framework
│   ├── models/          # Base models
│   ├── utils/           # Utilities
│   ├── plugins/         # Built-in plugins
│   └── middleware/      # Middleware
├── tests/               # Test suite
├── docs/                # Documentation
└── examples/            # Example applications
```

## Code Style

### Python Style

We use:

- **Black** for code formatting
- **Ruff** for linting
- **MyPy** for type checking

Configuration in `pyproject.toml`.

### Docstrings

Use Google-style docstrings:

```python
def function(arg1: str, arg2: int) -> bool:
    """
    Short description.
    
    Longer description with more details about what the function does.
    
    Args:
        arg1: Description of arg1
        arg2: Description of arg2
        
    Returns:
        Description of return value
        
    Raises:
        ValueError: When something goes wrong
        
    Example:
        >>> result = function("test", 42)
        >>> print(result)
        True
    """
    return True
```

### Type Hints

Always use type hints:

```python
from typing import List, Optional, Dict, Any

def process_data(
    items: List[Dict[str, Any]],
    limit: Optional[int] = None
) -> List[str]:
    """Process data with type hints."""
    return [item["name"] for item in items[:limit]]
```

## Testing

### Writing Tests

```python
import pytest
from api_starter import APIStarter, RequestContext


@pytest.mark.asyncio
async def test_endpoint():
    """Test endpoint functionality."""
    app = APIStarter()
    
    @app.endpoint("/test", methods=["GET"])
    async def test_handler(ctx: RequestContext):
        return {"status": "ok"}
    
    # Test logic here
    assert True
```

### Running Tests

```bash
# All tests
pytest

# Specific test file
pytest tests/test_application.py

# Specific test
pytest tests/test_application.py::test_endpoint_registration

# With coverage
pytest --cov=api_starter --cov-report=html

# Parallel execution
pytest -n auto
```

## Documentation

### Building Docs

```bash
cd docs
pip install -r requirements.txt
sphinx-build -b html . _build
```

### Documentation Style

- Use clear, concise language
- Include code examples
- Add diagrams where helpful
- Keep it up to date with code changes

## Review Process

Pull requests require:

1. ✅ All tests passing
2. ✅ Code coverage maintained (>90%)
3. ✅ Linters passing
4. ✅ Documentation updated
5. ✅ Review approval from maintainer

## Questions?

- 💬 [GitHub Discussions](https://github.com/yourusername/api-starter/discussions)
- 📧 [Email](mailto:dev@api-starter.dev)
- 💬 [Discord](https://discord.gg/api-starter)

Thank you for contributing! 🎉
```


[↑ Back to TOC](#-table-of-contents)
