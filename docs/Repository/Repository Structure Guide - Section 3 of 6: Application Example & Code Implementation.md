# 📁 Python API Starter - Repository Structure Guide

**Section 3 of 6: Application Example & Code Implementation**

---

## 📑 Table of Contents

- [Application Example Structure](#-application-example-structure)
- [Core Implementation with Docstrings](#-core-implementation-with-docstrings)
- [Application Class](#-application-class)
- [Context Class](#-context-class)
- [Router Implementation](#-router-implementation)

---

## 🚀 Application Example Structure

### Example Application Tree

```
examples/
├── __init__.py
├── simple/                                    # Simple example
│   ├── __init__.py
│   ├── main.py                               # Basic app
│   └── README.md
│
├── todo_api/                                  # Full-featured example
│   ├── __init__.py
│   ├── main.py                               # Application entry
│   ├── config/
│   │   ├── __init__.py
│   │   ├── settings.py                       # Configuration
│   │   └── logging.py                        # Logging setup
│   ├── models/
│   │   ├── __init__.py
│   │   ├── task.py                           # Task models
│   │   └── user.py                           # User models
│   ├── endpoints/
│   │   ├── __init__.py
│   │   ├── tasks.py                          # Task endpoints
│   │   ├── users.py                          # User endpoints
│   │   └── auth.py                           # Auth endpoints
│   ├── services/
│   │   ├── __init__.py
│   │   ├── task_service.py                   # Business logic
│   │   └── auth_service.py                   # Auth logic
│   ├── repositories/
│   │   ├── __init__.py
│   │   ├── base.py                           # Base repository
│   │   ├── task_repository.py                # Task data access
│   │   └── user_repository.py                # User data access
│   ├── database/
│   │   ├── __init__.py
│   │   ├── config.py                         # DB configuration
│   │   └── models.py                         # SQLAlchemy models
│   ├── security/
│   │   ├── __init__.py
│   │   ├── jwt.py                            # JWT utilities
│   │   ├── password.py                       # Password hashing
│   │   └── permissions.py                    # RBAC
│   ├── plugins/
│   │   ├── __init__.py
│   │   ├── database.py                       # Database plugin
│   │   ├── cache.py                          # Cache plugin
│   │   └── metrics.py                        # Metrics plugin
│   └── tests/
│       ├── __init__.py
│       ├── conftest.py                       # Pytest fixtures
│       ├── test_services.py
│       ├── test_endpoints.py
│       └── test_integration.py
```

---

## 📝 Core Implementation with Docstrings

### `api_starter/__init__.py`

```python
"""
API Starter Framework
=====================

A production-ready, FastAPI-inspired web framework built on ASGI.

Features:
    - Full async/await support
    - Type-safe with Pydantic validation
    - Plugin architecture for extensibility
    - Middleware system for request/response processing
    - Built-in observability and monitoring
    - Production-ready patterns

Basic Usage:
    >>> from api_starter import APIStarter, RequestContext
    >>> 
    >>> app = APIStarter(title="My API", version="1.0.0")
    >>> 
    >>> @app.endpoint("/hello", methods=["GET"])
    >>> async def hello(ctx: RequestContext):
    ...     return {"message": "Hello, World!"}
    >>> 
    >>> if __name__ == "__main__":
    ...     app.run(host="0.0.0.0", port=8000)

Author: Your Name
License: MIT
Version: 1.0.0
"""

from api_starter.__version__ import (
    __version__,
    __author__,
    __license__,
    __description__,
)
from api_starter.core.application import APIStarter
from api_starter.core.context import RequestContext
from api_starter.core.middleware import Middleware
from api_starter.core.plugin import Plugin
from api_starter.core.response import (
    JSONResponse,
    HTMLResponse,
    StreamingResponse,
    FileResponse,
)
from api_starter.core.exceptions import (
    APIException,
    ValidationError,
    NotFoundError,
    UnauthorizedError,
    ForbiddenError,
)
from api_starter.models.base import BaseModel
from api_starter.models.entity import EntityModel

# Package exports
__all__ = [
    # Version info
    "__version__",
    "__author__",
    "__license__",
    "__description__",
    # Core classes
    "APIStarter",
    "RequestContext",
    "Middleware",
    "Plugin",
    # Responses
    "JSONResponse",
    "HTMLResponse",
    "StreamingResponse",
    "FileResponse",
    # Exceptions
    "APIException",
    "ValidationError",
    "NotFoundError",
    "UnauthorizedError",
    "ForbiddenError",
    # Models
    "BaseModel",
    "EntityModel",
]
```

### `api_starter/__version__.py`

```python
"""
Version information for API Starter framework.

This module contains version and package metadata.
"""

__version__ = "1.0.0"
__author__ = "Your Name"
__author_email__ = "your.email@example.com"
__license__ = "MIT"
__description__ = "A production-ready, FastAPI-inspired web framework"
__url__ = "https://github.com/yourusername/api-starter"

# Version tuple for programmatic access
VERSION = tuple(map(int, __version__.split(".")))
```

---

## 🎯 Application Class

### `api_starter/core/application.py`

```python
"""
Core Application Class
======================

Main application class providing the ASGI interface and framework functionality.

This module contains the central APIStarter class that handles:
    - Request routing
    - Middleware processing
    - Plugin management
    - Error handling
    - ASGI lifecycle

Example:
    >>> from api_starter import APIStarter
    >>> 
    >>> app = APIStarter(
    ...     title="Task API",
    ...     version="1.0.0",
    ...     description="A task management API"
    ... )
    >>> 
    >>> @app.endpoint("/health", methods=["GET"])
    >>> async def health_check(ctx):
    ...     return {"status": "healthy"}
"""

from __future__ import annotations

import asyncio
import logging
from typing import (
    Any,
    Awaitable,
    Callable,
    Dict,
    List,
    Optional,
    Set,
    Union,
)

from starlette.applications import Starlette
from starlette.middleware import Middleware as StarletteMiddleware
from starlette.responses import Response
from starlette.routing import Route, Mount

from api_starter.core.context import RequestContext
from api_starter.core.middleware import Middleware
from api_starter.core.plugin import Plugin
from api_starter.core.router import Router
from api_starter.core.response import JSONResponse
from api_starter.core.exceptions import APIException

logger = logging.getLogger(__name__)


class APIStarter:
    """
    Main application class for the API Starter framework.
    
    This class provides a FastAPI-inspired interface built on ASGI/Starlette,
    offering a clean, type-safe way to build production APIs.
    
    Attributes:
        title (str): API title for documentation
        version (str): API version
        description (str): API description
        debug (bool): Debug mode flag
        
    Example:
        >>> app = APIStarter(
        ...     title="My API",
        ...     version="1.0.0",
        ...     description="Production API"
        ... )
        >>> 
        >>> @app.endpoint("/items", methods=["POST"])
        >>> async def create_item(ctx: RequestContext, item: Item):
        ...     return {"item": item.model_dump()}, 201
    
    Note:
        The application is fully async and requires an ASGI server
        like Uvicorn or Hypercorn to run.
    """
    
    def __init__(
        self,
        title: str = "API Starter",
        version: str = "1.0.0",
        description: str = "API built with API Starter framework",
        debug: bool = False,
        docs_url: Optional[str] = "/docs",
        redoc_url: Optional[str] = "/redoc",
        openapi_url: Optional[str] = "/openapi.json",
    ) -> None:
        """
        Initialize the API Starter application.
        
        Args:
            title: The title of the API (used in documentation)
            version: The version of the API (semantic versioning)
            description: A description of the API functionality
            debug: Enable debug mode with detailed error messages
            docs_url: URL path for Swagger UI documentation (None to disable)
            redoc_url: URL path for ReDoc documentation (None to disable)
            openapi_url: URL path for OpenAPI schema (None to disable)
            
        Example:
            >>> app = APIStarter(
            ...     title="Todo API",
            ...     version="2.0.0",
            ...     description="Task management system",
            ...     debug=True
            ... )
        """
        self.title = title
        self.version = version
        self.description = description
        self.debug = debug
        self.docs_url = docs_url
        self.redoc_url = redoc_url
        self.openapi_url = openapi_url
        
        # Internal state
        self._router = Router()
        self._middleware: List[Middleware] = []
        self._plugins: Dict[str, Plugin] = {}
        self._routes: List[Route] = []
        self._startup_handlers: List[Callable[[], Awaitable[None]]] = []
        self._shutdown_handlers: List[Callable[[], Awaitable[None]]] = []
        
        # Create underlying Starlette app
        self._app: Optional[Starlette] = None
        
        logger.info(
            f"Initialized {self.title} v{self.version}",
            extra={"debug": self.debug}
        )
    
    def endpoint(
        self,
        path: str,
        methods: List[str] = ["GET"],
        name: Optional[str] = None,
        tags: Optional[List[str]] = None,
        summary: Optional[str] = None,
        description: Optional[str] = None,
    ) -> Callable[[Callable], Callable]:
        """
        Decorator to register an endpoint handler.
        
        This decorator registers a function as an HTTP endpoint handler
        with automatic validation, serialization, and error handling.
        
        Args:
            path: URL path pattern (e.g., "/users/{user_id}")
            methods: List of HTTP methods (GET, POST, PUT, DELETE, etc.)
            name: Optional endpoint name for URL generation
            tags: Optional list of tags for documentation grouping
            summary: Short summary for documentation
            description: Detailed description for documentation
            
        Returns:
            Decorator function that registers the endpoint
            
        Example:
            >>> @app.endpoint("/users/{user_id}", methods=["GET"])
            >>> async def get_user(ctx: RequestContext, user_id: str):
            ...     '''Get user by ID.'''
            ...     user = await user_service.get(user_id)
            ...     return {"user": user.model_dump()}
            
            >>> @app.endpoint("/users", methods=["POST"])
            >>> async def create_user(ctx: RequestContext, user: UserCreate):
            ...     '''Create a new user with validation.'''
            ...     created = await user_service.create(user)
            ...     return {"user": created.model_dump()}, 201
        
        Note:
            - Path parameters are automatically extracted and passed
            - Request body is validated against type hints
            - Response is automatically serialized to JSON
            - Errors are caught and formatted appropriately
        """
        def decorator(func: Callable) -> Callable:
            # Extract function metadata
            endpoint_name = name or func.__name__
            endpoint_summary = summary or func.__doc__ or endpoint_name
            endpoint_description = description or func.__doc__
            
            # Register with router
            self._router.add_route(
                path=path,
                handler=func,
                methods=methods,
                name=endpoint_name,
                tags=tags or [],
                summary=endpoint_summary,
                description=endpoint_description,
            )
            
            logger.debug(
                f"Registered endpoint: {methods} {path} -> {endpoint_name}"
            )
            
            return func
        
        return decorator
    
    def add_middleware(self, middleware: Middleware) -> None:
        """
        Add middleware to the application.
        
        Middleware is executed in the order added for requests,
        and in reverse order for responses.
        
        Args:
            middleware: Middleware instance to add
            
        Example:
            >>> from api_starter.middleware import TimingMiddleware
            >>> 
            >>> app.add_middleware(TimingMiddleware())
            >>> app.add_middleware(AuthenticationMiddleware())
            
        Note:
            Middleware must be added before calling app.run()
        """
        self._middleware.append(middleware)
        logger.info(f"Added middleware: {middleware.__class__.__name__}")
    
    def add_plugin(self, plugin: Plugin) -> None:
        """
        Add a plugin to the application.
        
        Plugins extend framework functionality and can hook into
        the application lifecycle, requests, and responses.
        
        Args:
            plugin: Plugin instance to add
            
        Raises:
            ValueError: If plugin with same name already exists
            
        Example:
            >>> from api_starter.plugins import DatabasePlugin, CachePlugin
            >>> 
            >>> app.add_plugin(DatabasePlugin(db_url="postgresql://..."))
            >>> app.add_plugin(CachePlugin(redis_url="redis://..."))
            
        Note:
            Plugins are initialized when the application starts
        """
        if plugin.name in self._plugins:
            raise ValueError(f"Plugin '{plugin.name}' already registered")
        
        self._plugins[plugin.name] = plugin
        logger.info(f"Added plugin: {plugin.name}")
    
    def on_startup(self, func: Callable[[], Awaitable[None]]) -> Callable:
        """
        Register a startup handler.
        
        Startup handlers are called when the application starts,
        before accepting any requests.
        
        Args:
            func: Async function to call on startup
            
        Returns:
            The same function (for use as decorator)
            
        Example:
            >>> @app.on_startup
            >>> async def startup():
            ...     print("Application starting...")
            ...     await database.connect()
        """
        self._startup_handlers.append(func)
        return func
    
    def on_shutdown(self, func: Callable[[], Awaitable[None]]) -> Callable:
        """
        Register a shutdown handler.
        
        Shutdown handlers are called when the application stops,
        after processing all requests.
        
        Args:
            func: Async function to call on shutdown
            
        Returns:
            The same function (for use as decorator)
            
        Example:
            >>> @app.on_shutdown
            >>> async def shutdown():
            ...     print("Application shutting down...")
            ...     await database.disconnect()
        """
        self._shutdown_handlers.append(func)
        return func
    
    async def _initialize_plugins(self) -> None:
        """
        Initialize all registered plugins.
        
        Called during application startup. Plugins are initialized
        in the order they were added.
        """
        for name, plugin in self._plugins.items():
            try:
                await plugin.initialize(self)
                logger.info(f"Initialized plugin: {name}")
            except Exception as e:
                logger.error(
                    f"Failed to initialize plugin '{name}': {e}",
                    exc_info=True
                )
                raise
    
    async def _shutdown_plugins(self) -> None:
        """
        Shutdown all registered plugins.
        
        Called during application shutdown. Plugins are shut down
        in reverse order of initialization.
        """
        for name, plugin in reversed(list(self._plugins.items())):
            try:
                await plugin.shutdown()
                logger.info(f"Shut down plugin: {name}")
            except Exception as e:
                logger.error(
                    f"Error shutting down plugin '{name}': {e}",
                    exc_info=True
                )
    
    def run(
        self,
        host: str = "0.0.0.0",
        port: int = 8000,
        log_level: str = "info",
        reload: bool = False,
        workers: int = 1,
    ) -> None:
        """
        Run the application using Uvicorn.
        
        This is a convenience method for development. For production,
        run the ASGI app directly with an ASGI server.
        
        Args:
            host: Host to bind to
            port: Port to bind to
            log_level: Logging level (debug, info, warning, error)
            reload: Enable auto-reload on code changes
            workers: Number of worker processes
            
        Example:
            >>> if __name__ == "__main__":
            ...     app.run(host="0.0.0.0", port=8000)
            
        Note:
            For production, use:
            >>> uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4
        """
        import uvicorn
        
        uvicorn.run(
            self,
            host=host,
            port=port,
            log_level=log_level,
            reload=reload,
            workers=workers,
        )
    
    async def __call__(
        self,
        scope: Dict[str, Any],
        receive: Callable,
        send: Callable,
    ) -> None:
        """
        ASGI application interface.
        
        This method makes the class a valid ASGI application that can
        be served by ASGI servers like Uvicorn.
        
        Args:
            scope: ASGI connection scope
            receive: ASGI receive callable
            send: ASGI send callable
        """
        if self._app is None:
            # Build Starlette app on first call
            await self._build_app()
        
        await self._app(scope, receive, send)
    
    async def _build_app(self) -> None:
        """
        Build the underlying Starlette application.
        
        This is called lazily on the first request to allow
        all routes and middleware to be registered first.
        """
        # Convert routes
        routes = self._router.get_routes()
        
        # Build Starlette app
        self._app = Starlette(
            debug=self.debug,
            routes=routes,
            on_startup=[self._on_startup],
            on_shutdown=[self._on_shutdown],
        )
        
        logger.info("Built ASGI application")
    
    async def _on_startup(self) -> None:
        """Handle application startup."""
        logger.info(f"Starting {self.title} v{self.version}")
        
        # Initialize plugins
        await self._initialize_plugins()
        
        # Run user startup handlers
        for handler in self._startup_handlers:
            await handler()
        
        logger.info("Application started successfully")
    
    async def _on_shutdown(self) -> None:
        """Handle application shutdown."""
        logger.info("Shutting down application")
        
        # Run user shutdown handlers
        for handler in self._shutdown_handlers:
            await handler()
        
        # Shutdown plugins
        await self._shutdown_plugins()
        
        logger.info("Application shut down successfully")
```

---

## 📋 Context Class

### `api_starter/core/context.py`

```python
"""
Request Context
===============

Provides context for request processing with state management.

The RequestContext carries information about the current request through
middleware, plugins, and endpoint handlers.

Example:
    >>> @app.endpoint("/users/{user_id}", methods=["GET"])
    >>> async def get_user(ctx: RequestContext, user_id: str):
    ...     # Access request data
    ...     auth_token = ctx.headers.get("authorization")
    ...     
    ...     # Get user from context (added by auth middleware)
    ...     current_user_id = ctx.user_id
    ...     
    ...     # Store data for other middleware
    ...     ctx.set("request_start_time", time.time())
    ...     
    ...     return {"user_id": user_id}
"""

from __future__ import annotations

import time
from typing import Any, Dict, Optional

from starlette.requests import Request
from starlette.datastructures import Headers, QueryParams


class RequestContext:
    """
    Request context carrying state through the request lifecycle.
    
    This class wraps the ASGI request and provides a convenient interface
    for accessing request data and storing state that needs to be shared
    between middleware, plugins, and handlers.
    
    Attributes:
        method (str): HTTP method (GET, POST, etc.)
        path (str): Request path
        headers (Headers): Request headers
        query (QueryParams): Query parameters
        
    Example:
        >>> async def my_handler(ctx: RequestContext):
        ...     # Access request info
        ...     if ctx.method == "POST":
        ...         body = await ctx.json()
        ...     
        ...     # Get query parameters
        ...     page = ctx.query.get("page", "1")
        ...     
        ...     # Store custom data
        ...     ctx.set("user_id", "123")
        ...     user_id = ctx.get("user_id")
        ...     
        ...     return {"status": "ok"}
    
    Note:
        Context is created automatically by the framework for each request.
        Don't create instances manually.
    """
    
    def __init__(self, request: Request) -> None:
        """
        Initialize request context.
        
        Args:
            request: Starlette Request object
            
        Note:
            This is called automatically by the framework.
            Users should not create RequestContext instances directly.
        """
        self._request = request
        self._state: Dict[str, Any] = {}
        self._start_time = time.time()
    
    @property
    def method(self) -> str:
        """
        Get HTTP method.
        
        Returns:
            HTTP method (GET, POST, PUT, DELETE, etc.)
            
        Example:
            >>> if ctx.method == "POST":
            ...     data = await ctx.json()
        """
        return self._request.method
    
    @property
    def path(self) -> str:
        """
        Get request path.
        
        Returns:
            URL path without query string
            
        Example:
            >>> # For request to /users/123?page=1
            >>> print(ctx.path)  # /users/123
        """
        return self._request.url.path
    
    @property
    def headers(self) -> Headers:
        """
        Get request headers.
        
        Returns:
            Headers object (case-insensitive dict-like)
            
        Example:
            >>> auth = ctx.headers.get("authorization")
            >>> content_type = ctx.headers.get("content-type")
        """
        return self._request.headers
    
    @property
    def query(self) -> QueryParams:
        """
        Get query parameters.
        
        Returns:
            QueryParams object (multi-dict)
            
        Example:
            >>> page = ctx.query.get("page", "1")
            >>> filters = ctx.query.getlist("filter")
        """
        return self._request.query_params
    
    @property
    def path_params(self) -> Dict[str, str]:
        """
        Get path parameters.
        
        Returns:
            Dictionary of path parameters
            
        Example:
            >>> # For route /users/{user_id}
            >>> # Request to /users/123
            >>> print(ctx.path_params["user_id"])  # "123"
        """
        return self._request.path_params
    
    @property
    def client_ip(self) -> Optional[str]:
        """
        Get client IP address.
        
        Returns:
            Client IP address or None
            
        Example:
            >>> ip = ctx.client_ip
            >>> print(f"Request from {ip}")
        """
        if self._request.client:
            return self._request.client.host
        return None
    
    @property
    def url(self) -> str:
        """
        Get full request URL.
        
        Returns:
            Complete URL including scheme, host, and query
            
        Example:
            >>> print(ctx.url)
            # https://api.example.com/users/123?page=1
        """
        return str(self._request.url)
    
    async def json(self) -> Any:
        """
        Parse request body as JSON.
        
        Returns:
            Parsed JSON data (dict, list, etc.)
            
        Raises:
            JSONDecodeError: If body is not valid JSON
            
        Example:
            >>> data = await ctx.json()
            >>> print(data["name"])
        """
        return await self._request.json()
    
    async def body(self) -> bytes:
        """
        Get raw request body.
        
        Returns:
            Raw body bytes
            
        Example:
            >>> body = await ctx.body()
            >>> print(len(body))
        """
        return await self._request.body()
    
    async def form(self) -> Dict[str, Any]:
        """
        Parse request body as form data.
        
        Returns:
            Form data as dictionary
            
        Example:
            >>> form_data = await ctx.form()
            >>> username = form_data.get("username")
        """
        return await self._request.form()
    
    def get(self, key: str, default: Any = None) -> Any:
        """
        Get value from context state.
        
        Args:
            key: State key
            default: Default value if key doesn't exist
            
        Returns:
            Stored value or default
            
        Example:
            >>> user_id = ctx.get("user_id")
            >>> cache = ctx.get("cache", default_cache)
        """
        return self._state.get(key, default)
    
    def set(self, key: str, value: Any) -> None:
        """
        Set value in context state.
        
        Args:
            key: State key
            value: Value to store
            
        Example:
            >>> ctx.set("user_id", "123")
            >>> ctx.set("cache", redis_client)
        """
        self._state[key] = value
    
    def has(self, key: str) -> bool:
        """
        Check if key exists in context state.
        
        Args:
            key: State key
            
        Returns:
            True if key exists
            
        Example:
            >>> if ctx.has("user_id"):
            ...     user_id = ctx.get("user_id")
        """
        return key in self._state
    
    @property
    def elapsed_time(self) -> float:
        """
        Get elapsed time since request started.
        
        Returns:
            Elapsed seconds
            
        Example:
            >>> print(f"Request took {ctx.elapsed_time:.3f}s")
        """
        return time.time() - self._start_time
    
    def __repr__(self) -> str:
        """String representation of context."""
        return f"<RequestContext {self.method} {self.path}>"
```

---

## 🛤️ Router Implementation

### `api_starter/core/router.py`

```python
"""
URL Router
==========

Routes incoming requests to the appropriate handler.

The router matches URL patterns to registered handlers and extracts
path parameters for dependency injection.

Example:
    >>> router = Router()
    >>> router.add_route("/users/{user_id}", get_user, methods=["GET"])
    >>> 
    >>> # Router automatically extracts user_id
    >>> async def get_user(ctx: RequestContext, user_id: str):
    ...     return {"user_id": user_id}
"""

from __future__ import annotations

import re
from typing import Any, Callable, Dict, List, Optional, Pattern

from starlette.routing import Route

from api_starter.core.context import RequestContext


class RoutePattern:
    """
    Compiled route pattern with parameter extraction.
    
    Converts path patterns like "/users/{user_id}" into regex patterns
    that can match URLs and extract parameters.
    
    Example:
        >>> pattern = RoutePattern("/users/{user_id}/posts/{post_id}")
        >>> match = pattern.match("/users/123/posts/456")
        >>> print(match)  # {"user_id": "123", "post_id": "456"}
    """
    
    def __init__(self, path: str) -> None:
        """
        Initialize route pattern.
        
        Args:
            path: Path pattern with {param} placeholders
        """
        self.path = path
        self.param_names: List[str] = []
        self.regex: Pattern = self._compile_pattern(path)
    
    def _compile_pattern(self, path: str) -> Pattern:
        """
        Compile path pattern to regex.
        
        Args:
            path: Path pattern
            
        Returns:
            Compiled regex pattern
        """
        # Find all {param} patterns
        param_pattern = re.compile(r'\{([a-zA-Z_][a-zA-Z0-9_]*)\}')
        
        # Extract parameter names
        self.param_names = param_pattern.findall(path)
        
        # Replace {param} with regex capture groups
        regex_path = param_pattern.sub(r'([^/]+)', path)
        
        # Anchor to start and end
        regex_path = f'^{regex_path}$'
        
        return re.compile(regex_path)
    
    def match(self, path: str) -> Optional[Dict[str, str]]:
        """
        Match path and extract parameters.
        
        Args:
            path: Request path
            
        Returns:
            Dictionary of parameters or None if no match
        """
        match = self.regex.match(path)
        if not match:
            return None
        
        # Zip parameter names with captured values
        return dict(zip(self.param_names, match.groups()))


class Router:
    """
    URL router for matching paths to handlers.
    
    The router maintains a registry of routes and provides efficient
    matching of incoming requests to the appropriate handler.
    
    Example:
        >>> router = Router()
        >>> 
        >>> @app.endpoint("/users/{user_id}", methods=["GET"])
        >>> async def get_user(ctx: RequestContext, user_id: str):
        ...     return {"user_id": user_id}
    """
    
    def __init__(self) -> None:
        """Initialize empty router."""
        self._routes: List[Dict[str, Any]] = []
    
    def add_route(
        self,
        path: str,
        handler: Callable,
        methods: List[str] = ["GET"],
        name: Optional[str] = None,
        tags: Optional[List[str]] = None,
        summary: Optional[str] = None,
        description: Optional[str] = None,
    ) -> None:
        """
        Register a route.
        
        Args:
            path: URL path pattern (e.g., "/users/{user_id}")
            handler: Async function to handle requests
            methods: List of HTTP methods
            name: Route name for URL generation
            tags: Tags for documentation
            summary: Short summary
            description: Detailed description
        """
        route = {
            "path": path,
            "pattern": RoutePattern(path),
            "handler": handler,
            "methods": [m.upper() for m in methods],
            "name": name or handler.__name__,
            "tags": tags or [],
            "summary": summary,
            "description": description,
        }
        
        self._routes.append(route)
    
    def get_routes(self) -> List[Route]:
        """
        Get Starlette Route objects.
        
        Returns:
            List of Starlette Route objects
        """
        starlette_routes = []
        
        for route in self._routes:
            starlette_route = Route(
                path=route["path"],
                endpoint=route["handler"],
                methods=route["methods"],
                name=route["name"],
            )
            starlette_routes.append(starlette_route)
        
        return starlette_routes
    
    def match(self, path: str, method: str) -> Optional[Dict[str, Any]]:
        """
        Match path and method to a route.
        
        Args:
            path: Request path
            method: HTTP method
            
        Returns:
            Route info with parameters or None
        """
        for route in self._routes:
            if method.upper() not in route["methods"]:
                continue
            
            params = route["pattern"].match(path)
            if params is not None:
                return {
                    "handler": route["handler"],
                    "params": params,
                    "route": route,
                }
        
        return None
```

---

[↑ Back to TOC](#-table-of-contents)
