# 🚀 Python API Starter - Complete Implementation Guide

**Section 6 of 8: Advanced Patterns & Production Deployment**

---

## 📑 Table of Contents

- [🚀 Advanced Patterns & Production Deployment](#-advanced-patterns--production-deployment)
  - [Database Integration](#-database-integration)
  - [PostgreSQL with SQLAlchemy](#-postgresql-with-sqlalchemy)
  - [MongoDB Integration](#-mongodb-integration)
  - [WebSocket Support](#-websocket-support)
  - [Background Tasks & Job Queues](#-background-tasks--job-queues)
  - [Testing Strategies](#-testing-strategies)
  - [Production Configuration](#-production-configuration)
  - [Docker Deployment](#-docker-deployment)
  - [Kubernetes Deployment](#-kubernetes-deployment)
  - [CI/CD Pipelines](#-cicd-pipelines)
  - [Monitoring & Observability](#-monitoring--observability)
  - [Performance Tuning](#-performance-tuning)

---

## 💾 Database Integration

### Database Architecture Overview

```mermaid
graph TB
    subgraph API ["🌐    API    Layer"]
        EP1[📡 HTTP Endpoints<br/>REST API]
        EP2[🔮 GraphQL<br/>Flexible queries]
        EP3[⚡ gRPC<br/>High performance]
    end

    subgraph SERVICE ["💼    Service    Layer"]
        SV1[💼 Task Service<br/>Business logic]
        SV2[💼 User Service<br/>Business logic]
        SV3[💼 Notification Service<br/>Business logic]
    end

    subgraph REPOSITORY ["📦    Repository    Layer"]
        RP1[📦 Task Repository<br/>Data access]
        RP2[📦 User Repository<br/>Data access]
        RP3[📦 Notification Repository<br/>Data access]
    end

    subgraph ORM ["🔷    ORM    Layer"]
        OR1[🔷 SQLAlchemy<br/>Relational mapping]
        OR2[🔷 Motor/Beanie<br/>MongoDB driver]
    end

    subgraph DATABASE ["💾    Database    Layer"]
        DB1[(🐘 PostgreSQL<br/>Primary data)]
        DB2[(🍃 MongoDB<br/>Documents/logs)]
        DB3[(🔴 Redis<br/>Cache/sessions)]
    end

    subgraph MIGRATION ["🔄    Migration    &    Seeding"]
        MG1[📝 Alembic<br/>Schema migrations]
        MG2[🌱 Seed Scripts<br/>Initial data]
    end

    %% API to Service - Blue
    EP1 --> SV1
    EP1 --> SV2
    EP2 --> SV1
    EP2 --> SV2
    EP3 --> SV1
    linkStyle 0 stroke:#1976d2,stroke-width:3px
    linkStyle 1 stroke:#1976d2,stroke-width:3px
    linkStyle 2 stroke:#1976d2,stroke-width:3px
    linkStyle 3 stroke:#1976d2,stroke-width:3px
    linkStyle 4 stroke:#1976d2,stroke-width:3px

    %% Service to Repository - Purple
    SV1 --> RP1
    SV2 --> RP2
    SV3 --> RP3
    linkStyle 5 stroke:#7b1fa2,stroke-width:3px
    linkStyle 6 stroke:#7b1fa2,stroke-width:3px
    linkStyle 7 stroke:#7b1fa2,stroke-width:3px

    %% Repository to ORM - Teal
    RP1 --> OR1
    RP2 --> OR1
    RP3 --> OR2
    linkStyle 8 stroke:#00695c,stroke-width:3px
    linkStyle 9 stroke:#00695c,stroke-width:3px
    linkStyle 10 stroke:#00695c,stroke-width:3px

    %% ORM to Database - Orange
    OR1 --> DB1
    OR2 --> DB2
    RP1 --> DB3
    linkStyle 11 stroke:#f57c00,stroke-width:3px
    linkStyle 12 stroke:#f57c00,stroke-width:3px
    linkStyle 13 stroke:#f57c00,stroke-width:3px

    %% Migration connections - Green (dashed)
    MG1 -.-> DB1
    MG2 -.-> DB1
    MG2 -.-> DB2
    linkStyle 14 stroke:#388e3c,stroke-width:2px
    linkStyle 15 stroke:#388e3c,stroke-width:2px
    linkStyle 16 stroke:#388e3c,stroke-width:2px

    %% Subgraph styling
    style API fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style SERVICE fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style REPOSITORY fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style ORM fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style DATABASE fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    style MIGRATION fill:#fef7f7,stroke:#c2185b,stroke-width:3px,color:#000

    %% Node styling
    style EP1 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style EP2 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style EP3 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    
    style SV1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    
    style RP1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style RP2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style RP3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    
    style OR1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style OR2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    
    style DB1 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    style DB2 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    style DB3 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    
    style MG1 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style MG2 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
```

### Repository Pattern Benefits

| Benefit | Description |
|---------|-------------|
| **Separation of Concerns** | Business logic separate from data access |
| **Testability** | Easy to mock repositories for testing |
| **Flexibility** | Swap databases without changing business logic |
| **Consistency** | Centralized data access patterns |
| **Maintainability** | Single place to update queries |

[↑ Back to TOC](#-table-of-contents)

---

## 🐘 PostgreSQL with SQLAlchemy

### Installation

```bash
# Install PostgreSQL dependencies
pip install sqlalchemy asyncpg alembic

# Or install with extras
pip install -e ".[postgres]"

# Start PostgreSQL (Docker)
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=todo_db \
  -p 5432:5432 \
  postgres:15-alpine
```

### Database Configuration

Create `app/database/config.py`:

```python
"""
Database configuration.
"""
from sqlalchemy.ext.asyncio import (
    create_async_engine,
    AsyncSession,
    async_sessionmaker
)
from sqlalchemy.orm import declarative_base
import os

# Database URL from environment
DATABASE_URL = os.getenv(
    "DATABASE_URL",
    "postgresql+asyncpg://postgres:password@localhost:5432/todo_db"
)

# Create async engine
engine = create_async_engine(
    DATABASE_URL,
    echo=True,  # Log SQL queries (disable in production)
    pool_size=20,
    max_overflow=0,
    pool_pre_ping=True,  # Verify connections before using
)

# Session factory
async_session_maker = async_sessionmaker(
    engine,
    class_=AsyncSession,
    expire_on_commit=False
)

# Declarative base for models
Base = declarative_base()


async def get_session() -> AsyncSession:
    """
    Get database session.
    
    Usage:
        async with get_session() as session:
            result = await session.execute(query)
    """
    async with async_session_maker() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
        finally:
            await session.close()


async def init_db():
    """Initialize database - create tables."""
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    print("✓ Database tables created")


async def close_db():
    """Close database connection pool."""
    await engine.dispose()
    print("✓ Database connections closed")
```

### SQLAlchemy Models

Create `app/database/models.py`:

```python
"""
SQLAlchemy database models.
"""
from datetime import datetime
from sqlalchemy import (
    Column,
    String,
    Text,
    Boolean,
    DateTime,
    Enum,
    ARRAY
)
from sqlalchemy.dialects.postgresql import UUID
import uuid

from app.database.config import Base
from app.models.task import TaskPriority, TaskStatus


class TaskModel(Base):
    """
    Task database model.
    
    Maps to 'tasks' table in PostgreSQL.
    """
    __tablename__ = "tasks"
    
    # Primary key - UUID7 generated in application
    id = Column(
        UUID(as_uuid=False),
        primary_key=True,
        default=lambda: str(uuid.uuid7())
    )
    
    # Task fields
    title = Column(String(200), nullable=False, index=True)
    description = Column(Text, nullable=True)
    
    # Enums stored as strings
    priority = Column(
        Enum(TaskPriority, native_enum=False),
        nullable=False,
        default=TaskPriority.MEDIUM,
        index=True
    )
    
    status = Column(
        Enum(TaskStatus, native_enum=False),
        nullable=False,
        default=TaskStatus.TODO,
        index=True
    )
    
    # Completion tracking
    completed = Column(Boolean, nullable=False, default=False, index=True)
    completed_at = Column(DateTime, nullable=True)
    
    # Due date
    due_date = Column(DateTime, nullable=True, index=True)
    
    # Tags as PostgreSQL array
    tags = Column(ARRAY(String), nullable=False, default=list)
    
    # Timestamps
    created_at = Column(
        DateTime,
        nullable=False,
        default=datetime.utcnow,
        index=True
    )
    updated_at = Column(
        DateTime,
        nullable=False,
        default=datetime.utcnow,
        onupdate=datetime.utcnow
    )
    
    def __repr__(self):
        return f"<Task(id={self.id}, title={self.title})>"
```

### Repository Implementation

Create `app/repositories/task_repository.py`:

```python
"""
Task repository for database operations.
"""
from typing import Optional, List
from sqlalchemy import select, delete, and_, or_, func
from sqlalchemy.ext.asyncio import AsyncSession

from app.database.models import TaskModel
from app.models.task import (
    Task,
    CreateTaskRequest,
    UpdateTaskRequest,
    TaskPriority,
    TaskStatus
)


class TaskRepository:
    """
    Task repository handling all database operations.
    
    Benefits:
    - Centralized data access
    - Easy to test with mock
    - Consistent query patterns
    """
    
    def __init__(self, session: AsyncSession):
        """
        Initialize repository with database session.
        
        Args:
            session: SQLAlchemy async session
        """
        self.session = session
    
    async def create(self, data: CreateTaskRequest) -> Task:
        """
        Create a new task in database.
        
        Args:
            data: Task creation data
        
        Returns:
            Created task
        """
        # Create database model
        db_task = TaskModel(
            title=data.title,
            description=data.description,
            priority=data.priority,
            due_date=data.due_date,
            tags=data.tags
        )
        
        # Add to session
        self.session.add(db_task)
        await self.session.flush()  # Get ID without committing
        
        # Convert to domain model
        return self._to_domain(db_task)
    
    async def get_by_id(self, task_id: str) -> Optional[Task]:
        """
        Get task by ID.
        
        Args:
            task_id: Task UUID
        
        Returns:
            Task if found, None otherwise
        """
        result = await self.session.execute(
            select(TaskModel).where(TaskModel.id == task_id)
        )
        db_task = result.scalar_one_or_none()
        
        return self._to_domain(db_task) if db_task else None
    
    async def get_by_title(self, title: str) -> Optional[Task]:
        """
        Get task by title (case-insensitive).
        
        Args:
            title: Task title
        
        Returns:
            Task if found, None otherwise
        """
        result = await self.session.execute(
            select(TaskModel).where(
                func.lower(TaskModel.title) == title.lower()
            )
        )
        db_task = result.scalar_one_or_none()
        
        return self._to_domain(db_task) if db_task else None
    
    async def list(
        self,
        status: Optional[TaskStatus] = None,
        priority: Optional[TaskPriority] = None,
        completed: Optional[bool] = None,
        tag: Optional[str] = None,
        overdue_only: bool = False,
        skip: int = 0,
        limit: int = 100
    ) -> List[Task]:
        """
        List tasks with filters.
        
        Args:
            status: Filter by status
            priority: Filter by priority
            completed: Filter by completion
            tag: Filter by tag
            overdue_only: Show only overdue tasks
            skip: Number to skip (pagination)
            limit: Maximum to return
        
        Returns:
            List of tasks
        """
        # Build query
        query = select(TaskModel)
        
        # Apply filters
        filters = []
        
        if status is not None:
            filters.append(TaskModel.status == status)
        
        if priority is not None:
            filters.append(TaskModel.priority == priority)
        
        if completed is not None:
            filters.append(TaskModel.completed == completed)
        
        if tag is not None:
            # PostgreSQL array contains
            filters.append(TaskModel.tags.contains([tag.lower()]))
        
        if overdue_only:
            from datetime import datetime
            filters.append(
                and_(
                    TaskModel.due_date < datetime.utcnow(),
                    TaskModel.completed == False
                )
            )
        
        if filters:
            query = query.where(and_(*filters))
        
        # Order by created_at descending
        query = query.order_by(TaskModel.created_at.desc())
        
        # Pagination
        query = query.offset(skip).limit(limit)
        
        # Execute
        result = await self.session.execute(query)
        db_tasks = result.scalars().all()
        
        return [self._to_domain(t) for t in db_tasks]
    
    async def update(
        self,
        task_id: str,
        data: UpdateTaskRequest
    ) -> Optional[Task]:
        """
        Update task.
        
        Args:
            task_id: Task UUID
            data: Update data
        
        Returns:
            Updated task if found, None otherwise
        """
        # Get existing task
        result = await self.session.execute(
            select(TaskModel).where(TaskModel.id == task_id)
        )
        db_task = result.scalar_one_or_none()
        
        if not db_task:
            return None
        
        # Update fields
        if data.title is not None:
            db_task.title = data.title
        
        if data.description is not None:
            db_task.description = data.description
        
        if data.priority is not None:
            db_task.priority = data.priority
        
        if data.status is not None:
            db_task.status = data.status
        
        if data.due_date is not None:
            db_task.due_date = data.due_date
        
        if data.tags is not None:
            db_task.tags = data.tags
        
        if data.completed is not None:
            db_task.completed = data.completed
            if data.completed:
                from datetime import datetime
                db_task.completed_at = datetime.utcnow()
                db_task.status = TaskStatus.COMPLETED
            else:
                db_task.completed_at = None
                db_task.status = TaskStatus.TODO
        
        # Update timestamp
        from datetime import datetime
        db_task.updated_at = datetime.utcnow()
        
        await self.session.flush()
        
        return self._to_domain(db_task)
    
    async def delete(self, task_id: str) -> bool:
        """
        Delete task.
        
        Args:
            task_id: Task UUID
        
        Returns:
            True if deleted, False if not found
        """
        result = await self.session.execute(
            delete(TaskModel).where(TaskModel.id == task_id)
        )
        
        return result.rowcount > 0
    
    async def search(self, query: str) -> List[Task]:
        """
        Search tasks by title or description.
        
        Args:
            query: Search query
        
        Returns:
            Matching tasks
        """
        # PostgreSQL full-text search or simple ILIKE
        search_filter = or_(
            TaskModel.title.ilike(f"%{query}%"),
            TaskModel.description.ilike(f"%{query}%")
        )
        
        result = await self.session.execute(
            select(TaskModel)
            .where(search_filter)
            .order_by(TaskModel.created_at.desc())
        )
        
        db_tasks = result.scalars().all()
        
        return [self._to_domain(t) for t in db_tasks]
    
    async def count(
        self,
        status: Optional[TaskStatus] = None,
        completed: Optional[bool] = None
    ) -> int:
        """
        Count tasks with filters.
        
        Args:
            status: Filter by status
            completed: Filter by completion
        
        Returns:
            Task count
        """
        query = select(func.count(TaskModel.id))
        
        filters = []
        if status is not None:
            filters.append(TaskModel.status == status)
        if completed is not None:
            filters.append(TaskModel.completed == completed)
        
        if filters:
            query = query.where(and_(*filters))
        
        result = await self.session.execute(query)
        return result.scalar()
    
    def _to_domain(self, db_task: TaskModel) -> Task:
        """
        Convert database model to domain model.
        
        Args:
            db_task: SQLAlchemy model
        
        Returns:
            Pydantic domain model
        """
        return Task(
            id=db_task.id,
            title=db_task.title,
            description=db_task.description,
            priority=db_task.priority,
            status=db_task.status,
            completed=db_task.completed,
            completed_at=db_task.completed_at,
            due_date=db_task.due_date,
            tags=db_task.tags,
            created_at=db_task.created_at,
            updated_at=db_task.updated_at
        )
```

### Updated Service with Repository

Update `app/services/task_service.py`:

```python
"""
Task service using database repository.
"""
from typing import Optional, List
from datetime import datetime

from app.repositories.task_repository import TaskRepository
from app.models.task import (
    Task,
    CreateTaskRequest,
    UpdateTaskRequest,
    TaskPriority,
    TaskStatus,
    TaskStatsResponse
)


class TaskService:
    """
    Task service with database persistence.
    
    Uses repository pattern for data access.
    """
    
    def __init__(self, repository: TaskRepository):
        """
        Initialize service with repository.
        
        Args:
            repository: Task repository
        """
        self.repository = repository
    
    async def create_task(
        self,
        data: CreateTaskRequest,
        user_id: Optional[str] = None
    ) -> Task:
        """Create a new task."""
        # Check for duplicate title
        existing = await self.repository.get_by_title(data.title)
        if existing:
            raise ValueError(f"Task with title '{data.title}' already exists")
        
        return await self.repository.create(data)
    
    async def get_task(self, task_id: str) -> Optional[Task]:
        """Get task by ID."""
        return await self.repository.get_by_id(task_id)
    
    async def list_tasks(
        self,
        status: Optional[TaskStatus] = None,
        priority: Optional[TaskPriority] = None,
        tag: Optional[str] = None,
        completed: Optional[bool] = None,
        overdue_only: bool = False,
        skip: int = 0,
        limit: int = 100
    ) -> List[Task]:
        """List tasks with filtering."""
        return await self.repository.list(
            status=status,
            priority=priority,
            completed=completed,
            tag=tag,
            overdue_only=overdue_only,
            skip=skip,
            limit=limit
        )
    
    async def update_task(
        self,
        task_id: str,
        data: UpdateTaskRequest
    ) -> Optional[Task]:
        """Update task."""
        # Check for duplicate title if changing
        if data.title:
            existing = await self.repository.get_by_title(data.title)
            if existing and existing.id != task_id:
                raise ValueError(
                    f"Task with title '{data.title}' already exists"
                )
        
        return await self.repository.update(task_id, data)
    
    async def delete_task(self, task_id: str) -> bool:
        """Delete task."""
        return await self.repository.delete(task_id)
    
    async def search_tasks(self, query: str) -> List[Task]:
        """Search tasks."""
        return await self.repository.search(query)
    
    async def get_statistics(self) -> TaskStatsResponse:
        """Get task statistics."""
        # Count by different filters
        total = await self.repository.count()
        completed = await self.repository.count(completed=True)
        pending = total - completed
        
        # Count overdue
        tasks = await self.repository.list(overdue_only=True, limit=10000)
        overdue = len(tasks)
        
        # Count by priority
        by_priority = {}
        for priority in TaskPriority:
            count = await self.repository.count()  # Simplified
            by_priority[priority.value] = 0  # Would need separate counts
        
        # Count by status
        by_status = {}
        for status in TaskStatus:
            count = await self.repository.count(status=status)
            by_status[status.value] = count
        
        return TaskStatsResponse(
            total=total,
            completed=completed,
            pending=pending,
            overdue=overdue,
            by_priority=by_priority,
            by_status=by_status
        )
```

### Database Plugin

Create `app/plugins/database.py`:

```python
"""
Database plugin for connection management.
"""
from api_starter.core.application import Plugin, RequestContext
from app.database.config import init_db, close_db, get_session


class DatabasePlugin(Plugin):
    """
    Database plugin managing connection lifecycle.
    
    Features:
    - Initialize database on startup
    - Provide session to request context
    - Close connections on shutdown
    
    Example:
        app.add_plugin(DatabasePlugin())
    """
    
    def __init__(self):
        super().__init__(name="database")
    
    async def initialize(self, app):
        """Initialize database tables."""
        await init_db()
        print("✓ Database initialized")
    
    async def on_request(self, ctx: RequestContext):
        """Attach database session to context."""
        # Session will be created on first access
        ctx.db_session = None
        ctx.get_db_session = get_session
    
    async def shutdown(self):
        """Close database connections."""
        await close_db()
```

### Updated Endpoints with Database

Update `app/endpoints/tasks.py`:

```python
from app.repositories.task_repository import TaskRepository
from app.services.task_service import TaskService


def register_task_endpoints(app: APIStarter):
    """Register all task endpoints."""
    
    @app.endpoint("/tasks", methods=["POST"])
    async def create_task(ctx: RequestContext, data: CreateTaskRequest):
        """Create a new task."""
        # Get database session
        async with ctx.get_db_session() as session:
            # Create repository and service
            repository = TaskRepository(session)
            service = TaskService(repository)
            
            try:
                task = await service.create_task(data)
                
                return JSONResponse(
                    {
                        "message": "Task created successfully",
                        "task": task.model_dump()
                    },
                    status_code=201
                )
            
            except ValueError as e:
                return JSONResponse(
                    {"error": str(e)},
                    status_code=409
                )
    
    @app.endpoint("/tasks", methods=["GET"])
    async def list_tasks(ctx: RequestContext):
        """List all tasks."""
        async with ctx.get_db_session() as session:
            repository = TaskRepository(session)
            service = TaskService(repository)
            
            # Parse filters
            status_str = ctx.query.get("status")
            priority_str = ctx.query.get("priority")
            completed_str = ctx.query.get("completed")
            tag = ctx.query.get("tag")
            page = int(ctx.query.get("page", 1))
            page_size = int(ctx.query.get("page_size", 20))
            
            # Convert to enums
            status = TaskStatus(status_str) if status_str else None
            priority = TaskPriority(priority_str) if priority_str else None
            completed = (completed_str.lower() == "true") if completed_str else None
            
            # Get tasks
            skip = (page - 1) * page_size
            tasks = await service.list_tasks(
                status=status,
                priority=priority,
                completed=completed,
                tag=tag,
                skip=skip,
                limit=page_size + 1
            )
            
            # Check pagination
            has_next = len(tasks) > page_size
            if has_next:
                tasks = tasks[:page_size]
            
            return {
                "tasks": [t.model_dump() for t in tasks],
                "page": page,
                "page_size": page_size,
                "has_next": has_next
            }
```

### Database Migrations with Alembic

```bash
# Initialize Alembic
alembic init migrations

# Create first migration
alembic revision --autogenerate -m "Create tasks table"

# Apply migrations
alembic upgrade head

# Rollback migration
alembic downgrade -1
```

Create `alembic.ini`:

```ini
[alembic]
script_location = migrations
prepend_sys_path = .
sqlalchemy.url = postgresql+asyncpg://postgres:password@localhost:5432/todo_db

[loggers]
keys = root,sqlalchemy,alembic

[handlers]
keys = console

[formatters]
keys = generic

[logger_root]
level = WARN
handlers = console

[logger_sqlalchemy]
level = WARN
handlers =
qualname = sqlalchemy.engine

[logger_alembic]
level = INFO
handlers =
qualname = alembic

[handler_console]
class = StreamHandler
args = (sys.stderr,)
level = NOTSET
formatter = generic

[formatter_generic]
format = %(levelname)-5.5s [%(name)s] %(message)s
```

[↑ Back to TOC](#-table-of-contents)

---

## 🍃 MongoDB Integration

### Installation

```bash
# Install MongoDB dependencies
pip install motor beanie

# Start MongoDB (Docker)
docker run -d \
  --name mongodb \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  -p 27017:27017 \
  mongo:7
```

### MongoDB Models with Beanie

Create `app/database/mongo_models.py`:

```python
"""
MongoDB models with Beanie ODM.
"""
from datetime import datetime
from typing import Optional, List
from beanie import Document, Indexed
from pydantic import Field

from app.models.task import TaskPriority, TaskStatus


class TaskDocument(Document):
    """
    Task document for MongoDB.
    
    Beanie provides async ODM on top of Motor.
    """
    # MongoDB _id is automatically created
    
    # Indexed fields for faster queries
    title: Indexed(str)
    description: Optional[str] = None
    
    priority: TaskPriority = TaskPriority.MEDIUM
    status: Indexed(TaskStatus) = TaskStatus.TODO
    
    completed: Indexed(bool) = False
    completed_at: Optional[datetime] = None
    
    due_date: Optional[Indexed(datetime)] = None
    tags: List[str] = Field(default_factory=list)
    
    created_at: Indexed(datetime) = Field(default_factory=datetime.utcnow)
    updated_at: datetime = Field(default_factory=datetime.utcnow)
    
    class Settings:
        name = "tasks"  # Collection name
        indexes = [
            "title",
            "status",
            "completed",
            "created_at",
            [("tags", 1)],  # Index on array field
        ]


async def init_mongo():
    """Initialize MongoDB connection."""
    from motor.motor_asyncio import AsyncIOMotorClient
    from beanie import init_beanie
    
    # MongoDB connection string
    MONGO_URL = "mongodb://admin:password@localhost:27017"
    
    # Create client
    client = AsyncIOMotorClient(MONGO_URL)
    
    # Initialize Beanie with models
    await init_beanie(
        database=client.todo_db,
        document_models=[TaskDocument]
    )
    
    print("✓ MongoDB initialized")
```

### MongoDB Repository

Create `app/repositories/mongo_task_repository.py`:

```python
"""
MongoDB task repository.
"""
from typing import Optional, List
from datetime import datetime

from app.database.mongo_models import TaskDocument
from app.models.task import (
    Task,
    CreateTaskRequest,
    UpdateTaskRequest,
    TaskPriority,
    TaskStatus
)


class MongoTaskRepository:
    """
    MongoDB task repository using Beanie.
    
    Similar interface to SQL repository for easy swapping.
    """
    
    async def create(self, data: CreateTaskRequest) -> Task:
        """Create task in MongoDB."""
        doc = TaskDocument(
            title=data.title,
            description=data.description,
            priority=data.priority,
            due_date=data.due_date,
            tags=data.tags
        )
        
        await doc.insert()
        
        return self._to_domain(doc)
    
    async def get_by_id(self, task_id: str) -> Optional[Task]:
        """Get task by ID."""
        doc = await TaskDocument.get(task_id)
        return self._to_domain(doc) if doc else None
    
    async def get_by_title(self, title: str) -> Optional[Task]:
        """Get task by title."""
        doc = await TaskDocument.find_one(
            TaskDocument.title == title
        )
        return self._to_domain(doc) if doc else None
    
    async def list(
        self,
        status: Optional[TaskStatus] = None,
        priority: Optional[TaskPriority] = None,
        completed: Optional[bool] = None,
        tag: Optional[str] = None,
        skip: int = 0,
        limit: int = 100
    ) -> List[Task]:
        """List tasks with filters."""
        # Build query
        query = TaskDocument.find()
        
        if status is not None:
            query = query.find(TaskDocument.status == status)
        
        if priority is not None:
            query = query.find(TaskDocument.priority == priority)
        
        if completed is not None:
            query = query.find(TaskDocument.completed == completed)
        
        if tag is not None:
            query = query.find(TaskDocument.tags == tag)
        
        # Sort and paginate
        docs = await query.sort(-TaskDocument.created_at).skip(skip).limit(limit).to_list()
        
        return [self._to_domain(doc) for doc in docs]
    
    async def update(
        self,
        task_id: str,
        data: UpdateTaskRequest
    ) -> Optional[Task]:
        """Update task."""
        doc = await TaskDocument.get(task_id)
        if not doc:
            return None
        
        # Update fields
        if data.title is not None:
            doc.title = data.title
        if data.description is not None:
            doc.description = data.description
        if data.priority is not None:
            doc.priority = data.priority
        if data.status is not None:
            doc.status = data.status
        if data.completed is not None:
            doc.completed = data.completed
            if data.completed:
                doc.completed_at = datetime.utcnow()
                doc.status = TaskStatus.COMPLETED
        if data.due_date is not None:
            doc.due_date = data.due_date
        if data.tags is not None:
            doc.tags = data.tags
        
        doc.updated_at = datetime.utcnow()
        await doc.save()
        
        return self._to_domain(doc)
    
    async def delete(self, task_id: str) -> bool:
        """Delete task."""
        doc = await TaskDocument.get(task_id)
        if doc:
            await doc.delete()
            return True
        return False
    
    async def search(self, query: str) -> List[Task]:
        """Search tasks using text search."""
        # MongoDB text search requires index
        docs = await TaskDocument.find(
            {
                "$or": [
                    {"title": {"$regex": query, "$options": "i"}},
                    {"description": {"$regex": query, "$options": "i"}}
                ]
            }
        ).to_list()
        
        return [self._to_domain(doc) for doc in docs]
    
    def _to_domain(self, doc: TaskDocument) -> Task:
        """Convert MongoDB document to domain model."""
        return Task(
            id=str(doc.id),
            title=doc.title,
            description=doc.description,
            priority=doc.priority,
            status=doc.status,
            completed=doc.completed,
            completed_at=doc.completed_at,
            due_date=doc.due_date,
            tags=doc.tags,
            created_at=doc.created_at,
            updated_at=doc.updated_at
        )
```

[↑ Back to TOC](#-table-of-contents)

---

## 🔌 WebSocket Support

### WebSocket Architecture

```mermaid
graph TB
    subgraph CLIENTS ["📱    WebSocket    Clients"]
        C1[🌐 Web Browser<br/>JavaScript client]
        C2[📱 Mobile App<br/>Native WebSocket]
        C3[🖥️ Desktop App<br/>WebSocket client]
    end

    subgraph CONNECTION ["🔌    Connection    Management"]
        CM1[🔗 Connection Handler<br/>Accept/Reject]
        CM2[📋 Connection Registry<br/>Active connections]
        CM3[💓 Heartbeat Monitor<br/>Keep-alive pings]
    end

    subgraph MESSAGES ["📨    Message    Processing"]
        MP1[📥 Receive Message<br/>Parse JSON]
        MP2[✅ Validate Message<br/>Check format]
        MP3[🎯 Route Message<br/>Match handler]
        MP4[💼 Execute Handler<br/>Business logic]
    end

    subgraph BROADCAST ["📡    Broadcasting    System"]
        BC1[📢 Broadcast to All<br/>All connections]
        BC2[🎯 Send to User<br/>Specific user]
        BC3[🏷️ Send to Room<br/>Grouped clients]
    end

    subgraph EVENTS ["⚡    Real-Time    Events"]
        EV1[✨ Task Created<br/>Notify subscribers]
        EV2[✏️ Task Updated<br/>Push changes]
        EV3[🗑️ Task Deleted<br/>Remove from UI]
        EV4[📊 Stats Changed<br/>Update dashboard]
    end

    %% Clients to Connection - Blue
    C1 --> CM1
    C2 --> CM1
    C3 --> CM1
    linkStyle 0 stroke:#1976d2,stroke-width:3px
    linkStyle 1 stroke:#1976d2,stroke-width:3px
    linkStyle 2 stroke:#1976d2,stroke-width:3px

    %% Connection management - Purple
    CM1 --> CM2
    CM2 --> CM3
    linkStyle 3 stroke:#7b1fa2,stroke-width:3px
    linkStyle 4 stroke:#7b1fa2,stroke-width:3px

    %% Connection to Messages - Purple
    CM2 --> MP1
    linkStyle 5 stroke:#7b1fa2,stroke-width:3px

    %% Message processing - Teal
    MP1 --> MP2
    MP2 --> MP3
    MP3 --> MP4
    linkStyle 6 stroke:#00695c,stroke-width:3px
    linkStyle 7 stroke:#00695c,stroke-width:3px
    linkStyle 8 stroke:#00695c,stroke-width:3px

    %% Handler to Events - Orange
    MP4 --> EV1
    MP4 --> EV2
    MP4 --> EV3
    linkStyle 9 stroke:#f57c00,stroke-width:3px
    linkStyle 10 stroke:#f57c00,stroke-width:3px
    linkStyle 11 stroke:#f57c00,stroke-width:3px

    %% Events to Broadcast - Green
    EV1 --> BC1
    EV2 --> BC2
    EV3 --> BC3
    EV4 --> BC1
    linkStyle 12 stroke:#388e3c,stroke-width:3px
    linkStyle 13 stroke:#388e3c,stroke-width:3px
    linkStyle 14 stroke:#388e3c,stroke-width:3px
    linkStyle 15 stroke:#388e3c,stroke-width:3px

    %% Broadcast back to Clients - Indigo (final)
    BC1 --> C1
    BC1 --> C2
    BC1 --> C3
    BC2 --> C1
    BC3 --> C2
    linkStyle 16 stroke:#3f51b5,stroke-width:4px
    linkStyle 17 stroke:#3f51b5,stroke-width:4px
    linkStyle 18 stroke:#3f51b5,stroke-width:4px
    linkStyle 19 stroke:#3f51b5,stroke-width:4px
    linkStyle 20 stroke:#3f51b5,stroke-width:4px

    %% Subgraph styling
    style CLIENTS fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style CONNECTION fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style MESSAGES fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style BROADCAST fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    style EVENTS fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000

    %% Node styling
    style C1 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style C2 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style C3 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    
    style CM1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style CM2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style CM3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    
    style MP1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style MP2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style MP3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style MP4 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    
    style BC1 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style BC2 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style BC3 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    
    style EV1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style EV2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style EV3 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style EV4 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
```

### WebSocket Plugin Implementation

Create `app/plugins/websocket.py`:

```python
"""
WebSocket plugin for real-time updates.
"""
import json
from typing import Dict, Set
from starlette.websockets import WebSocket, WebSocketDisconnect
from starlette.routing import WebSocketRoute

from api_starter.core.application import Plugin


class ConnectionManager:
    """
    Manage WebSocket connections.
    
    Features:
    - Track active connections
    - Broadcast messages
    - Send to specific users/rooms
    """
    
    def __init__(self):
        # All active connections
        self.active_connections: Set[WebSocket] = set()
        
        # Connections by user ID
        self.user_connections: Dict[str, Set[WebSocket]] = {}
        
        # Connections by room
        self.rooms: Dict[str, Set[WebSocket]] = {}
    
    async def connect(
        self,
        websocket: WebSocket,
        user_id: str = None
    ):
        """Accept WebSocket connection."""
        await websocket.accept()
        self.active_connections.add(websocket)
        
        if user_id:
            if user_id not in self.user_connections:
                self.user_connections[user_id] = set()
            self.user_connections[user_id].add(websocket)
        
        print(f"✓ WebSocket connected. Total: {len(self.active_connections)}")
    
    def disconnect(self, websocket: WebSocket):
        """Remove WebSocket connection."""
        self.active_connections.discard(websocket)
        
        # Remove from user connections
        for user_id, connections in self.user_connections.items():
            connections.discard(websocket)
        
        # Remove from rooms
        for room, connections in self.rooms.items():
            connections.discard(websocket)
        
        print(f"✓ WebSocket disconnected. Total: {len(self.active_connections)}")
    
    async def send_personal(
        self,
        message: dict,
        websocket: WebSocket
    ):
        """Send message to specific connection."""
        await websocket.send_json(message)
    
    async def send_to_user(
        self,
        message: dict,
        user_id: str
    ):
        """Send message to all connections of a user."""
        if user_id in self.user_connections:
            for websocket in self.user_connections[user_id]:
                await websocket.send_json(message)
    
    async def broadcast(self, message: dict):
        """Broadcast message to all connections."""
        disconnected = []
        
        for websocket in self.active_connections:
            try:
                await websocket.send_json(message)
            except Exception:
                disconnected.append(websocket)
        
        # Clean up disconnected
        for websocket in disconnected:
            self.disconnect(websocket)
    
    async def join_room(self, websocket: WebSocket, room: str):
        """Add connection to room."""
        if room not in self.rooms:
            self.rooms[room] = set()
        self.rooms[room].add(websocket)
    
    async def leave_room(self, websocket: WebSocket, room: str):
        """Remove connection from room."""
        if room in self.rooms:
            self.rooms[room].discard(websocket)
    
    async def send_to_room(self, message: dict, room: str):
        """Send message to all connections in room."""
        if room in self.rooms:
            for websocket in self.rooms[room]:
                await websocket.send_json(message)


class WebSocketPlugin(Plugin):
    """
    WebSocket plugin for real-time communication.
    
    Features:
    - Connection management
    - Broadcasting
    - Room-based messaging
    - Heartbeat monitoring
    
    Example:
        app.add_plugin(WebSocketPlugin(path="/ws"))
    """
    
    def __init__(self, path: str = "/ws"):
        super().__init__(name="websocket")
        self.path = path
        self.manager = ConnectionManager()
    
    async def initialize(self, app):
        """Register WebSocket route."""
        
        async def websocket_endpoint(websocket: WebSocket):
            """WebSocket connection handler."""
            # Get user_id from query params (if authenticated)
            user_id = websocket.query_params.get("user_id")
            
            await self.manager.connect(websocket, user_id)
            
            try:
                while True:
                    # Receive message
                    data = await websocket.receive_json()
                    
                    # Handle different message types
                    message_type = data.get("type")
                    
                    if message_type == "ping":
                        # Heartbeat
                        await self.manager.send_personal(
                            {"type": "pong"},
                            websocket
                        )
                    
                    elif message_type == "join_room":
                        # Join a room
                        room = data.get("room")
                        await self.manager.join_room(websocket, room)
                        await self.manager.send_personal(
                            {
                                "type": "room_joined",
                                "room": room
                            },
                            websocket
                        )
                    
                    elif message_type == "leave_room":
                        # Leave a room
                        room = data.get("room")
                        await self.manager.leave_room(websocket, room)
                    
                    elif message_type == "message":
                        # Echo message back (or handle custom logic)
                        await self.manager.send_personal(
                            {
                                "type": "message_received",
                                "content": data.get("content")
                            },
                            websocket
                        )
            
            except WebSocketDisconnect:
                self.manager.disconnect(websocket)
        
        # Add WebSocket route to Starlette
        from starlette.routing import Mount
        ws_route = WebSocketRoute(self.path, websocket_endpoint)
        app._routes.append(ws_route)
        
        print(f"✓ WebSocket endpoint: {self.path}")
    
    async def notify_task_created(self, task: dict):
        """Broadcast task creation event."""
        await self.manager.broadcast({
            "type": "task_created",
            "task": task
        })
    
    async def notify_task_updated(self, task: dict):
        """Broadcast task update event."""
        await self.manager.broadcast({
            "type": "task_updated",
            "task": task
        })
    
    async def notify_task_deleted(self, task_id: str):
        """Broadcast task deletion event."""
        await self.manager.broadcast({
            "type": "task_deleted",
            "task_id": task_id
        })
```

### Using WebSocket in Endpoints

Update endpoints to broadcast changes:

```python
from app.plugins.websocket import WebSocketPlugin

# In endpoint:
@app.endpoint("/tasks", methods=["POST"])
async def create_task(ctx: RequestContext, data: CreateTaskRequest):
    """Create task and broadcast update."""
    async with ctx.get_db_session() as session:
        repository = TaskRepository(session)
        service = TaskService(repository)
        
        task = await service.create_task(data)
        
        # Broadcast to WebSocket clients
        ws_plugin = app._plugins.get("websocket")
        if ws_plugin:
            await ws_plugin.notify_task_created(task.model_dump())
        
        return JSONResponse(
            {"message": "Task created", "task": task.model_dump()},
            status_code=201
        )
```

### WebSocket Client Example

```html
<!DOCTYPE html>
<html>
<head>
    <title>WebSocket Demo</title>
</head>
<body>
    <h1>Real-Time Task Updates</h1>
    <div id="messages"></div>
    
    <script>
        // Connect to WebSocket
        const ws = new WebSocket('ws://localhost:8000/ws');
        
        ws.onopen = () => {
            console.log('Connected to WebSocket');
            
            // Send heartbeat every 30 seconds
            setInterval(() => {
                ws.send(JSON.stringify({type: 'ping'}));
            }, 30000);
        };
        
        ws.onmessage = (event) => {
            const data = JSON.parse(event.data);
            console.log('Received:', data);
            
            const messages = document.getElementById('messages');
            
            if (data.type === 'task_created') {
                messages.innerHTML += `<p>New task: ${data.task.title}</p>`;
            } else if (data.type === 'task_updated') {
                messages.innerHTML += `<p>Updated task: ${data.task.title}</p>`;
            } else if (data.type === 'task_deleted') {
                messages.innerHTML += `<p>Deleted task: ${data.task_id}</p>`;
            }
        };
        
        ws.onerror = (error) => {
            console.error('WebSocket error:', error);
        };
        
        ws.onclose = () => {
            console.log('Disconnected from WebSocket');
        };
    </script>
</body>
</html>
```

[↑ Back to TOC](#-table-of-contents)

---

## ⚙️ Background Tasks & Job Queues

### Celery Integration

```bash
# Install Celery and Redis
pip install celery redis

# Start Redis (for broker)
docker run -d -p 6379:6379 redis:7-alpine

# Start Celery worker
celery -A app.tasks worker --loglevel=info
```

### Celery Configuration

Create `app/celery_app.py`:

```python
"""
Celery application for background tasks.
"""
from celery import Celery
import os

# Celery configuration
CELERY_BROKER_URL = os.getenv(
    "CELERY_BROKER_URL",
    "redis://localhost:6379/0"
)
CELERY_RESULT_BACKEND = os.getenv(
    "CELERY_RESULT_BACKEND",
    "redis://localhost:6379/0"
)

# Create Celery app
celery_app = Celery(
    "todo_api",
    broker=CELERY_BROKER_URL,
    backend=CELERY_RESULT_BACKEND
)

# Configuration
celery_app.conf.update(
    task_serializer="json",
    accept_content=["json"],
    result_serializer="json",
    timezone="UTC",
    enable_utc=True,
    task_track_started=True,
    task_time_limit=30 * 60,  # 30 minutes
    task_soft_time_limit=25 * 60,  # 25 minutes
)
```

### Background Tasks

Create `app/tasks.py`:

```python
"""
Background tasks with Celery.
"""
from datetime import datetime, timedelta
from app.celery_app import celery_app
import asyncio


@celery_app.task(name="send_reminder_email")
def send_reminder_email(task_id: str, task_title: str, user_email: str):
    """
    Send reminder email for upcoming task.
    
    This runs in background worker.
    """
    print(f"Sending reminder for task: {task_title} to {user_email}")
    
    # Email sending logic here
    # e.g., using SendGrid, AWS SES, etc.
    
    return {"status": "sent", "task_id": task_id}


@celery_app.task(name="check_overdue_tasks")
def check_overdue_tasks():
    """
    Periodic task to check for overdue tasks.
    
    Run this as a scheduled task (cron-like).
    """
    print("Checking for overdue tasks...")
    
    # Query database for overdue tasks
    # Send notifications
    
    return {"checked_at": datetime.utcnow().isoformat()}


@celery_app.task(name="generate_report")
def generate_report(report_type: str, user_id: str):
    """
    Generate report in background.
    
    Long-running task that generates PDF/Excel reports.
    """
    print(f"Generating {report_type} report for user {user_id}")
    
    # Report generation logic
    # 1. Query data
    # 2. Generate PDF/Excel
    # 3. Upload to S3
    # 4. Send notification
    
    return {
        "report_url": "https://s3.../report.pdf",
        "generated_at": datetime.utcnow().isoformat()
    }


@celery_app.task(name="process_bulk_import")
def process_bulk_import(file_path: str, user_id: str):
    """
    Process bulk task import from CSV/Excel.
    
    Handles large file imports in background.
    """
    print(f"Processing bulk import from {file_path}")
    
    # Import logic
    # 1. Read file
    # 2. Validate data
    # 3. Create tasks in batches
    # 4. Send completion notification
    
    return {"imported": 100, "failed": 5}
```

### Scheduled Tasks

Create `app/celery_beat.py`:

```python
"""
Celery Beat scheduler for periodic tasks.
"""
from celery.schedules import crontab
from app.celery_app import celery_app

# Scheduled tasks configuration
celery_app.conf.beat_schedule = {
    # Check overdue tasks every hour
    'check-overdue-tasks': {
        'task': 'check_overdue_tasks',
        'schedule': crontab(minute=0),  # Every hour
    },
    
    # Send daily summary at 9 AM
    'send-daily-summary': {
        'task': 'send_daily_summary',
        'schedule': crontab(hour=9, minute=0),  # 9:00 AM daily
    },
    
    # Cleanup old data at midnight
    'cleanup-old-data': {
        'task': 'cleanup_old_data',
        'schedule': crontab(hour=0, minute=0),  # Midnight
    },
}
```

### Using Background Tasks in API

```python
from app.tasks import send_reminder_email, generate_report

@app.endpoint("/tasks/{task_id}/remind", methods=["POST"])
async def send_reminder(ctx: RequestContext, task_id: str):
    """Schedule reminder email for task."""
    async with ctx.get_db_session() as session:
        repository = TaskRepository(session)
        task = await repository.get_by_id(task_id)
        
        if not task:
            return JSONResponse({"error": "Task not found"}, status_code=404)
        
        # Queue background task
        result = send_reminder_email.delay(
            task_id=task.id,
            task_title=task.title,
            user_email="user@example.com"
        )
        
        return {
            "message": "Reminder scheduled",
            "task_id": result.id
        }


@app.endpoint("/reports/generate", methods=["POST"])
async def generate_report_endpoint(ctx: RequestContext):
    """Generate report in background."""
    from starlette.requests import Request
    request = Request(ctx._request)
    body = await request.json()
    
    report_type = body.get("type", "summary")
    user_id = ctx.user_id
    
    # Queue report generation
    result = generate_report.delay(report_type, user_id)
    
    return {
        "message": "Report generation started",
        "task_id": result.id,
        "status_url": f"/tasks/status/{result.id}"
    }


@app.endpoint("/tasks/status/{task_id}", methods=["GET"])
async def check_task_status(ctx: RequestContext, task_id: str):
    """Check status of background task."""
    from celery.result import AsyncResult
    
    result = AsyncResult(task_id, app=celery_app)
    
    if result.ready():
        return {
            "status": "completed",
            "result": result.get()
        }
    elif result.failed():
        return {
            "status": "failed",
            "error": str(result.info)
        }
    else:
        return {
            "status": "pending",
            "progress": result.info if isinstance(result.info, dict) else {}
        }
```

### Running Celery

```bash
# Terminal 1: Start Celery worker
celery -A app.celery_app worker --loglevel=info

# Terminal 2: Start Celery Beat (scheduler)
celery -A app.celery_beat beat --loglevel=info

# Terminal 3: Start API
python app/main.py
```

[↑ Back to TOC](#-table-of-contents)

---

## 🧪 Testing Strategies

### Test Architecture

```mermaid
graph TB
    subgraph UNIT ["🔬    Unit    Tests"]
        U1[📦 Model Tests<br/>Pydantic validation]
        U2[💼 Service Tests<br/>Business logic]
        U3[📊 Repository Tests<br/>Data access mocks]
    end

    subgraph INTEGRATION ["🔗    Integration    Tests"]
        I1[🗄️ Database Tests<br/>Real DB operations]
        I2[📡 API Tests<br/>HTTP endpoints]
        I3[🔮 GraphQL Tests<br/>Query resolution]
    end

    subgraph E2E ["🎭    End-to-End    Tests"]
        E1[🌐 Full Flow Tests<br/>User scenarios]
        E2[📱 WebSocket Tests<br/>Real-time features]
        E3[⚙️ Background Task Tests<br/>Async operations]
    end

    subgraph PERFORMANCE ["⚡    Performance    Tests"]
        P1[📊 Load Tests<br/>High traffic]
        P2[🔥 Stress Tests<br/>Breaking point]
        P3[⏱️ Latency Tests<br/>Response times]
    end

    %% Subgraph styling
    style UNIT fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style INTEGRATION fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style E2E fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style PERFORMANCE fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000

    %% Node styling
    style U1 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style U2 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style U3 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    
    style I1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style I2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style I3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    
    style E1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style E2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style E3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    
    style P1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style P2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style P3 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
```

### Unit Tests

Create `tests/test_services.py`:

```python
"""
Unit tests for services.
"""
import pytest
from unittest.mock import AsyncMock, MagicMock
from datetime import datetime

from app.services.task_service import TaskService
from app.models.task import (
    CreateTaskRequest,
    UpdateTaskRequest,
    Task,
    TaskPriority,
    TaskStatus
)


@pytest.fixture
def mock_repository():
    """Create mock repository."""
    return AsyncMock()


@pytest.fixture
def task_service(mock_repository):
    """Create task service with mock repository."""
    return TaskService(mock_repository)


@pytest.mark.asyncio
class TestTaskService:
    """Test task service business logic."""
    
    async def test_create_task_success(self, task_service, mock_repository):
        """Test successful task creation."""
        # Arrange
        data = CreateTaskRequest(
            title="Test task",
            priority=TaskPriority.HIGH
        )
        
        expected_task = Task(
            id="test-id",
            title="Test task",
            priority=TaskPriority.HIGH,
            status=TaskStatus.TODO,
            completed=False,
            tags=[],
            created_at=datetime.utcnow(),
            updated_at=datetime.utcnow()
        )
        
        mock_repository.get_by_title.return_value = None
        mock_repository.create.return_value = expected_task
        
        # Act
        result = await task_service.create_task(data)
        
        # Assert
        assert result.title == "Test task"
        assert result.priority == TaskPriority.HIGH
        mock_repository.create.assert_called_once()
    
    async def test_create_task_duplicate_title(
        self,
        task_service,
        mock_repository
    ):
        """Test that duplicate titles are rejected."""
        # Arrange
        existing_task = Task(
            id="existing-id",
            title="Existing task",
            priority=TaskPriority.MEDIUM,
            status=TaskStatus.TODO,
            completed=False,
            tags=[],
            created_at=datetime.utcnow(),
            updated_at=datetime.utcnow()
        )
        
        mock_repository.get_by_title.return_value = existing_task
        
        data = CreateTaskRequest(title="Existing task")
        
        # Act & Assert
        with pytest.raises(ValueError, match="already exists"):
            await task_service.create_task(data)
    
    async def test_get_task_not_found(self, task_service, mock_repository):
        """Test getting non-existent task."""
        # Arrange
        mock_repository.get_by_id.return_value = None
        
        # Act
        result = await task_service.get_task("non-existent")
        
        # Assert
        assert result is None
```

### Integration Tests

Create `tests/test_integration.py`:

```python
"""
Integration tests with real database.
"""
import pytest
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

from app.database.config import Base
from app.database.models import TaskModel
from app.repositories.task_repository import TaskRepository
from app.services.task_service import TaskService
from app.models.task import CreateTaskRequest, TaskPriority


# Test database URL
TEST_DATABASE_URL = "postgresql+asyncpg://postgres:password@localhost:5432/test_db"


@pytest.fixture
async def test_db():
    """Create test database."""
    engine = create_async_engine(TEST_DATABASE_URL, echo=True)
    
    # Create tables
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    
    # Create session
    async_session = sessionmaker(
        engine, class_=AsyncSession, expire_on_commit=False
    )
    
    async with async_session() as session:
        yield session
    
    # Drop tables
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.drop_all)
    
    await engine.dispose()


@pytest.mark.asyncio
class TestTaskIntegration:
    """Integration tests with real database."""
    
    async def test_create_and_retrieve_task(self, test_db):
        """Test creating and retrieving task from database."""
        # Create service with real repository
        repository = TaskRepository(test_db)
        service = TaskService(repository)
        
        # Create task
        data = CreateTaskRequest(
            title="Integration test task",
            priority=TaskPriority.HIGH
        )
        
        created = await service.create_task(data)
        await test_db.commit()
        
        # Retrieve task
        retrieved = await service.get_task(created.id)
        
        # Assert
        assert retrieved is not None
        assert retrieved.title == "Integration test task"
        assert retrieved.priority == TaskPriority.HIGH
    
    async def test_list_tasks_with_filters(self, test_db):
        """Test listing tasks with filters."""
        repository = TaskRepository(test_db)
        service = TaskService(repository)
        
        # Create multiple tasks
        for i in range(5):
            data = CreateTaskRequest(
                title=f"Task {i}",
                priority=TaskPriority.HIGH if i < 3 else TaskPriority.LOW
            )
            await service.create_task(data)
        
        await test_db.commit()
        
        # List high priority tasks
        high_priority = await service.list_tasks(priority=TaskPriority.HIGH)
        
        assert len(high_priority) == 3
        assert all(t.priority == TaskPriority.HIGH for t in high_priority)
```

### API Endpoint Tests

Create `tests/test_api.py`:

```python
"""
API endpoint tests.
"""
import pytest
from httpx import AsyncClient
from app.main import app


@pytest.mark.asyncio
class TestTaskAPI:
    """Test task API endpoints."""
    
    async def test_create_task_endpoint(self):
        """Test POST /tasks endpoint."""
        async with AsyncClient(app=app, base_url="http://test") as client:
            response = await client.post(
                "/tasks",
                json={
                    "title": "API test task",
                    "description": "Testing API",
                    "priority": "high"
                }
            )
        
        assert response.status_code == 201
        data = response.json()
        assert "task" in data
        assert data["task"]["title"] == "API test task"
    
    async def test_list_tasks_endpoint(self):
        """Test GET /tasks endpoint."""
        async with AsyncClient(app=app, base_url="http://test") as client:
            # Create a task first
            await client.post(
                "/tasks",
                json={"title": "Task for listing"}
            )
            
            # List tasks
            response = await client.get("/tasks")
        
        assert response.status_code == 200
        data = response.json()
        assert "tasks" in data
        assert isinstance(data["tasks"], list)
    
    async def test_get_nonexistent_task(self):
        """Test getting non-existent task returns 404."""
        async with AsyncClient(app=app, base_url="http://test") as client:
            response = await client.get("/tasks/nonexistent-id")
        
        assert response.status_code == 404
```

### Running Tests

```bash
# Run all tests
pytest tests/ -v

# Run with coverage
pytest tests/ --cov=app --cov-report=html --cov-report=term

# Run specific test file
pytest tests/test_services.py -v

# Run specific test
pytest tests/test_services.py::TestTaskService::test_create_task_success -v

# Run tests in parallel
pytest tests/ -n auto
```

[↑ Back to TOC](#-table-of-contents)

---

## ⚙️ Production Configuration

### Environment-Based Configuration

Create `app/config/production.py`:

```python
"""
Production configuration.
"""
import os
from pydantic_settings import BaseSettings


class ProductionSettings(BaseSettings):
    """Production settings with environment variables."""
    
    # Application
    app_name: str = "Todo API"
    app_version: str = "1.0.0"
    debug: bool = False
    
    # Server
    host: str = "0.0.0.0"
    port: int = 8000
    workers: int = 4
    
    # Database
    database_url: str
    database_pool_size: int = 20
    database_max_overflow: int = 10
    
    # Redis
    redis_url: str
    redis_max_connections: int = 50
    
    # Celery
    celery_broker_url: str
    celery_result_backend: str
    
    # Security
    secret_key: str
    allowed_origins: list[str] = ["https://myapp.com"]
    
    # Monitoring
    sentry_dsn: str = ""
    log_level: str = "INFO"
    
    # Performance
    cache_ttl: int = 300
    rate_limit_per_minute: int = 60
    rate_limit_per_hour: int = 1000
    
    class Config:
        env_file = ".env.production"
        case_sensitive = False


settings = ProductionSettings()
```

### Logging Configuration

Create `app/config/logging.py`:

```python
"""
Logging configuration.
"""
import logging
import sys
from pythonjsonlogger import jsonlogger


def setup_logging(level: str = "INFO"):
    """
    Setup structured JSON logging for production.
    
    Args:
        level: Log level (DEBUG, INFO, WARNING, ERROR, CRITICAL)
    """
    # Create JSON formatter
    formatter = jsonlogger.JsonFormatter(
        fmt="%(asctime)s %(name)s %(levelname)s %(message)s",
        datefmt="%Y-%m-%dT%H:%M:%S"
    )
    
    # Setup handler
    handler = logging.StreamHandler(sys.stdout)
    handler.setFormatter(formatter)
    
    # Configure root logger
    logger = logging.getLogger()
    logger.setLevel(level)
    logger.addHandler(handler)
    
    # Suppress noisy loggers
    logging.getLogger("uvicorn.access").setLevel(logging.WARNING)
    logging.getLogger("sqlalchemy.engine").setLevel(logging.WARNING)
```

### Error Monitoring with Sentry

```python
"""
Sentry integration for error tracking.
"""
import sentry_sdk
from sentry_sdk.integrations.starlette import StarletteIntegration
from sentry_sdk.integrations.sqlalchemy import SqlalchemyIntegration

def init_sentry(dsn: str, environment: str = "production"):
    """
    Initialize Sentry error tracking.
    
    Args:
        dsn: Sentry DSN
        environment: Environment name
    """
    sentry_sdk.init(
        dsn=dsn,
        environment=environment,
        traces_sample_rate=0.1,  # 10% of transactions
        profiles_sample_rate=0.1,  # 10% for profiling
        integrations=[
            StarletteIntegration(),
            SqlalchemyIntegration(),
        ],
    )
```

[↑ Back to TOC](#-table-of-contents)

---

## 🐳 Docker Deployment

### Multi-Stage Dockerfile

Create `Dockerfile`:

```dockerfile
# Stage 1: Builder
FROM python:3.11-slim as builder

WORKDIR /build

# Install build dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements
COPY requirements.txt .

# Create virtual environment and install dependencies
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    libpq5 \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Copy virtual environment from builder
COPY --from=builder /opt/venv /opt/venv

# Copy application code
COPY app/ ./app/

# Set environment variables
ENV PATH="/opt/venv/bin:$PATH" \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Create non-root user
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# Expose port
EXPOSE 8000

# Run application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
```

### Docker Compose for Development

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  # API Service
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    environment:
      - DEBUG=true
      - DATABASE_URL=postgresql+asyncpg://postgres:password@postgres:5432/todo_db
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
    depends_on:
      - postgres
      - redis
    volumes:
      - ./app:/app/app  # Hot reload in development
    networks:
      - app-network
    restart: unless-stopped

  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=todo_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - app-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Celery Worker
  celery:
    build:
      context: .
      dockerfile: Dockerfile
    command: celery -A app.celery_app worker --loglevel=info
    environment:
      - DATABASE_URL=postgresql+asyncpg://postgres:password@postgres:5432/todo_db
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
    depends_on:
      - postgres
      - redis
    networks:
      - app-network
    restart: unless-stopped

  # Celery Beat (Scheduler)
  celery-beat:
    build:
      context: .
      dockerfile: Dockerfile
    command: celery -A app.celery_beat beat --loglevel=info
    environment:
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/0
    depends_on:
      - redis
    networks:
      - app-network
    restart: unless-stopped

  # Nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
    depends_on:
      - api
    networks:
      - app-network
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:

networks:
  app-network:
    driver: bridge
```

### Nginx Configuration

Create `nginx.conf`:

```nginx
events {
    worker_connections 1024;
}

http {
    upstream api {
        server api:8000;
    }

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

    server {
        listen 80;
        server_name api.example.com;

        # Redirect to HTTPS
        return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name api.example.com;

        # SSL Configuration
        ssl_certificate /etc/nginx/ssl/fullchain.pem;
        ssl_certificate_key /etc/nginx/ssl/privkey.pem;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;

        # Security Headers
        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header Strict-Transport-Security "max-age=31536000" always;

        # Compression
        gzip on;
        gzip_types text/plain application/json;
        gzip_min_length 1000;

        # API Proxy
        location / {
            limit_req zone=api_limit burst=20 nodelay;
            
            proxy_pass http://api;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            
            # WebSocket support
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }

        # Health check endpoint (no rate limit)
        location /health {
            proxy_pass http://api;
            access_log off;
        }
    }
}
```

### Running with Docker Compose

```bash
# Build and start all services
docker-compose up --build -d

# View logs
docker-compose logs -f api

# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v

# Scale API service
docker-compose up --scale api=3 -d
```

[↑ Back to TOC](#-table-of-contents)

---

## ☸️ Kubernetes Deployment

### Kubernetes Manifests

Create `k8s/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-api
  labels:
    app: todo-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: todo-api
  template:
    metadata:
      labels:
        app: todo-api
    spec:
      containers:
      - name: api
        image: yourusername/todo-api:latest
        ports:
        - containerPort: 8000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: todo-secrets
              key: database-url
        - name: REDIS_URL
          valueFrom:
            configMapKeyRef:
              name: todo-config
              key: redis-url
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 5
```

Create `k8s/service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: todo-api-service
spec:
  selector:
    app: todo-api
  ports:
  - port: 80
    targetPort: 8000
  type: LoadBalancer
```

Create `k8s/configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: todo-config
data:
  redis-url: "redis://redis-service:6379/0"
  log-level: "INFO"
```

Create `k8s/secrets.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: todo-secrets
type: Opaque
data:
  database-url: <base64-encoded-url>
  secret-key: <base64-encoded-key>
```

### Deploy to Kubernetes

```bash
# Apply configurations
kubectl apply -f k8s/

# Check deployment
kubectl get deployments
kubectl get pods
kubectl get services

# View logs
kubectl logs -f deployment/todo-api

# Scale deployment
kubectl scale deployment todo-api --replicas=5

# Update deployment
kubectl set image deployment/todo-api api=yourusername/todo-api:v2
```

[↑ Back to TOC](#-table-of-contents)

---

## 🚀 CI/CD Pipelines

### GitHub Actions Workflow

Create `.github/workflows/ci-cd.yml`:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: password
          POSTGRES_DB: test_db
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
      
      redis:
        image: redis:7-alpine
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
        cache: 'pip'
    
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install pytest pytest-cov pytest-asyncio
    
    - name: Run tests
      env:
        DATABASE_URL: postgresql+asyncpg://postgres:password@localhost:5432/test_db
        REDIS_URL: redis://localhost:6379/0
      run: |
        pytest tests/ --cov=app --cov-report=xml --cov-report=term
    
    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
  
  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
    
    - name: Build and push
      uses: docker/build-push-action@v4
      with:
        context: .
        push: true
        tags: yourusername/todo-api:${{ github.sha }},yourusername/todo-api:latest
        cache-from: type=gha
        cache-to: type=gha,mode=max
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Configure kubectl
      uses: azure/setup-kubectl@v3
    
    - name: Set Kubernetes context
      uses: azure/k8s-set-context@v3
      with:
        kubeconfig: ${{ secrets.KUBE_CONFIG }}
    
    - name: Deploy to Kubernetes
      run: |
        kubectl set image deployment/todo-api \
          api=yourusername/todo-api:${{ github.sha }}
        kubectl rollout status deployment/todo-api
```

[↑ Back to TOC](#-table-of-contents)

---

## 📊 Monitoring & Observability

### Prometheus Metrics

Already implemented in Section 5. Add to production:

```python
# app/main.py
from app.plugins.metrics import MetricsPlugin
from app.plugins.tracing import TracingPlugin

app.add_plugin(MetricsPlugin())
app.add_plugin(TracingPlugin(service_name="todo-api-prod"))
```

### Grafana Dashboard

Create `grafana/dashboard.json` (example):

```json
{
  "dashboard": {
    "title": "Todo API Metrics",
    "panels": [
      {
        "title": "Request Rate",
        "targets": [
          {
            "expr": "rate(api_requests_total[5m])"
          }
        ]
      },
      {
        "title": "Error Rate",
        "targets": [
          {
            "expr": "rate(api_requests_total{status=~\"5..\"}[5m])"
          }
        ]
      },
      {
        "title": "Latency (p95)",
        "targets": [
          {
            "expr": "histogram_quantile(0.95, rate(api_request_duration_seconds_bucket[5m]))"
          }
        ]
      }
    ]
  }
}
```

[↑ Back to TOC](#-table-of-contents)

---

## ⚡ Performance Tuning

### Database Optimization

```python
# Use connection pooling
engine = create_async_engine(
    DATABASE_URL,
    pool_size=20,
    max_overflow=10,
    pool_pre_ping=True,
    pool_recycle=3600
)

# Add indexes
class TaskModel(Base):
    __tablename__ = "tasks"
    __table_args__ = (
        Index('idx_status_created', 'status', 'created_at'),
        Index('idx_tags_gin', 'tags', postgresql_using='gin'),
    )
```

### Caching Strategy

```python
# Cache frequently accessed data
@app.endpoint("/tasks/{task_id}", methods=["GET"])
async def get_task(ctx: RequestContext, task_id: str):
    # Try cache first (TTL: 5 minutes)
    cached = await ctx.cache.get(f"task:{task_id}")
    if cached:
        return {"task": cached, "cached": True}
    
    # Fetch from database
    task = await service.get_task(task_id)
    
    # Cache result
    await ctx.cache.set(f"task:{task_id}", task.model_dump(), ttl=300)
    
    return {"task": task.model_dump(), "cached": False}
```

### Load Testing

```bash
# Install locust
pip install locust

# Create locustfile.py
# Run load test
locust -f locustfile.py --host=http://localhost:8000
```

Create `locustfile.py`:

```python
from locust import HttpUser, task, between

class TodoAPIUser(HttpUser):
    wait_time = between(1, 3)
    
    @task(3)
    def list_tasks(self):
        self.client.get("/tasks")
    
    @task(1)
    def create_task(self):
        self.client.post("/tasks", json={
            "title": "Load test task",
            "priority": "medium"
        })
```

[↑ Back to TOC](#-table-of-contents)

---

## 🎓 Summary

You've now implemented production-grade features! Here's what you accomplished:

### ✅ Features Implemented

| Feature | Status | Benefit |
|---------|--------|---------|
| **PostgreSQL** | ✅ Complete | Reliable data persistence |
| **MongoDB** | ✅ Complete | Document storage option |
| **WebSockets** | ✅ Complete | Real-time updates |
| **Background Tasks** | ✅ Complete | Async job processing |
| **Testing** | ✅ Complete | Quality assurance |
| **Docker** | ✅ Complete | Containerization |
| **Kubernetes** | ✅ Complete | Orchestration |
| **CI/CD** | ✅ Complete | Automated deployment |
| **Monitoring** | ✅ Complete | Observability |

### 🚀 Next Steps

**Section 7: Security & Compliance**
- OAuth2 authentication
- Role-based access control
- API rate limiting
- Data encryption

**Section 8: Advanced Topics**
- Multi-tenancy
- Event-driven architecture
- CQRS pattern
- Microservices communication

[↑ Back to TOC](#-table-of-contents)

---

**📌 Note**: This is Section 6 of 8. You now have a production-ready API with database integration, real-time features, background processing, comprehensive testing, and deployment pipelines!

**Congratulations!** You've built an enterprise-grade API framework that's ready for production deployment. 🎉
