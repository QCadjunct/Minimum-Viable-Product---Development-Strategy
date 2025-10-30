# 📁 Python API Starter - Repository Structure Guide

**Section 5 of 6: Testing Infrastructure**

---

## 📑 Table of Contents

- [Testing Directory Structure](#-testing-directory-structure)
- [Pytest Configuration](#-pytest-configuration)
- [Test Fixtures & Utilities](#-test-fixtures--utilities)
- [Unit Tests](#-unit-tests)
- [Integration Tests](#-integration-tests)
- [Coverage Configuration](#-coverage-configuration)
- [Testing Best Practices](#-testing-best-practices)

---

## 🧪 Testing Directory Structure

### Complete Test Structure

```
tests/
├── __init__.py
├── conftest.py                               # Pytest fixtures
├── pytest.ini                                # Pytest configuration
├── .coveragerc                               # Coverage configuration
│
├── unit/                                     # Unit tests
│   ├── __init__.py
│   ├── test_application.py                  # Application tests
│   ├── test_context.py                      # Context tests
│   ├── test_router.py                       # Router tests
│   ├── test_middleware.py                   # Middleware tests
│   ├── test_plugin.py                       # Plugin tests
│   └── test_models.py                       # Model tests
│
├── integration/                              # Integration tests
│   ├── __init__.py
│   ├── test_api_endpoints.py               # End-to-end API tests
│   ├── test_database.py                    # Database integration
│   ├── test_cache.py                       # Cache integration
│   └── test_authentication.py              # Auth integration
│
├── functional/                               # Functional tests
│   ├── __init__.py
│   ├── test_user_flows.py                  # User workflow tests
│   └── test_business_logic.py              # Business logic tests
│
├── performance/                              # Performance tests
│   ├── __init__.py
│   ├── test_load.py                        # Load testing
│   └── test_benchmarks.py                  # Benchmark tests
│
├── fixtures/                                 # Test data
│   ├── __init__.py
│   ├── database.py                         # Database fixtures
│   ├── api_client.py                       # API client fixtures
│   └── sample_data.py                      # Sample data generators
│
└── utils/                                    # Test utilities
    ├── __init__.py
    ├── helpers.py                          # Test helper functions
    ├── mocks.py                            # Mock objects
    └── assertions.py                       # Custom assertions
```

---

## ⚙️ Pytest Configuration

### `tests/pytest.ini`

```ini
# Pytest configuration file
# 
# Configures test discovery, plugins, and output formatting
# for the API Starter test suite.

[pytest]
# Test discovery patterns
python_files = test_*.py
python_classes = Test*
python_functions = test_*

# Minimum Python version
minversion = 7.0

# Add paths to Python path
pythonpath = .

# Test paths
testpaths = tests

# Markers for categorizing tests
markers =
    unit: Unit tests (fast, isolated)
    integration: Integration tests (require external services)
    functional: Functional tests (end-to-end scenarios)
    performance: Performance and load tests
    slow: Tests that take significant time
    asyncio: Async tests using pytest-asyncio
    database: Tests requiring database
    cache: Tests requiring Redis cache
    skip_ci: Skip in CI environment

# Async configuration
asyncio_mode = auto

# Output options
addopts =
    # Verbose output
    -v
    # Show local variables in tracebacks
    --showlocals
    # Show summary of all test outcomes
    -ra
    # Strict markers (fail on unknown markers)
    --strict-markers
    # Strict config (fail on config issues)
    --strict-config
    # Show warnings
    -W error
    # Coverage (when --cov flag used)
    --cov-report=term-missing
    --cov-report=html
    --cov-report=xml
    # Parallel execution (when -n flag used)
    # --numprocesses=auto

# Ignore patterns
norecursedirs = 
    .git
    .tox
    dist
    build
    *.egg
    __pycache__
    .venv
    venv

# Timeout for tests (seconds)
timeout = 300

# Logging
log_cli = true
log_cli_level = INFO
log_cli_format = %(asctime)s [%(levelname)8s] %(message)s
log_cli_date_format = %Y-%m-%d %H:%M:%S

# Warnings
filterwarnings =
    error
    ignore::DeprecationWarning
    ignore::PendingDeprecationWarning
```

### `tests/.coveragerc`

```ini
# Coverage configuration
#
# Configures code coverage measurement for tests

[run]
# Measure branch coverage
branch = True

# Source packages to measure
source = api_starter

# Omit these files from coverage
omit =
    */tests/*
    */test_*.py
    */__pycache__/*
    */venv/*
    */.venv/*
    */site-packages/*
    */conftest.py

# Parallel mode for pytest-xdist
parallel = True

[report]
# Precision for coverage percentages
precision = 2

# Show missing lines
show_missing = True

# Skip covered files in report
skip_covered = False

# Skip empty files
skip_empty = True

# Fail if coverage below this threshold
fail_under = 90

# Exclude lines from coverage
exclude_lines =
    # Standard pragma
    pragma: no cover
    
    # Don't complain about missing debug code
    def __repr__
    def __str__
    
    # Don't complain if tests don't hit defensive code
    raise AssertionError
    raise NotImplementedError
    
    # Don't complain if non-runnable code isn't run
    if __name__ == .__main__.:
    if TYPE_CHECKING:
    
    # Don't complain about abstract methods
    @abstractmethod
    @abc.abstractmethod
    
    # Don't complain about overload definitions
    @overload

[html]
# Directory for HTML coverage report
directory = htmlcov

[xml]
# Output file for XML coverage report
output = coverage.xml
```

---

## 🔧 Test Fixtures & Utilities

### `tests/conftest.py`

```python
"""
Pytest configuration and shared fixtures.

This module provides fixtures and configuration used across all tests.
Fixtures are automatically discovered by pytest and available to all test modules.

Example:
    >>> def test_something(app):
    ...     '''Test using the app fixture.'''
    ...     assert app.title == "Test API"
"""

from __future__ import annotations

import asyncio
import os
from typing import AsyncGenerator, Generator

import pytest
from httpx import AsyncClient
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import sessionmaker

from api_starter import APIStarter
from api_starter.core.context import RequestContext


# ============================================================================
# Pytest Configuration
# ============================================================================

def pytest_configure(config):
    """
    Configure pytest environment.
    
    Called once at the start of the test session.
    
    Args:
        config: Pytest config object
    """
    # Set test environment
    os.environ["TESTING"] = "1"
    os.environ["DATABASE_URL"] = "postgresql://localhost/test_db"
    
    # Register custom markers
    config.addinivalue_line(
        "markers",
        "unit: Unit tests (fast, isolated)"
    )
    config.addinivalue_line(
        "markers",
        "integration: Integration tests"
    )


def pytest_collection_modifyitems(config, items):
    """
    Modify test items after collection.
    
    Automatically marks tests based on their location.
    
    Args:
        config: Pytest config object
        items: List of collected test items
    """
    for item in items:
        # Auto-mark based on test path
        if "unit" in item.nodeid:
            item.add_marker(pytest.mark.unit)
        elif "integration" in item.nodeid:
            item.add_marker(pytest.mark.integration)
        
        # Mark async tests
        if asyncio.iscoroutinefunction(item.function):
            item.add_marker(pytest.mark.asyncio)


# ============================================================================
# Event Loop Fixtures
# ============================================================================

@pytest.fixture(scope="session")
def event_loop() -> Generator[asyncio.AbstractEventLoop, None, None]:
    """
    Create event loop for async tests.
    
    Yields:
        Event loop for the test session
        
    Note:
        Session-scoped to avoid creating/closing loop for each test
    """
    loop = asyncio.get_event_loop_policy().new_event_loop()
    yield loop
    loop.close()


# ============================================================================
# Application Fixtures
# ============================================================================

@pytest.fixture
def app() -> APIStarter:
    """
    Create test application instance.
    
    Returns:
        Configured APIStarter instance
        
    Example:
        >>> def test_endpoint(app):
        ...     @app.endpoint("/test", methods=["GET"])
        ...     async def test_handler(ctx):
        ...         return {"status": "ok"}
    """
    return APIStarter(
        title="Test API",
        version="1.0.0",
        description="Test application",
        debug=True
    )


@pytest.fixture
async def client(app: APIStarter) -> AsyncGenerator[AsyncClient, None]:
    """
    Create async HTTP client for testing endpoints.
    
    Args:
        app: Test application fixture
        
    Yields:
        Configured AsyncClient
        
    Example:
        >>> async def test_endpoint(client):
        ...     response = await client.get("/test")
        ...     assert response.status_code == 200
    """
    async with AsyncClient(app=app, base_url="http://test") as client:
        yield client


# ============================================================================
# Database Fixtures
# ============================================================================

@pytest.fixture(scope="session")
async def db_engine():
    """
    Create database engine for testing.
    
    Yields:
        SQLAlchemy async engine
        
    Note:
        Session-scoped to reuse connection pool across tests
    """
    engine = create_async_engine(
        "postgresql+asyncpg://localhost/test_db",
        echo=False,
        pool_pre_ping=True
    )
    
    yield engine
    
    await engine.dispose()


@pytest.fixture
async def db_session(db_engine) -> AsyncGenerator[AsyncSession, None]:
    """
    Create database session for testing.
    
    Each test gets a fresh session that is rolled back after the test.
    
    Args:
        db_engine: Database engine fixture
        
    Yields:
        SQLAlchemy async session
        
    Example:
        >>> async def test_create_user(db_session):
        ...     user = User(name="Test")
        ...     db_session.add(user)
        ...     await db_session.flush()
        ...     assert user.id is not None
    """
    async_session = sessionmaker(
        db_engine,
        class_=AsyncSession,
        expire_on_commit=False
    )
    
    async with async_session() as session:
        async with session.begin():
            yield session
            await session.rollback()


@pytest.fixture(autouse=True)
async def setup_database(db_engine):
    """
    Set up database schema before tests.
    
    Args:
        db_engine: Database engine fixture
        
    Note:
        autouse=True means this runs automatically for all tests
    """
    from api_starter.database.models import Base
    
    async with db_engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    
    yield
    
    async with db_engine.begin() as conn:
        await conn.run_sync(Base.metadata.drop_all)


# ============================================================================
# Cache Fixtures
# ============================================================================

@pytest.fixture
async def redis_client():
    """
    Create Redis client for testing.
    
    Yields:
        Redis client instance
        
    Example:
        >>> async def test_cache(redis_client):
        ...     await redis_client.set("key", "value")
        ...     result = await redis_client.get("key")
        ...     assert result == "value"
    """
    import redis.asyncio as redis
    
    client = redis.from_url(
        "redis://localhost:6379/15",  # Use database 15 for tests
        encoding="utf-8",
        decode_responses=True
    )
    
    yield client
    
    # Clean up after test
    await client.flushdb()
    await client.close()


# ============================================================================
# Mock Fixtures
# ============================================================================

@pytest.fixture
def mock_user():
    """
    Create mock user for testing.
    
    Returns:
        Mock user dictionary
        
    Example:
        >>> def test_user_endpoint(mock_user):
        ...     assert mock_user["email"] == "test@example.com"
    """
    return {
        "id": "user-123",
        "email": "test@example.com",
        "name": "Test User",
        "role": "user"
    }


@pytest.fixture
def mock_context(app: APIStarter) -> RequestContext:
    """
    Create mock request context.
    
    Args:
        app: Test application fixture
        
    Returns:
        Mock RequestContext instance
        
    Example:
        >>> def test_handler(mock_context):
        ...     mock_context.set("user_id", "123")
        ...     assert mock_context.get("user_id") == "123"
    """
    from unittest.mock import Mock
    
    mock_request = Mock()
    mock_request.method = "GET"
    mock_request.url.path = "/test"
    mock_request.headers = {}
    mock_request.query_params = {}
    mock_request.path_params = {}
    
    return RequestContext(mock_request)


# ============================================================================
# Authentication Fixtures
# ============================================================================

@pytest.fixture
def auth_token(mock_user) -> str:
    """
    Generate authentication token for testing.
    
    Args:
        mock_user: Mock user fixture
        
    Returns:
        JWT authentication token
        
    Example:
        >>> async def test_protected_endpoint(client, auth_token):
        ...     response = await client.get(
        ...         "/protected",
        ...         headers={"Authorization": f"Bearer {auth_token}"}
        ...     )
        ...     assert response.status_code == 200
    """
    from api_starter.security.jwt import create_access_token
    
    return create_access_token(
        user_id=mock_user["id"],
        role=mock_user["role"]
    )


@pytest.fixture
async def authenticated_client(
    client: AsyncClient,
    auth_token: str
) -> AsyncClient:
    """
    Create authenticated HTTP client.
    
    Args:
        client: HTTP client fixture
        auth_token: Authentication token fixture
        
    Returns:
        Client with authentication headers
        
    Example:
        >>> async def test_profile(authenticated_client):
        ...     response = await authenticated_client.get("/profile")
        ...     assert response.status_code == 200
    """
    client.headers["Authorization"] = f"Bearer {auth_token}"
    return client
```

### `tests/fixtures/sample_data.py`

```python
"""
Sample data generators for tests.

Provides factory functions to generate test data with realistic values.

Example:
    >>> task = create_task_data(title="Test Task")
    >>> user = create_user_data(email="test@example.com")
"""

from datetime import datetime, timedelta
from typing import Dict, List, Optional
from uuid import uuid4


def create_task_data(
    title: str = "Test Task",
    description: Optional[str] = None,
    completed: bool = False,
    priority: str = "medium",
    due_date: Optional[datetime] = None,
    tags: Optional[List[str]] = None
) -> Dict:
    """
    Generate sample task data.
    
    Args:
        title: Task title
        description: Task description
        completed: Whether task is completed
        priority: Task priority (low, medium, high)
        due_date: Task due date
        tags: List of tags
        
    Returns:
        Dictionary with task data
        
    Example:
        >>> task = create_task_data(title="Write tests")
        >>> assert task["title"] == "Write tests"
        >>> assert task["completed"] is False
    """
    return {
        "id": str(uuid4()),
        "title": title,
        "description": description or f"Description for {title}",
        "completed": completed,
        "priority": priority,
        "due_date": due_date or (datetime.utcnow() + timedelta(days=7)),
        "tags": tags or ["test"],
        "created_at": datetime.utcnow(),
        "updated_at": datetime.utcnow()
    }


def create_user_data(
    email: str = "test@example.com",
    name: str = "Test User",
    role: str = "user",
    active: bool = True
) -> Dict:
    """
    Generate sample user data.
    
    Args:
        email: User email
        name: User name
        role: User role (user, admin, guest)
        active: Whether user is active
        
    Returns:
        Dictionary with user data
        
    Example:
        >>> user = create_user_data(email="admin@example.com", role="admin")
        >>> assert user["role"] == "admin"
    """
    return {
        "id": str(uuid4()),
        "email": email,
        "name": name,
        "role": role,
        "active": active,
        "created_at": datetime.utcnow(),
        "updated_at": datetime.utcnow()
    }


def create_batch_tasks(count: int = 10) -> List[Dict]:
    """
    Generate multiple sample tasks.
    
    Args:
        count: Number of tasks to generate
        
    Returns:
        List of task dictionaries
        
    Example:
        >>> tasks = create_batch_tasks(5)
        >>> assert len(tasks) == 5
        >>> assert all("id" in task for task in tasks)
    """
    return [
        create_task_data(
            title=f"Task {i}",
            priority=["low", "medium", "high"][i % 3],
            completed=i % 2 == 0
        )
        for i in range(count)
    ]
```

---

## 🧪 Unit Tests

### `tests/unit/test_application.py`

```python
"""
Unit tests for the Application class.

Tests core application functionality including:
- Application initialization
- Endpoint registration
- Middleware management
- Plugin system
- Lifecycle hooks

Run with: pytest tests/unit/test_application.py -v
"""

import pytest
from unittest.mock import AsyncMock, Mock, patch

from api_starter import APIStarter, RequestContext
from api_starter.core.middleware import Middleware
from api_starter.core.plugin import Plugin


@pytest.mark.unit
class TestApplicationInitialization:
    """Test application initialization and configuration."""
    
    def test_create_application_with_defaults(self):
        """
        Test creating application with default parameters.
        
        Verifies that the application is created with sensible defaults
        when no parameters are provided.
        """
        # Arrange & Act
        app = APIStarter()
        
        # Assert
        assert app.title == "API Starter"
        assert app.version == "1.0.0"
        assert app.debug is False
        assert app.docs_url == "/docs"
    
    def test_create_application_with_custom_config(self):
        """
        Test creating application with custom configuration.
        
        Verifies that custom configuration values are properly set
        during application initialization.
        """
        # Arrange & Act
        app = APIStarter(
            title="Test API",
            version="2.0.0",
            description="Test Description",
            debug=True,
            docs_url="/api/docs"
        )
        
        # Assert
        assert app.title == "Test API"
        assert app.version == "2.0.0"
        assert app.description == "Test Description"
        assert app.debug is True
        assert app.docs_url == "/api/docs"
    
    def test_disable_docs(self):
        """
        Test disabling documentation endpoints.
        
        Verifies that documentation URLs can be disabled by setting
        them to None.
        """
        # Arrange & Act
        app = APIStarter(
            docs_url=None,
            redoc_url=None,
            openapi_url=None
        )
        
        # Assert
        assert app.docs_url is None
        assert app.redoc_url is None
        assert app.openapi_url is None


@pytest.mark.unit
class TestEndpointRegistration:
    """Test endpoint registration and routing."""
    
    def test_register_simple_endpoint(self, app: APIStarter):
        """
        Test registering a simple GET endpoint.
        
        Verifies that endpoints can be registered using the @app.endpoint
        decorator and are added to the router.
        """
        # Arrange
        @app.endpoint("/test", methods=["GET"])
        async def test_handler(ctx: RequestContext):
            return {"status": "ok"}
        
        # Act
        routes = app._router._routes
        
        # Assert
        assert len(routes) == 1
        assert routes[0]["path"] == "/test"
        assert routes[0]["methods"] == ["GET"]
        assert routes[0]["handler"] == test_handler
    
    def test_register_multiple_methods(self, app: APIStarter):
        """
        Test registering endpoint with multiple HTTP methods.
        
        Verifies that a single endpoint can handle multiple HTTP methods.
        """
        # Arrange & Act
        @app.endpoint("/users", methods=["GET", "POST", "PUT"])
        async def users_handler(ctx: RequestContext):
            return {}
        
        # Assert
        routes = app._router._routes
        assert routes[0]["methods"] == ["GET", "POST", "PUT"]
    
    def test_endpoint_with_path_parameters(self, app: APIStarter):
        """
        Test registering endpoint with path parameters.
        
        Verifies that path parameters are properly extracted from
        the route pattern.
        """
        # Arrange & Act
        @app.endpoint("/users/{user_id}/posts/{post_id}", methods=["GET"])
        async def get_post(ctx: RequestContext, user_id: str, post_id: str):
            return {"user_id": user_id, "post_id": post_id}
        
        # Assert
        route = app._router._routes[0]
        assert route["path"] == "/users/{user_id}/posts/{post_id}"
        assert len(route["pattern"].param_names) == 2
        assert "user_id" in route["pattern"].param_names
        assert "post_id" in route["pattern"].param_names
    
    def test_endpoint_metadata(self, app: APIStarter):
        """
        Test endpoint registration with metadata.
        
        Verifies that endpoint metadata (name, tags, summary, description)
        is properly stored during registration.
        """
        # Arrange & Act
        @app.endpoint(
            "/users",
            methods=["GET"],
            name="list_users",
            tags=["users", "public"],
            summary="List all users",
            description="Returns a paginated list of all users"
        )
        async def list_users(ctx: RequestContext):
            return []
        
        # Assert
        route = app._router._routes[0]
        assert route["name"] == "list_users"
        assert route["tags"] == ["users", "public"]
        assert route["summary"] == "List all users"
        assert "paginated list" in route["description"]


@pytest.mark.unit
class TestMiddleware:
    """Test middleware management."""
    
    def test_add_middleware(self, app: APIStarter):
        """
        Test adding middleware to application.
        
        Verifies that middleware can be added and is stored in the
        correct order.
        """
        # Arrange
        class TestMiddleware(Middleware):
            async def process_request(self, ctx: RequestContext):
                pass
        
        middleware = TestMiddleware()
        
        # Act
        app.add_middleware(middleware)
        
        # Assert
        assert len(app._middleware) == 1
        assert app._middleware[0] == middleware
    
    def test_middleware_execution_order(self, app: APIStarter):
        """
        Test middleware execution order.
        
        Verifies that middleware is executed in the order it was added
        for requests (FIFO).
        """
        # Arrange
        execution_order = []
        
        class Middleware1(Middleware):
            async def process_request(self, ctx: RequestContext):
                execution_order.append("middleware1")
        
        class Middleware2(Middleware):
            async def process_request(self, ctx: RequestContext):
                execution_order.append("middleware2")
        
        # Act
        app.add_middleware(Middleware1())
        app.add_middleware(Middleware2())
        
        # Assert
        assert len(app._middleware) == 2
        # Verify order is preserved
        assert isinstance(app._middleware[0], Middleware1)
        assert isinstance(app._middleware[1], Middleware2)


@pytest.mark.unit
class TestPluginSystem:
    """Test plugin management."""
    
    def test_add_plugin(self, app: APIStarter):
        """
        Test adding plugin to application.
        
        Verifies that plugins can be added and are registered
        with their unique names.
        """
        # Arrange
        class TestPlugin(Plugin):
            def __init__(self):
                super().__init__(name="test_plugin")
            
            async def initialize(self, app):
                pass
        
        plugin = TestPlugin()
        
        # Act
        app.add_plugin(plugin)
        
        # Assert
        assert "test_plugin" in app._plugins
        assert app._plugins["test_plugin"] == plugin
    
    def test_duplicate_plugin_raises_error(self, app: APIStarter):
        """
        Test that adding duplicate plugin raises error.
        
        Verifies that attempting to add a plugin with a name that
        already exists raises a ValueError.
        """
        # Arrange
        class TestPlugin(Plugin):
            def __init__(self):
                super().__init__(name="duplicate")
            
            async def initialize(self, app):
                pass
        
        plugin1 = TestPlugin()
        plugin2 = TestPlugin()
        
        # Act
        app.add_plugin(plugin1)
        
        # Assert
        with pytest.raises(ValueError, match="already registered"):
            app.add_plugin(plugin2)
    
    @pytest.mark.asyncio
    async def test_plugin_initialization(self, app: APIStarter):
        """
        Test plugin initialization during application startup.
        
        Verifies that plugins are properly initialized when the
        application starts.
        """
        # Arrange
        initialized = []
        
        class TestPlugin(Plugin):
            def __init__(self, name: str):
                super().__init__(name=name)
            
            async def initialize(self, app):
                initialized.append(self.name)
        
        plugin1 = TestPlugin("plugin1")
        plugin2 = TestPlugin("plugin2")
        
        app.add_plugin(plugin1)
        app.add_plugin(plugin2)
        
        # Act
        await app._initialize_plugins()
        
        # Assert
        assert len(initialized) == 2
        assert "plugin1" in initialized
        assert "plugin2" in initialized


@pytest.mark.unit
class TestLifecycleHooks:
    """Test application lifecycle hooks."""
    
    @pytest.mark.asyncio
    async def test_startup_hook(self, app: APIStarter):
        """
        Test startup hook execution.
        
        Verifies that functions decorated with @app.on_startup are
        called when the application starts.
        """
        # Arrange
        startup_called = []
        
        @app.on_startup
        async def startup_handler():
            startup_called.append(True)
        
        # Act
        await app._on_startup()
        
        # Assert
        assert len(startup_called) == 1
        assert startup_called[0] is True
    
    @pytest.mark.asyncio
    async def test_shutdown_hook(self, app: APIStarter):
        """
        Test shutdown hook execution.
        
        Verifies that functions decorated with @app.on_shutdown are
        called when the application stops.
        """
        # Arrange
        shutdown_called = []
        
        @app.on_shutdown
        async def shutdown_handler():
            shutdown_called.append(True)
        
        # Act
        await app._on_shutdown()
        
        # Assert
        assert len(shutdown_called) == 1
        assert shutdown_called[0] is True
    
    @pytest.mark.asyncio
    async def test_multiple_startup_hooks(self, app: APIStarter):
        """
        Test multiple startup hooks execution.
        
        Verifies that multiple startup hooks are all executed
        in the order they were registered.
        """
        # Arrange
        execution_order = []
        
        @app.on_startup
        async def startup1():
            execution_order.append("startup1")
        
        @app.on_startup
        async def startup2():
            execution_order.append("startup2")
        
        # Act
        await app._on_startup()
        
        # Assert
        assert execution_order == ["startup1", "startup2"]
```

### `tests/unit/test_context.py`

```python
"""
Unit tests for RequestContext class.

Tests request context functionality including:
- Property access (method, path, headers, etc.)
- State management (get, set, has)
- Request body parsing
- URL and parameter extraction

Run with: pytest tests/unit/test_context.py -v
"""

import pytest
from unittest.mock import Mock, AsyncMock

from api_starter.core.context import RequestContext


@pytest.mark.unit
class TestContextProperties:
    """Test request context properties."""
    
    def test_method_property(self, mock_context: RequestContext):
        """
        Test accessing HTTP method.
        
        Verifies that the method property returns the correct
        HTTP method from the request.
        """
        # Arrange
        mock_context._request.method = "POST"
        
        # Act
        method = mock_context.method
        
        # Assert
        assert method == "POST"
    
    def test_path_property(self, mock_context: RequestContext):
        """
        Test accessing request path.
        
        Verifies that the path property returns the URL path
        without query parameters.
        """
        # Arrange
        mock_context._request.url.path = "/api/users/123"
        
        # Act
        path = mock_context.path
        
        # Assert
        assert path == "/api/users/123"
    
    def test_headers_property(self, mock_context: RequestContext):
        """
        Test accessing request headers.
        
        Verifies that headers can be accessed in a case-insensitive manner.
        """
        # Arrange
        from starlette.datastructures import Headers
        mock_context._request.headers = Headers({
            "content-type": "application/json",
            "authorization": "Bearer token123"
        })
        
        # Act
        headers = mock_context.headers
        
        # Assert
        assert headers.get("content-type") == "application/json"
        assert headers.get("Authorization") == "Bearer token123"  # Case-insensitive
    
    def test_query_params(self, mock_context: RequestContext):
        """
        Test accessing query parameters.
        
        Verifies that query parameters can be accessed from the context.
        """
        # Arrange
        from starlette.datastructures import QueryParams
        mock_context._request.query_params = QueryParams("page=1&limit=20")
        
        # Act
        query = mock_context.query
        
        # Assert
        assert query.get("page") == "1"
        assert query.get("limit") == "20"
    
    def test_path_params(self, mock_context: RequestContext):
        """
        Test accessing path parameters.
        
        Verifies that path parameters extracted from the URL pattern
        are available in the context.
        """
        # Arrange
        mock_context._request.path_params = {
            "user_id": "123",
            "post_id": "456"
        }
        
        # Act
        params = mock_context.path_params
        
        # Assert
        assert params["user_id"] == "123"
        assert params["post_id"] == "456"
    
    def test_client_ip(self, mock_context: RequestContext):
        """
        Test accessing client IP address.
        
        Verifies that the client IP can be extracted from the request.
        """
        # Arrange
        mock_client = Mock()
        mock_client.host = "192.168.1.100"
        mock_context._request.client = mock_client
        
        # Act
        ip = mock_context.client_ip
        
        # Assert
        assert ip == "192.168.1.100"


@pytest.mark.unit
class TestStateManagement:
    """Test context state management."""
    
    def test_set_and_get_state(self, mock_context: RequestContext):
        """
        Test storing and retrieving state.
        
        Verifies that custom data can be stored in the context
        and retrieved later.
        """
        # Arrange & Act
        mock_context.set("user_id", "123")
        mock_context.set("authenticated", True)
        
        # Assert
        assert mock_context.get("user_id") == "123"
        assert mock_context.get("authenticated") is True
    
    def test_get_with_default(self, mock_context: RequestContext):
        """
        Test retrieving state with default value.
        
        Verifies that a default value is returned when the
        requested key doesn't exist.
        """
        # Act
        value = mock_context.get("nonexistent", default="default_value")
        
        # Assert
        assert value == "default_value"
    
    def test_has_state(self, mock_context: RequestContext):
        """
        Test checking if state exists.
        
        Verifies that the has() method correctly identifies
        whether a key exists in the state.
        """
        # Arrange
        mock_context.set("exists", "value")
        
        # Act & Assert
        assert mock_context.has("exists") is True
        assert mock_context.has("nonexistent") is False
    
    def test_overwrite_state(self, mock_context: RequestContext):
        """
        Test overwriting existing state.
        
        Verifies that setting a key that already exists
        updates the value.
        """
        # Arrange
        mock_context.set("key", "original")
        
        # Act
        mock_context.set("key", "updated")
        
        # Assert
        assert mock_context.get("key") == "updated"


@pytest.mark.unit
@pytest.mark.asyncio
class TestBodyParsing:
    """Test request body parsing methods."""
    
    async def test_parse_json_body(self, mock_context: RequestContext):
        """
        Test parsing JSON request body.
        
        Verifies that JSON data can be parsed from the request body.
        """
        # Arrange
        mock_context._request.json = AsyncMock(
            return_value={"name": "Test", "age": 30}
        )
        
        # Act
        data = await mock_context.json()
        
        # Assert
        assert data["name"] == "Test"
        assert data["age"] == 30
    
    async def test_parse_raw_body(self, mock_context: RequestContext):
        """
        Test getting raw request body.
        
        Verifies that raw bytes can be retrieved from the request body.
        """
        # Arrange
        mock_context._request.body = AsyncMock(
            return_value=b"raw data"
        )
        
        # Act
        body = await mock_context.body()
        
        # Assert
        assert body == b"raw data"
    
    async def test_parse_form_data(self, mock_context: RequestContext):
        """
        Test parsing form data.
        
        Verifies that form-encoded data can be parsed.
        """
        # Arrange
        mock_context._request.form = AsyncMock(
            return_value={"username": "testuser", "password": "secret"}
        )
        
        # Act
        form = await mock_context.form()
        
        # Assert
        assert form["username"] == "testuser"
        assert form["password"] == "secret"


@pytest.mark.unit
class TestContextUtilities:
    """Test context utility methods."""
    
    def test_elapsed_time(self, mock_context: RequestContext):
        """
        Test elapsed time tracking.
        
        Verifies that the context tracks how long the request
        has been processing.
        """
        # Arrange
        import time
        
        # Act
        time.sleep(0.1)  # Sleep for 100ms
        elapsed = mock_context.elapsed_time
        
        # Assert
        assert elapsed >= 0.1
        assert elapsed < 0.2  # Should be around 100ms
    
    def test_context_repr(self, mock_context: RequestContext):
        """
        Test string representation of context.
        
        Verifies that the context has a useful string representation
        for debugging.
        """
        # Arrange
        mock_context._request.method = "POST"
        mock_context._request.url.path = "/api/users"
        
        # Act
        repr_str = repr(mock_context)
        
        # Assert
        assert "POST" in repr_str
        assert "/api/users" in repr_str
```

---

## 🔗 Integration Tests

### `tests/integration/test_api_endpoints.py`

```python
"""
Integration tests for API endpoints.

Tests complete request/response cycle including:
- Endpoint handling
- Middleware processing
- Request validation
- Response formatting
- Error handling

Run with: pytest tests/integration/test_api_endpoints.py -v
"""

import pytest
from httpx import AsyncClient
from pydantic import BaseModel

from api_starter import APIStarter, RequestContext


@pytest.mark.integration
@pytest.mark.asyncio
class TestBasicEndpoints:
    """Test basic endpoint functionality."""
    
    async def test_get_endpoint(self, client: AsyncClient, app: APIStarter):
        """
        Test GET endpoint returns correct response.
        
        Verifies end-to-end GET request handling including routing,
        handler execution, and response formatting.
        """
        # Arrange
        @app.endpoint("/test", methods=["GET"])
        async def get_test(ctx: RequestContext):
            return {"message": "success"}
        
        # Act
        response = await client.get("/test")
        
        # Assert
        assert response.status_code == 200
        assert response.json() == {"message": "success"}
    
    async def test_post_endpoint_with_json(
        self,
        client: AsyncClient,
        app: APIStarter
    ):
        """
        Test POST endpoint with JSON body.
        
        Verifies that JSON request bodies are properly parsed,
        validated, and processed.
        """
        # Arrange
        class CreateData(BaseModel):
            name: str
            value: int
        
        @app.endpoint("/create", methods=["POST"])
        async def create_item(ctx: RequestContext, data: CreateData):
            return {
                "created": True,
                "name": data.name,
                "value": data.value
            }
        
        # Act
        response = await client.post(
            "/create",
            json={"name": "test", "value": 42}
        )
        
        # Assert
        assert response.status_code == 200
        data = response.json()
        assert data["created"] is True
        assert data["name"] == "test"
        assert data["value"] == 42
    
    async def test_path_parameters(
        self,
        client: AsyncClient,
        app: APIStarter
    ):
        """
        Test endpoint with path parameters.
        
        Verifies that path parameters are properly extracted and
        passed to the handler.
        """
        # Arrange
        @app.endpoint("/users/{user_id}/posts/{post_id}", methods=["GET"])
        async def get_post(ctx: RequestContext, user_id: str, post_id: str):
            return {
                "user_id": user_id,
                "post_id": post_id
            }
        
        # Act
        response = await client.get("/users/123/posts/456")
        
        # Assert
        assert response.status_code == 200
        data = response.json()
        assert data["user_id"] == "123"
        assert data["post_id"] == "456"
    
    async def test_query_parameters(
        self,
        client: AsyncClient,
        app: APIStarter
    ):
        """
        Test endpoint with query parameters.
        
        Verifies that query parameters can be accessed and used
        in the endpoint handler.
        """
        # Arrange
        @app.endpoint("/search", methods=["GET"])
        async def search(ctx: RequestContext):
            query = ctx.query.get("q", "")
            page = ctx.query.get("page", "1")
            return {
                "query": query,
                "page": int(page)
            }
        
        # Act
        response = await client.get("/search?q=test&page=2")
        
        # Assert
        assert response.status_code == 200
        data = response.json()
        assert data["query"] == "test"
        assert data["page"] == 2


@pytest.mark.integration
@pytest.mark.asyncio
class TestCRUDOperations:
    """Test CRUD operation endpoints."""
    
    async def test_create_read_update_delete_flow(
        self,
        client: AsyncClient,
        app: APIStarter
    ):
        """
        Test complete CRUD flow.
        
        Verifies that all CRUD operations work together correctly
        in a realistic workflow.
        """
        # Arrange
        storage = {}
        
        class Item(BaseModel):
            name: str
            value: int
        
        @app.endpoint("/items", methods=["POST"])
        async def create(ctx: RequestContext, item: Item):
            item_id = "item-1"
            storage[item_id] = item.model_dump()
            return {"id": item_id, **storage[item_id]}, 201
        
        @app.endpoint("/items/{item_id}", methods=["GET"])
        async def read(ctx: RequestContext, item_id: str):
            if item_id not in storage:
                return {"error": "Not found"}, 404
            return storage[item_id]
        
        @app.endpoint("/items/{item_id}", methods=["PUT"])
        async def update(ctx: RequestContext, item_id: str, item: Item):
            if item_id not in storage:
                return {"error": "Not found"}, 404
            storage[item_id] = item.model_dump()
            return storage[item_id]
        
        @app.endpoint("/items/{item_id}", methods=["DELETE"])
        async def delete(ctx: RequestContext, item_id: str):
            if item_id not in storage:
                return {"error": "Not found"}, 404
            del storage[item_id]
            return None, 204
        
        # Act & Assert - CREATE
        create_response = await client.post(
            "/items",
            json={"name": "test", "value": 100}
        )
        assert create_response.status_code == 201
        item_id = create_response.json()["id"]
        
        # Act & Assert - READ
        read_response = await client.get(f"/items/{item_id}")
        assert read_response.status_code == 200
        assert read_response.json()["name"] == "test"
        
        # Act & Assert - UPDATE
        update_response = await client.put(
            f"/items/{item_id}",
            json={"name": "updated", "value": 200}
        )
        assert update_response.status_code == 200
        assert update_response.json()["name"] == "updated"
        
        # Act & Assert - DELETE
        delete_response = await client.delete(f"/items/{item_id}")
        assert delete_response.status_code == 204
        
        # Verify item is gone
        get_deleted = await client.get(f"/items/{item_id}")
        assert get_deleted.status_code == 404
```

---

## 📊 Coverage Configuration

### `pyproject.toml` (Test Configuration Section)

```toml
[tool.pytest.ini_options]
minversion = "7.0"
addopts = "-ra -q --strict-markers --strict-config"
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]

[tool.coverage.run]
branch = true
source = ["api_starter"]
omit = [
    "*/tests/*",
    "*/__pycache__/*",
    "*/venv/*",
    "*/.venv/*",
]

[tool.coverage.report]
precision = 2
show_missing = true
skip_covered = false
fail_under = 90

[tool.coverage.html]
directory = "htmlcov"
```

---

## ✅ Testing Best Practices

### Testing Guidelines Document

Create `tests/TESTING_GUIDE.md`:

```markdown
# Testing Guidelines

## Test Organization

### Unit Tests
- Test single units in isolation
- Mock all external dependencies
- Fast execution (< 100ms per test)
- No I/O operations
- 100% code coverage target

### Integration Tests
- Test component interactions
- Use real databases (test instances)
- May use external services
- Slower execution acceptable
- Focus on critical paths

### Functional Tests
- Test complete user workflows
- End-to-end scenarios
- Use production-like setup
- Validate business requirements

## Naming Conventions

```python
def test_<functionality>_<scenario>():
    """Test that <functionality> <scenario>."""
    pass
```

Examples:
- `test_create_user_with_valid_data()`
- `test_login_fails_with_invalid_password()`
- `test_get_user_returns_404_when_not_found()`

## Test Structure (AAA Pattern)

```python
def test_example():
    """Test description."""
    # Arrange - Set up test data
    user = create_test_user()
    
    # Act - Execute the functionality
    result = user_service.authenticate(user.email, "password")
    
    # Assert - Verify the outcome
    assert result.success is True
```

## Fixtures

- Use fixtures for common setup
- Keep fixtures focused and reusable
- Document fixture purpose
- Clean up after tests

## Assertions

- One logical assertion per test
- Use descriptive assertion messages
- Test both positive and negative cases

## Coverage

- Aim for 90%+ coverage
- Focus on critical business logic
- Don't test framework code
- Don't chase 100% coverage blindly

## Running Tests

```bash
# All tests
pytest

# Unit tests only
pytest tests/unit/ -m unit

# Integration tests
pytest tests/integration/ -m integration

# With coverage
pytest --cov=api_starter --cov-report=html

# Parallel execution
pytest -n auto

# Verbose output
pytest -v

# Stop on first failure
pytest -x
```
```

---

[Continue to Section 6: CI/CD & Deployment Configuration →](#)

[↑ Back to TOC](#-table-of-contents)
