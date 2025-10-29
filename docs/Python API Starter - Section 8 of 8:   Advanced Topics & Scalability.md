# 🚀 Python API Starter - Complete Implementation Guide

**Section 8 of 8: Advanced Topics & Scalability**

---

## 📑 Table of Contents

- [🚀 Advanced Topics & Scalability](#-advanced-topics--scalability)
  - [Scalability Architecture Overview](#-scalability-architecture-overview)
  - [Multi-Tenancy Patterns](#-multi-tenancy-patterns)
  - [Event-Driven Architecture](#-event-driven-architecture)
  - [CQRS Pattern](#-cqrs-pattern)
  - [Microservices Communication](#-microservices-communication)
  - [Advanced Caching Strategies](#-advanced-caching-strategies)
  - [Message Queues & Pub/Sub](#-message-queues--pubsub)
  - [Circuit Breakers & Resilience](#-circuit-breakers--resilience)
  - [Database Sharding](#-database-sharding)
  - [High Availability Setup](#-high-availability-setup)
  - [Horizontal Scaling](#-horizontal-scaling)
  - [Service Mesh](#-service-mesh)
  - [Performance Optimization](#-performance-optimization)
  - [Production Deployment Guide](#-production-deployment-guide)
  - [Course Completion & Next Steps](#-course-completion--next-steps)

---

## 🏗️ Scalability Architecture Overview

### Enterprise-Scale Architecture

```mermaid
graph TB
    subgraph EDGE ["🌐    Edge    Layer"]
        CDN[🌍 CDN<br/>CloudFlare/Fastly]
        LB[⚖️ Load Balancer<br/>NGINX/HAProxy]
        WAF[🛡️ WAF<br/>Security filtering]
    end

    subgraph API_GATEWAY ["🚪    API    Gateway    Layer"]
        GW1[🚪 API Gateway 1<br/>Kong/Ambassador]
        GW2[🚪 API Gateway 2<br/>Redundant]
        RL[🚦 Rate Limiter<br/>Redis-based]
        AUTH[🔐 Auth Service<br/>Centralized]
    end

    subgraph SERVICES ["⚙️    Microservices    Layer"]
        SVC1[📋 Task Service<br/>Pods: 3+]
        SVC2[👤 User Service<br/>Pods: 2+]
        SVC3[📊 Analytics Service<br/>Pods: 2+]
        SVC4[📧 Notification Service<br/>Pods: 2+]
    end

    subgraph MESSAGING ["📨    Message    Queue    Layer"]
        MQ1[📬 RabbitMQ/Kafka<br/>Event bus]
        MQ2[🔄 Redis Pub/Sub<br/>Real-time events]
    end

    subgraph DATA ["💾    Data    Layer"]
        PG1[(🐘 PostgreSQL<br/>Primary)]
        PG2[(🐘 PostgreSQL<br/>Read Replica 1)]
        PG3[(🐘 PostgreSQL<br/>Read Replica 2)]
        MONGO[(🍃 MongoDB<br/>Document store)]
        REDIS[(🔴 Redis<br/>Cache cluster)]
        ES[(🔍 Elasticsearch<br/>Search engine)]
    end

    subgraph STORAGE ["💿    Storage    Layer"]
        S3[☁️ S3/MinIO<br/>Object storage]
        BACKUP[💾 Backup Service<br/>Automated backups]
    end

    subgraph MONITORING ["📊    Observability    Layer"]
        PROM[📈 Prometheus<br/>Metrics]
        GRAF[📊 Grafana<br/>Dashboards]
        JAEGER[🔍 Jaeger<br/>Distributed tracing]
        ELK[📝 ELK Stack<br/>Log aggregation]
    end

    %% Edge to Gateway - Blue
    CDN --> LB
    LB --> WAF
    WAF --> GW1
    WAF --> GW2
    linkStyle 0,1,2,3 stroke:#1976d2,stroke-width:3px

    %% Gateway to Services - Purple
    GW1 --> RL
    GW2 --> RL
    RL --> AUTH
    AUTH --> SVC1
    AUTH --> SVC2
    AUTH --> SVC3
    AUTH --> SVC4
    linkStyle 4,5,6,7,8,9,10 stroke:#7b1fa2,stroke-width:3px

    %% Services to Messaging - Teal
    SVC1 --> MQ1
    SVC2 --> MQ1
    SVC3 --> MQ1
    SVC4 --> MQ2
    linkStyle 11,12,13,14 stroke:#00695c,stroke-width:3px

    %% Services to Data - Orange
    SVC1 --> PG1
    SVC1 --> PG2
    SVC2 --> PG1
    SVC3 --> MONGO
    SVC1 --> REDIS
    SVC2 --> REDIS
    SVC3 --> ES
    linkStyle 15,16,17,18,19,20,21 stroke:#f57c00,stroke-width:2px

    %% Data replication - Green (dashed)
    PG1 -.-> PG2
    PG1 -.-> PG3
    PG1 -.-> BACKUP
    linkStyle 22,23,24 stroke:#388e3c,stroke-width:2px

    %% Storage connections - Brown (dashed)
    SVC1 -.-> S3
    SVC4 -.-> S3
    linkStyle 25,26 stroke:#795548,stroke-width:2px

    %% Monitoring connections - Indigo (dashed)
    SVC1 -.-> PROM
    SVC2 -.-> PROM
    SVC3 -.-> PROM
    PROM -.-> GRAF
    SVC1 -.-> JAEGER
    SVC1 -.-> ELK
    linkStyle 27,28,29,30,31,32 stroke:#3f51b5,stroke-width:2px

    %% Subgraph styling
    style EDGE fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style API_GATEWAY fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style SERVICES fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style MESSAGING fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style DATA fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    style STORAGE fill:#efebe9,stroke:#795548,stroke-width:3px,color:#000
    style MONITORING fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
```

### Scalability Patterns

| Pattern | Use Case | Benefits |
|---------|----------|----------|
| **Horizontal Scaling** | Add more instances | Linear scalability |
| **Database Replication** | Read-heavy workloads | Distribute read load |
| **Caching** | Frequent data access | Reduce DB load |
| **Message Queues** | Async processing | Decouple services |
| **CDN** | Static content | Global distribution |
| **Sharding** | Large datasets | Partition data |
| **Circuit Breakers** | Service failures | Prevent cascading failures |
| **Load Balancing** | Distribute traffic | High availability |

[↑ Back to TOC](#-table-of-contents)

---

## 🏢 Multi-Tenancy Patterns

### Multi-Tenancy Architecture

```mermaid
graph TB
    subgraph REQUEST ["📥    Incoming    Request"]
        REQ[🌐 HTTP Request<br/>tenant: acme-corp]
    end

    subgraph IDENTIFICATION ["🔍    Tenant    Identification"]
        ID1[📋 Header-based<br/>X-Tenant-ID]
        ID2[🌐 Subdomain<br/>acme.api.com]
        ID3[🔑 API Key<br/>tenant_abc123]
        ID4[🎫 JWT Claim<br/>tenant_id in token]
    end

    subgraph ISOLATION ["🔒    Data    Isolation    Strategies"]
        ISO1[🗄️ Separate Database<br/>Per tenant DB]
        ISO2[📊 Shared Database<br/>Separate schemas]
        ISO3[🏷️ Row-Level Security<br/>tenant_id column]
    end

    subgraph ROUTING ["🚦    Tenant    Routing"]
        RT1[📍 Route to DB<br/>Connection pool]
        RT2[🔐 Apply filters<br/>WHERE tenant_id=X]
        RT3[⚙️ Load config<br/>Tenant settings]
    end

    subgraph DATA ["💾    Data    Storage"]
        DB1[(🏢 Tenant A<br/>acme_db)]
        DB2[(🏢 Tenant B<br/>startup_db)]
        DB3[(🏢 Shared DB<br/>tenant_id filter)]
    end

    %% Request to Identification
    REQ --> ID1
    REQ --> ID2
    REQ --> ID3
    REQ --> ID4
    linkStyle 0,1,2,3 stroke:#1976d2,stroke-width:3px

    %% Identification to Isolation
    ID1 --> ISO1
    ID2 --> ISO2
    ID3 --> ISO3
    ID4 --> ISO3
    linkStyle 4,5,6,7 stroke:#7b1fa2,stroke-width:3px

    %% Isolation to Routing
    ISO1 --> RT1
    ISO2 --> RT1
    ISO3 --> RT2
    linkStyle 8,9,10 stroke:#00695c,stroke-width:3px

    %% Routing to Data
    RT1 --> DB1
    RT1 --> DB2
    RT2 --> DB3
    RT3 --> DB3
    linkStyle 11,12,13,14 stroke:#f57c00,stroke-width:3px

    %% Styling
    style REQUEST fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style IDENTIFICATION fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style ISOLATION fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style ROUTING fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style DATA fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
```

### Tenant Identification

Create `app/multitenancy/tenant_context.py`:

```python
"""
Multi-tenancy context management.
"""
from contextvars import ContextVar
from typing import Optional
from dataclasses import dataclass

# Thread-safe tenant context
_tenant_context: ContextVar[Optional['TenantContext']] = ContextVar(
    'tenant_context',
    default=None
)


@dataclass
class TenantContext:
    """
    Tenant context information.
    
    Stores current tenant ID and metadata.
    """
    tenant_id: str
    tenant_name: str
    schema_name: Optional[str] = None
    database_url: Optional[str] = None
    settings: dict = None
    
    def __post_init__(self):
        if self.settings is None:
            self.settings = {}


def set_tenant_context(context: TenantContext):
    """Set current tenant context."""
    _tenant_context.set(context)


def get_tenant_context() -> Optional[TenantContext]:
    """Get current tenant context."""
    return _tenant_context.get()


def clear_tenant_context():
    """Clear tenant context."""
    _tenant_context.set(None)


def require_tenant_context() -> TenantContext:
    """
    Get tenant context or raise error.
    
    Raises:
        RuntimeError: If no tenant context set
    """
    context = get_tenant_context()
    if context is None:
        raise RuntimeError("No tenant context available")
    return context
```

### Tenant Resolution Strategies

Create `app/multitenancy/tenant_resolver.py`:

```python
"""
Tenant resolution strategies.
"""
from abc import ABC, abstractmethod
from typing import Optional
from urllib.parse import urlparse

from starlette.requests import Request

from app.models.tenant import Tenant
from app.repositories.tenant_repository import TenantRepository


class TenantResolver(ABC):
    """Base tenant resolver."""
    
    @abstractmethod
    async def resolve(self, request: Request) -> Optional[str]:
        """
        Resolve tenant ID from request.
        
        Args:
            request: HTTP request
        
        Returns:
            Tenant ID or None
        """
        pass


class HeaderTenantResolver(TenantResolver):
    """
    Resolve tenant from HTTP header.
    
    Example: X-Tenant-ID: acme-corp
    """
    
    def __init__(self, header_name: str = "X-Tenant-ID"):
        self.header_name = header_name
    
    async def resolve(self, request: Request) -> Optional[str]:
        """Resolve from header."""
        return request.headers.get(self.header_name)


class SubdomainTenantResolver(TenantResolver):
    """
    Resolve tenant from subdomain.
    
    Example: acme.api.example.com -> acme
    """
    
    def __init__(self, base_domain: str = "api.example.com"):
        self.base_domain = base_domain
    
    async def resolve(self, request: Request) -> Optional[str]:
        """Resolve from subdomain."""
        host = request.headers.get("host", "")
        
        # Remove port if present
        host = host.split(":")[0]
        
        # Check if subdomain exists
        if host.endswith(self.base_domain):
            subdomain = host.replace(f".{self.base_domain}", "")
            if subdomain and subdomain != self.base_domain:
                return subdomain
        
        return None


class JWTTenantResolver(TenantResolver):
    """
    Resolve tenant from JWT token claim.
    
    Requires authentication first.
    """
    
    async def resolve(self, request: Request) -> Optional[str]:
        """Resolve from JWT token."""
        from app.security.jwt import verify_token
        
        # Get token from header
        auth_header = request.headers.get("authorization", "")
        if not auth_header.startswith("Bearer "):
            return None
        
        token = auth_header[7:]
        payload = verify_token(token)
        
        if payload:
            return payload.get("tenant_id")
        
        return None


class APIKeyTenantResolver(TenantResolver):
    """
    Resolve tenant from API key.
    
    Looks up tenant associated with API key.
    """
    
    def __init__(self, api_key_service):
        self.api_key_service = api_key_service
    
    async def resolve(self, request: Request) -> Optional[str]:
        """Resolve from API key."""
        api_key = request.headers.get("X-API-Key")
        if not api_key:
            return None
        
        # Verify API key and get tenant
        key_entity = await self.api_key_service.verify_api_key(api_key)
        
        if key_entity:
            return key_entity.tenant_id
        
        return None


class CompositeTenantResolver(TenantResolver):
    """
    Try multiple resolution strategies in order.
    
    Example:
        resolver = CompositeTenantResolver([
            HeaderTenantResolver(),
            SubdomainTenantResolver(),
            JWTTenantResolver()
        ])
    """
    
    def __init__(self, resolvers: list[TenantResolver]):
        self.resolvers = resolvers
    
    async def resolve(self, request: Request) -> Optional[str]:
        """Try each resolver until one succeeds."""
        for resolver in self.resolvers:
            tenant_id = await resolver.resolve(request)
            if tenant_id:
                return tenant_id
        
        return None
```

### Row-Level Security Implementation

Create `app/multitenancy/row_level_security.py`:

```python
"""
Row-level security for shared database multi-tenancy.
"""
from sqlalchemy import Column, String, event
from sqlalchemy.orm import Session

from app.multitenancy.tenant_context import get_tenant_context, require_tenant_context


class TenantMixin:
    """
    Mixin for tenant-scoped models.
    
    Add this to any model that should be tenant-scoped.
    
    Example:
        class Task(Base, TenantMixin):
            __tablename__ = "tasks"
            id = Column(String, primary_key=True)
            title = Column(String)
            # tenant_id is added by mixin
    """
    
    tenant_id = Column(String, nullable=False, index=True)


def apply_tenant_filters(session: Session):
    """
    Apply tenant filters to all queries automatically.
    
    This ensures queries only return data for current tenant.
    """
    tenant_context = get_tenant_context()
    
    if tenant_context:
        # Add filter to all queries
        @event.listens_for(session, "after_attach")
        def receive_after_attach(session, instance):
            """Set tenant_id on new instances."""
            if hasattr(instance, 'tenant_id') and not instance.tenant_id:
                instance.tenant_id = tenant_context.tenant_id
        
        @event.listens_for(session, "before_flush")
        def receive_before_flush(session, flush_context, instances):
            """Validate tenant_id before flush."""
            for instance in session.new:
                if hasattr(instance, 'tenant_id'):
                    if not instance.tenant_id:
                        instance.tenant_id = tenant_context.tenant_id
                    elif instance.tenant_id != tenant_context.tenant_id:
                        raise ValueError(
                            f"Cannot create instance for different tenant: "
                            f"expected {tenant_context.tenant_id}, "
                            f"got {instance.tenant_id}"
                        )


class TenantScopedQuery:
    """
    Automatically scope queries to current tenant.
    
    Example:
        tasks = await TenantScopedQuery(session, Task).all()
        # Automatically filtered by tenant_id
    """
    
    def __init__(self, session: Session, model):
        self.session = session
        self.model = model
        self.tenant_context = require_tenant_context()
    
    def _add_tenant_filter(self, query):
        """Add tenant filter to query."""
        if hasattr(self.model, 'tenant_id'):
            return query.filter(
                self.model.tenant_id == self.tenant_context.tenant_id
            )
        return query
    
    async def all(self):
        """Get all records for current tenant."""
        from sqlalchemy import select
        query = select(self.model)
        query = self._add_tenant_filter(query)
        result = await self.session.execute(query)
        return result.scalars().all()
    
    async def get(self, id):
        """Get record by ID for current tenant."""
        from sqlalchemy import select
        query = select(self.model).where(self.model.id == id)
        query = self._add_tenant_filter(query)
        result = await self.session.execute(query)
        return result.scalar_one_or_none()
```

### Multi-Tenancy Middleware

Create `app/plugins/multitenancy.py`:

```python
"""
Multi-tenancy plugin.
"""
from starlette.responses import JSONResponse

from api_starter.core.application import Plugin, RequestContext
from app.multitenancy.tenant_context import (
    set_tenant_context,
    clear_tenant_context,
    TenantContext
)
from app.multitenancy.tenant_resolver import TenantResolver
from app.repositories.tenant_repository import TenantRepository


class MultiTenancyPlugin(Plugin):
    """
    Multi-tenancy plugin.
    
    Features:
    - Automatic tenant resolution
    - Tenant context management
    - Row-level security
    - Per-tenant configuration
    
    Example:
        from app.multitenancy.tenant_resolver import HeaderTenantResolver
        
        app.add_plugin(MultiTenancyPlugin(
            resolver=HeaderTenantResolver()
        ))
    """
    
    def __init__(self, resolver: TenantResolver, required: bool = True):
        super().__init__(name="multitenancy")
        self.resolver = resolver
        self.required = required
    
    async def on_request(self, ctx: RequestContext):
        """Resolve and set tenant context."""
        from starlette.requests import Request
        request = Request(ctx._request)
        
        # Resolve tenant ID
        tenant_id = await self.resolver.resolve(request)
        
        if not tenant_id:
            if self.required:
                raise Exception("Tenant ID required but not provided")
            return
        
        # Load tenant from database
        async with ctx.get_db_session() as session:
            repo = TenantRepository(session)
            tenant = await repo.get_by_id(tenant_id)
            
            if not tenant:
                raise Exception(f"Tenant not found: {tenant_id}")
            
            if not tenant.is_active:
                raise Exception(f"Tenant is inactive: {tenant_id}")
        
        # Set tenant context
        tenant_context = TenantContext(
            tenant_id=tenant.id,
            tenant_name=tenant.name,
            schema_name=tenant.schema_name,
            settings=tenant.settings
        )
        
        set_tenant_context(tenant_context)
        
        # Attach to request context
        ctx.tenant_id = tenant.id
        ctx.tenant_name = tenant.name
    
    async def on_response(self, ctx: RequestContext, response):
        """Clear tenant context after request."""
        clear_tenant_context()


class TenantIsolationPlugin(Plugin):
    """
    Enforce strict tenant isolation in queries.
    
    Add this plugin after MultiTenancyPlugin.
    """
    
    def __init__(self):
        super().__init__(name="tenant_isolation")
    
    async def on_request(self, ctx: RequestContext):
        """Apply tenant filters to database session."""
        from app.multitenancy.row_level_security import apply_tenant_filters
        
        if hasattr(ctx, 'db_session') and ctx.db_session:
            apply_tenant_filters(ctx.db_session)
```

### Tenant Model & Repository

Create `app/models/tenant.py`:

```python
"""
Tenant models for multi-tenancy.
"""
from datetime import datetime
from typing import Optional, Dict, Any
from pydantic import BaseModel, Field

from api_starter.models import EntityModel


class TenantCreate(BaseModel):
    """Tenant creation request."""
    name: str = Field(min_length=1, max_length=100)
    schema_name: Optional[str] = None  # For schema-per-tenant
    database_url: Optional[str] = None  # For database-per-tenant
    settings: Dict[str, Any] = Field(default_factory=dict)


class Tenant(EntityModel):
    """Tenant entity."""
    name: str
    schema_name: Optional[str] = None
    database_url: Optional[str] = None
    is_active: bool = True
    settings: Dict[str, Any] = Field(default_factory=dict)
    
    # Usage tracking
    user_count: int = 0
    storage_used_bytes: int = 0
    
    # Subscription info
    plan: str = "free"  # free, basic, premium
    subscription_ends_at: Optional[datetime] = None
```

### Using Multi-Tenancy

```python
# Configure multi-tenancy
from app.multitenancy.tenant_resolver import (
    CompositeTenantResolver,
    HeaderTenantResolver,
    SubdomainTenantResolver
)
from app.plugins.multitenancy import MultiTenancyPlugin, TenantIsolationPlugin

# Use multiple resolution strategies
resolver = CompositeTenantResolver([
    HeaderTenantResolver(),
    SubdomainTenantResolver(base_domain="api.example.com")
])

app.add_plugin(MultiTenancyPlugin(resolver=resolver, required=True))
app.add_plugin(TenantIsolationPlugin())


# In endpoints - tenant is automatically available
@app.endpoint("/tasks", methods=["GET"])
@require_auth
async def list_tasks(ctx: RequestContext):
    """
    List tasks for current tenant.
    
    Automatically filtered by tenant_id.
    """
    async with ctx.get_db_session() as session:
        # Queries are automatically scoped to current tenant
        repo = TaskRepository(session)
        tasks = await repo.list()  # Only returns current tenant's tasks
    
    return {
        "tenant_id": ctx.tenant_id,
        "tenant_name": ctx.tenant_name,
        "tasks": [t.model_dump() for t in tasks]
    }
```

[↑ Back to TOC](#-table-of-contents)

---

## 📨 Event-Driven Architecture

### Event Flow Architecture

```mermaid
graph TB
    subgraph PRODUCERS ["📤    Event    Producers"]
        P1[📋 Task Service<br/>Creates events]
        P2[👤 User Service<br/>Creates events]
        P3[📧 Email Service<br/>Creates events]
    end

    subgraph EVENT_BUS ["🚌    Event    Bus"]
        EB1[📨 RabbitMQ<br/>Message broker]
        EB2[🎯 Event Topics<br/>task.*, user.*]
        EB3[📦 Event Store<br/>Event sourcing]
    end

    subgraph CONSUMERS ["📥    Event    Consumers"]
        C1[📊 Analytics<br/>Aggregate data]
        C2[📧 Notifications<br/>Send alerts]
        C3[🔍 Search Index<br/>Update ES]
        C4[📝 Audit Logger<br/>Record events]
    end

    subgraph HANDLERS ["⚙️    Event    Handlers"]
        H1[🎯 Task Created<br/>Handler]
        H2[✏️ Task Updated<br/>Handler]
        H3[👤 User Registered<br/>Handler]
    end

    %% Producers to Event Bus
    P1 --> EB1
    P2 --> EB1
    P3 --> EB1
    linkStyle 0,1,2 stroke:#1976d2,stroke-width:3px

    %% Event Bus routing
    EB1 --> EB2
    EB2 --> EB3
    linkStyle 3,4 stroke:#7b1fa2,stroke-width:3px

    %% Event Bus to Consumers
    EB2 --> C1
    EB2 --> C2
    EB2 --> C3
    EB2 --> C4
    linkStyle 5,6,7,8 stroke:#00695c,stroke-width:3px

    %% Consumers to Handlers
    C1 --> H1
    C2 --> H2
    C3 --> H3
    C4 --> H1
    linkStyle 9,10,11,12 stroke:#f57c00,stroke-width:3px

    %% Styling
    style PRODUCERS fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style EVENT_BUS fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style CONSUMERS fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style HANDLERS fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
```

### Event Models

Create `app/events/base.py`:

```python
"""
Base event models and infrastructure.
"""
from datetime import datetime
from typing import Any, Dict, Optional
from enum import Enum
from pydantic import BaseModel, Field
import uuid


class EventType(str, Enum):
    """Event types."""
    TASK_CREATED = "task.created"
    TASK_UPDATED = "task.updated"
    TASK_DELETED = "task.deleted"
    TASK_COMPLETED = "task.completed"
    
    USER_REGISTERED = "user.registered"
    USER_UPDATED = "user.updated"
    USER_DELETED = "user.deleted"
    
    EMAIL_SENT = "email.sent"
    EMAIL_FAILED = "email.failed"


class DomainEvent(BaseModel):
    """
    Base domain event.
    
    All events should inherit from this.
    """
    event_id: str = Field(default_factory=lambda: str(uuid.uuid4()))
    event_type: EventType
    aggregate_id: str  # ID of the entity this event relates to
    aggregate_type: str  # Type of entity (task, user, etc.)
    tenant_id: Optional[str] = None
    user_id: Optional[str] = None
    timestamp: datetime = Field(default_factory=datetime.utcnow)
    version: int = 1
    metadata: Dict[str, Any] = Field(default_factory=dict)
    payload: Dict[str, Any] = Field(default_factory=dict)
    
    class Config:
        json_encoders = {
            datetime: lambda v: v.isoformat()
        }


class TaskCreatedEvent(DomainEvent):
    """Event emitted when task is created."""
    event_type: EventType = EventType.TASK_CREATED
    aggregate_type: str = "task"


class TaskUpdatedEvent(DomainEvent):
    """Event emitted when task is updated."""
    event_type: EventType = EventType.TASK_UPDATED
    aggregate_type: str = "task"


class TaskDeletedEvent(DomainEvent):
    """Event emitted when task is deleted."""
    event_type: EventType = EventType.TASK_DELETED
    aggregate_type: str = "task"


class TaskCompletedEvent(DomainEvent):
    """Event emitted when task is completed."""
    event_type: EventType = EventType.TASK_COMPLETED
    aggregate_type: str = "task"


class UserRegisteredEvent(DomainEvent):
    """Event emitted when user registers."""
    event_type: EventType = EventType.USER_REGISTERED
    aggregate_type: str = "user"
```

### Event Bus Implementation

Create `app/events/event_bus.py`:

```python
"""
Event bus for publishing and subscribing to events.
"""
import asyncio
from typing import Callable, List, Dict, Type
import json

from app.events.base import DomainEvent, EventType


class EventBus:
    """
    In-memory event bus for pub/sub.
    
    For production, use RabbitMQ, Kafka, or AWS SNS/SQS.
    """
    
    def __init__(self):
        # Event handlers: {event_type: [handler1, handler2, ...]}
        self._handlers: Dict[EventType, List[Callable]] = {}
        
        # Event history (for debugging/replay)
        self._event_history: List[DomainEvent] = []
    
    def subscribe(
        self,
        event_type: EventType,
        handler: Callable[[DomainEvent], None]
    ):
        """
        Subscribe to event type.
        
        Args:
            event_type: Type of event to listen for
            handler: Async function to handle event
        
        Example:
            async def on_task_created(event: TaskCreatedEvent):
                print(f"Task created: {event.aggregate_id}")
            
            event_bus.subscribe(EventType.TASK_CREATED, on_task_created)
        """
        if event_type not in self._handlers:
            self._handlers[event_type] = []
        
        self._handlers[event_type].append(handler)
    
    async def publish(self, event: DomainEvent):
        """
        Publish event to all subscribers.
        
        Args:
            event: Domain event to publish
        
        Example:
            event = TaskCreatedEvent(
                aggregate_id=task.id,
                payload={"title": task.title}
            )
            await event_bus.publish(event)
        """
        # Store in history
        self._event_history.append(event)
        
        # Get handlers for this event type
        handlers = self._handlers.get(event.event_type, [])
        
        # Execute all handlers concurrently
        if handlers:
            await asyncio.gather(
                *[handler(event) for handler in handlers],
                return_exceptions=True  # Don't fail if one handler fails
            )
    
    def get_history(
        self,
        event_type: Optional[EventType] = None,
        aggregate_id: Optional[str] = None
    ) -> List[DomainEvent]:
        """
        Get event history.
        
        Args:
            event_type: Filter by event type
            aggregate_id: Filter by aggregate ID
        
        Returns:
            List of events
        """
        events = self._event_history
        
        if event_type:
            events = [e for e in events if e.event_type == event_type]
        
        if aggregate_id:
            events = [e for e in events if e.aggregate_id == aggregate_id]
        
        return events


# Global event bus instance
_event_bus: Optional[EventBus] = None


def get_event_bus() -> EventBus:
    """Get global event bus instance."""
    global _event_bus
    if _event_bus is None:
        _event_bus = EventBus()
    return _event_bus
```

### RabbitMQ Event Bus (Production)

Create `app/events/rabbitmq_bus.py`:

```python
"""
RabbitMQ-based event bus for production.
"""
import asyncio
import json
from typing import Callable, Dict
import aio_pika

from app.events.base import DomainEvent, EventType


class RabbitMQEventBus:
    """
    Event bus using RabbitMQ.
    
    Features:
    - Persistent messages
    - Guaranteed delivery
    - Message acknowledgment
    - Dead letter queues
    """
    
    def __init__(self, rabbitmq_url: str):
        self.rabbitmq_url = rabbitmq_url
        self.connection: Optional[aio_pika.Connection] = None
        self.channel: Optional[aio_pika.Channel] = None
        self.exchange: Optional[aio_pika.Exchange] = None
        self._handlers: Dict[EventType, List[Callable]] = {}
    
    async def connect(self):
        """Connect to RabbitMQ."""
        self.connection = await aio_pika.connect_robust(self.rabbitmq_url)
        self.channel = await self.connection.channel()
        
        # Declare exchange
        self.exchange = await self.channel.declare_exchange(
            "domain_events",
            aio_pika.ExchangeType.TOPIC,
            durable=True
        )
        
        print("✓ Connected to RabbitMQ")
    
    async def disconnect(self):
        """Disconnect from RabbitMQ."""
        if self.connection:
            await self.connection.close()
    
    async def publish(self, event: DomainEvent):
        """
        Publish event to RabbitMQ.
        
        Args:
            event: Domain event
        """
        if not self.exchange:
            raise RuntimeError("Not connected to RabbitMQ")
        
        # Serialize event
        message_body = event.model_dump_json().encode()
        
        # Create message
        message = aio_pika.Message(
            body=message_body,
            delivery_mode=aio_pika.DeliveryMode.PERSISTENT,
            content_type="application/json",
            headers={
                "event_type": event.event_type.value,
                "event_id": event.event_id,
                "tenant_id": event.tenant_id
            }
        )
        
        # Publish to exchange with routing key
        routing_key = event.event_type.value  # e.g., "task.created"
        await self.exchange.publish(message, routing_key=routing_key)
    
    async def subscribe(
        self,
        event_type: EventType,
        handler: Callable[[DomainEvent], None],
        queue_name: Optional[str] = None
    ):
        """
        Subscribe to event type.
        
        Args:
            event_type: Event type to subscribe to
            handler: Handler function
            queue_name: Queue name (default: auto-generated)
        """
        if not self.channel:
            raise RuntimeError("Not connected to RabbitMQ")
        
        # Declare queue
        queue_name = queue_name or f"queue_{event_type.value}"
        queue = await self.channel.declare_queue(
            queue_name,
            durable=True
        )
        
        # Bind queue to exchange
        await queue.bind(
            self.exchange,
            routing_key=event_type.value
        )
        
        # Start consuming
        async def on_message(message: aio_pika.IncomingMessage):
            async with message.process():
                # Deserialize event
                event_data = json.loads(message.body.decode())
                event = DomainEvent(**event_data)
                
                # Call handler
                try:
                    await handler(event)
                except Exception as e:
                    print(f"Error handling event: {e}")
                    # Message will be requeued if not acknowledged
                    raise
        
        await queue.consume(on_message)
```

### Event Handlers

Create `app/events/handlers.py`:

```python
"""
Event handlers.
"""
from app.events.base import (
    TaskCreatedEvent,
    TaskCompletedEvent,
    UserRegisteredEvent
)


async def on_task_created(event: TaskCreatedEvent):
    """
    Handle task created event.
    
    Actions:
    - Send notification
    - Update analytics
    - Index in search
    """
    print(f"[EVENT] Task created: {event.aggregate_id}")
    
    # Send notification
    # await notification_service.send(...)
    
    # Update search index
    # await elasticsearch.index(...)
    
    # Track analytics
    # await analytics.track("task_created", ...)


async def on_task_completed(event: TaskCompletedEvent):
    """
    Handle task completed event.
    
    Actions:
    - Send congratulations email
    - Update statistics
    - Award points (gamification)
    """
    print(f"[EVENT] Task completed: {event.aggregate_id}")
    
    # Send email
    # await email_service.send_template(...)
    
    # Update user stats
    # await user_service.increment_tasks_completed(...)


async def on_user_registered(event: UserRegisteredEvent):
    """
    Handle user registration event.
    
    Actions:
    - Send welcome email
    - Create default tasks
    - Set up user workspace
    """
    print(f"[EVENT] User registered: {event.aggregate_id}")
    
    # Send welcome email
    # await email_service.send_welcome(...)
    
    # Create sample tasks
    # await task_service.create_sample_tasks(...)


# Register all handlers
def register_event_handlers(event_bus):
    """Register all event handlers with event bus."""
    from app.events.base import EventType
    
    event_bus.subscribe(EventType.TASK_CREATED, on_task_created)
    event_bus.subscribe(EventType.TASK_COMPLETED, on_task_completed)
    event_bus.subscribe(EventType.USER_REGISTERED, on_user_registered)
    
    print("✓ Event handlers registered")
```

### Using Events in Services

```python
from app.events.event_bus import get_event_bus
from app.events.base import TaskCreatedEvent, TaskCompletedEvent


class TaskService:
    """Task service with event publishing."""
    
    def __init__(self, repository: TaskRepository):
        self.repo = repository
        self.event_bus = get_event_bus()
    
    async def create_task(self, data: CreateTaskRequest) -> Task:
        """Create task and publish event."""
        # Create task
        task = await self.repo.create(data)
        
        # Publish event
        event = TaskCreatedEvent(
            aggregate_id=task.id,
            tenant_id=task.tenant_id,
            user_id=task.user_id,
            payload={
                "title": task.title,
                "priority": task.priority.value,
                "due_date": task.due_date.isoformat() if task.due_date else None
            }
        )
        
        await self.event_bus.publish(event)
        
        return task
    
    async def complete_task(self, task_id: str) -> Task:
        """Complete task and publish event."""
        # Update task
        task = await self.repo.get_by_id(task_id)
        task.completed = True
        task.completed_at = datetime.utcnow()
        await self.repo.update(task)
        
        # Publish event
        event = TaskCompletedEvent(
            aggregate_id=task.id,
            tenant_id=task.tenant_id,
            user_id=task.user_id,
            payload={
                "title": task.title,
                "completed_at": task.completed_at.isoformat()
            }
        )
        
        await self.event_bus.publish(event)
        
        return task
```

[↑ Back to TOC](#-table-of-contents)

---

## 🔀 CQRS Pattern

### CQRS Architecture

```mermaid
graph TB
    subgraph CLIENT ["📱    Client    Application"]
        UI[🖥️ User Interface<br/>Forms & Views]
    end

    subgraph COMMANDS ["📝    Command    Side    (Write)"]
        CMD1[✏️ CreateTask<br/>Command]
        CMD2[✅ CompleteTask<br/>Command]
        CMD3[🗑️ DeleteTask<br/>Command]
        CH[⚙️ Command Handlers<br/>Business logic]
        WDB[(💾 Write Database<br/>PostgreSQL)]
    end

    subgraph EVENTS ["📨    Event    Store"]
        ES[(📦 Event Store<br/>All domain events)]
        EB[🚌 Event Bus<br/>Pub/Sub]
    end

    subgraph QUERIES ["📖    Query    Side    (Read)"]
        Q1[📋 ListTasks<br/>Query]
        Q2[📊 GetStatistics<br/>Query]
        Q3[🔍 SearchTasks<br/>Query]
        QH[⚙️ Query Handlers<br/>Data retrieval]
        RDB[(🔍 Read Database<br/>Optimized views)]
        CACHE[(🔴 Redis Cache<br/>Fast reads)]
    end

    subgraph PROJECTIONS ["🎯    Projections    &    Denormalization"]
        P1[📊 Task Statistics<br/>Materialized view]
        P2[🔍 Search Index<br/>Elasticsearch]
        P3[📈 Analytics<br/>Time-series data]
    end

    %% UI to Commands
    UI -->|Write| CMD1
    UI -->|Write| CMD2
    UI -->|Write| CMD3
    linkStyle 0,1,2 stroke:#1976d2,stroke-width:3px

    %% Commands processing
    CMD1 --> CH
    CMD2 --> CH
    CMD3 --> CH
    CH --> WDB
    linkStyle 3,4,5,6 stroke:#7b1fa2,stroke-width:3px

    %% Commands to Events
    CH --> ES
    ES --> EB
    linkStyle 7,8 stroke:#00695c,stroke-width:3px

    %% UI to Queries
    UI -->|Read| Q1
    UI -->|Read| Q2
    UI -->|Read| Q3
    linkStyle 9,10,11 stroke:#f57c00,stroke-width:3px

    %% Queries processing
    Q1 --> QH
    Q2 --> QH
    Q3 --> QH
    QH --> RDB
    QH --> CACHE
    linkStyle 12,13,14,15,16 stroke:#f57c00,stroke-width:3px

    %% Events to Projections
    EB --> P1
    EB --> P2
    EB --> P3
    linkStyle 17,18,19 stroke:#388e3c,stroke-width:3px

    %% Projections to Read DB
    P1 --> RDB
    P2 --> RDB
    P3 --> RDB
    linkStyle 20,21,22 stroke:#388e3c,stroke-width:2px

    %% Styling
    style CLIENT fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style COMMANDS fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style EVENTS fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style QUERIES fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style PROJECTIONS fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
```

### Command Models

Create `app/cqrs/commands.py`:

```python
"""
CQRS Commands (Write operations).
"""
from abc import ABC, abstractmethod
from typing import Generic, TypeVar, Optional
from pydantic import BaseModel


TResult = TypeVar('TResult')


class Command(BaseModel, ABC):
    """
    Base command.
    
    Commands represent write operations.
    """
    
    class Config:
        frozen = True  # Commands are immutable


class CreateTaskCommand(Command):
    """Command to create a task."""
    title: str
    description: Optional[str] = None
    priority: str = "medium"
    due_date: Optional[str] = None
    tags: list[str] = []
    user_id: str
    tenant_id: str


class UpdateTaskCommand(Command):
    """Command to update a task."""
    task_id: str
    title: Optional[str] = None
    description: Optional[str] = None
    priority: Optional[str] = None
    status: Optional[str] = None
    user_id: str
    tenant_id: str


class CompleteTaskCommand(Command):
    """Command to complete a task."""
    task_id: str
    user_id: str
    tenant_id: str


class DeleteTaskCommand(Command):
    """Command to delete a task."""
    task_id: str
    user_id: str
    tenant_id: str


class CommandHandler(ABC, Generic[TResult]):
    """
    Base command handler.
    
    Handlers contain business logic for commands.
    """
    
    @abstractmethod
    async def handle(self, command: Command) -> TResult:
        """
        Handle command.
        
        Args:
            command: Command to execute
        
        Returns:
            Command result
        """
        pass
```

### Command Handlers

Create `app/cqrs/command_handlers.py`:

```python
"""
Command handlers implementation.
"""
from app.cqrs.commands import (
    CreateTaskCommand,
    UpdateTaskCommand,
    CompleteTaskCommand,
    DeleteTaskCommand,
    CommandHandler
)
from app.models.task import Task, CreateTaskRequest, UpdateTaskRequest
from app.repositories.task_repository import TaskRepository
from app.events.event_bus import get_event_bus
from app.events.base import (
    TaskCreatedEvent,
    TaskUpdatedEvent,
    TaskCompletedEvent,
    TaskDeletedEvent
)


class CreateTaskCommandHandler(CommandHandler[Task]):
    """Handler for CreateTaskCommand."""
    
    def __init__(self, repository: TaskRepository):
        self.repo = repository
        self.event_bus = get_event_bus()
    
    async def handle(self, command: CreateTaskCommand) -> Task:
        """Create task and publish event."""
        # Create task
        request = CreateTaskRequest(
            title=command.title,
            description=command.description,
            priority=command.priority,
            due_date=command.due_date,
            tags=command.tags
        )
        
        task = await self.repo.create(request)
        
        # Publish event
        event = TaskCreatedEvent(
            aggregate_id=task.id,
            tenant_id=command.tenant_id,
            user_id=command.user_id,
            payload=task.model_dump()
        )
        
        await self.event_bus.publish(event)
        
        return task


class CompleteTaskCommandHandler(CommandHandler[Task]):
    """Handler for CompleteTaskCommand."""
    
    def __init__(self, repository: TaskRepository):
        self.repo = repository
        self.event_bus = get_event_bus()
    
    async def handle(self, command: CompleteTaskCommand) -> Task:
        """Complete task and publish event."""
        from datetime import datetime
        
        # Get task
        task = await self.repo.get_by_id(command.task_id)
        if not task:
            raise ValueError(f"Task not found: {command.task_id}")
        
        # Check ownership
        if task.tenant_id != command.tenant_id:
            raise ValueError("Access denied")
        
        # Update task
        update_request = UpdateTaskRequest(
            completed=True
        )
        
        task = await self.repo.update(command.task_id, update_request)
        
        # Publish event
        event = TaskCompletedEvent(
            aggregate_id=task.id,
            tenant_id=command.tenant_id,
            user_id=command.user_id,
            payload={
                "title": task.title,
                "completed_at": task.completed_at.isoformat()
            }
        )
        
        await self.event_bus.publish(event)
        
        return task


class DeleteTaskCommandHandler(CommandHandler[bool]):
    """Handler for DeleteTaskCommand."""
    
    def __init__(self, repository: TaskRepository):
        self.repo = repository
        self.event_bus = get_event_bus()
    
    async def handle(self, command: DeleteTaskCommand) -> bool:
        """Delete task and publish event."""
        # Get task first to verify ownership
        task = await self.repo.get_by_id(command.task_id)
        if not task:
            return False
        
        # Check ownership
        if task.tenant_id != command.tenant_id:
            raise ValueError("Access denied")
        
        # Delete task
        deleted = await self.repo.delete(command.task_id)
        
        if deleted:
            # Publish event
            event = TaskDeletedEvent(
                aggregate_id=command.task_id,
                tenant_id=command.tenant_id,
                user_id=command.user_id,
                payload={"title": task.title}
            )
            
            await self.event_bus.publish(event)
        
        return deleted
```

### Query Models

Create `app/cqrs/queries.py`:

```python
"""
CQRS Queries (Read operations).
"""
from abc import ABC, abstractmethod
from typing import Generic, TypeVar, Optional, List
from pydantic import BaseModel


TResult = TypeVar('TResult')


class Query(BaseModel, ABC):
    """
    Base query.
    
    Queries represent read operations.
    """
    pass


class ListTasksQuery(Query):
    """Query to list tasks."""
    tenant_id: str
    user_id: Optional[str] = None
    status: Optional[str] = None
    priority: Optional[str] = None
    tag: Optional[str] = None
    page: int = 1
    page_size: int = 20


class GetTaskQuery(Query):
    """Query to get single task."""
    task_id: str
    tenant_id: str


class GetTaskStatisticsQuery(Query):
    """Query to get task statistics."""
    tenant_id: str
    user_id: Optional[str] = None


class SearchTasksQuery(Query):
    """Query to search tasks."""
    query: str
    tenant_id: str
    limit: int = 20


class QueryHandler(ABC, Generic[TResult]):
    """
    Base query handler.
    
    Handlers retrieve data from read models.
    """
    
    @abstractmethod
    async def handle(self, query: Query) -> TResult:
        """
        Handle query.
        
        Args:
            query: Query to execute
        
        Returns:
            Query result
        """
        pass
```

### Query Handlers with Read Models

Create `app/cqrs/query_handlers.py`:

```python
"""
Query handlers implementation.
"""
from typing import List, Optional

from app.cqrs.queries import (
    ListTasksQuery,
    GetTaskQuery,
    GetTaskStatisticsQuery,
    SearchTasksQuery,
    QueryHandler
)
from app.models.task import Task, TaskStatsResponse
from app.repositories.task_repository import TaskRepository


class ListTasksQueryHandler(QueryHandler[List[Task]]):
    """Handler for ListTasksQuery."""
    
    def __init__(self, repository: TaskRepository, cache):
        self.repo = repository
        self.cache = cache
    
    async def handle(self, query: ListTasksQuery) -> List[Task]:
        """List tasks with caching."""
        # Build cache key
        cache_key = (
            f"tasks:{query.tenant_id}:"
            f"{query.status}:{query.priority}:"
            f"{query.page}:{query.page_size}"
        )
        
        # Try cache first
        cached = await self.cache.get(cache_key)
        if cached:
            return [Task(**t) for t in cached]
        
        # Query database
        tasks = await self.repo.list(
            status=query.status,
            priority=query.priority,
            tag=query.tag,
            skip=(query.page - 1) * query.page_size,
            limit=query.page_size
        )
        
        # Cache results
        await self.cache.set(
            cache_key,
            [t.model_dump() for t in tasks],
            ttl=300  # 5 minutes
        )
        
        return tasks


class GetTaskQueryHandler(QueryHandler[Optional[Task]]):
    """Handler for GetTaskQuery."""
    
    def __init__(self, repository: TaskRepository, cache):
        self.repo = repository
        self.cache = cache
    
    async def handle(self, query: GetTaskQuery) -> Optional[Task]:
        """Get single task with caching."""
        # Try cache first
        cache_key = f"task:{query.task_id}"
        cached = await self.cache.get(cache_key)
        
        if cached:
            return Task(**cached)
        
        # Query database
        task = await self.repo.get_by_id(query.task_id)
        
        if task:
            # Verify tenant
            if task.tenant_id != query.tenant_id:
                return None
            
            # Cache result
            await self.cache.set(
                cache_key,
                task.model_dump(),
                ttl=300
            )
        
        return task


class GetTaskStatisticsQueryHandler(QueryHandler[TaskStatsResponse]):
    """Handler for GetTaskStatisticsQuery."""
    
    def __init__(self, repository: TaskRepository, cache):
        self.repo = repository
        self.cache = cache
    
    async def handle(self, query: GetTaskStatisticsQuery) -> TaskStatsResponse:
        """Get statistics with aggressive caching."""
        # Try cache
        cache_key = f"stats:{query.tenant_id}"
        if query.user_id:
            cache_key += f":{query.user_id}"
        
        cached = await self.cache.get(cache_key)
        if cached:
            return TaskStatsResponse(**cached)
        
        # Calculate statistics
        total = await self.repo.count()
        completed = await self.repo.count(completed=True)
        
        stats = TaskStatsResponse(
            total=total,
            completed=completed,
            pending=total - completed,
            overdue=0,  # Calculate if needed
            by_priority={},
            by_status={}
        )
        
        # Cache for longer (stats change less frequently)
        await self.cache.set(
            cache_key,
            stats.model_dump(),
            ttl=600  # 10 minutes
        )
        
        return stats
```

### Command/Query Bus

Create `app/cqrs/bus.py`:

```python
"""
Command and Query bus for CQRS.
"""
from typing import Dict, Type, TypeVar
from app.cqrs.commands import Command, CommandHandler
from app.cqrs.queries import Query, QueryHandler


TCommand = TypeVar('TCommand', bound=Command)
TQuery = TypeVar('TQuery', bound=Query)


class CommandBus:
    """
    Command bus for dispatching commands to handlers.
    
    Example:
        bus = CommandBus()
        bus.register(CreateTaskCommand, CreateTaskCommandHandler(repo))
        
        result = await bus.dispatch(CreateTaskCommand(...))
    """
    
    def __init__(self):
        self._handlers: Dict[Type[Command], CommandHandler] = {}
    
    def register(
        self,
        command_type: Type[TCommand],
        handler: CommandHandler
    ):
        """Register command handler."""
        self._handlers[command_type] = handler
    
    async def dispatch(self, command: Command):
        """
        Dispatch command to appropriate handler.
        
        Args:
            command: Command to execute
        
        Returns:
            Command result
        """
        handler = self._handlers.get(type(command))
        
        if not handler:
            raise ValueError(f"No handler registered for {type(command)}")
        
        return await handler.handle(command)


class QueryBus:
    """
    Query bus for dispatching queries to handlers.
    
    Example:
        bus = QueryBus()
        bus.register(ListTasksQuery, ListTasksQueryHandler(repo, cache))
        
        result = await bus.dispatch(ListTasksQuery(...))
    """
    
    def __init__(self):
        self._handlers: Dict[Type[Query], QueryHandler] = {}
    
    def register(
        self,
        query_type: Type[TQuery],
        handler: QueryHandler
    ):
        """Register query handler."""
        self._handlers[query_type] = handler
    
    async def dispatch(self, query: Query):
        """
        Dispatch query to appropriate handler.
        
        Args:
            query: Query to execute
        
        Returns:
            Query result
        """
        handler = self._handlers.get(type(query))
        
        if not handler:
            raise ValueError(f"No handler registered for {type(query)}")
        
        return await handler.handle(query)
```

### Using CQRS in Endpoints

```python
from app.cqrs.bus import CommandBus, QueryBus
from app.cqrs.commands import CreateTaskCommand, CompleteTaskCommand
from app.cqrs.queries import ListTasksQuery, GetTaskQuery

# Initialize buses
command_bus = CommandBus()
query_bus = QueryBus()

# Register handlers (in app initialization)
command_bus.register(CreateTaskCommand, CreateTaskCommandHandler(repo))
command_bus.register(CompleteTaskCommand, CompleteTaskCommandHandler(repo))

query_bus.register(ListTasksQuery, ListTasksQueryHandler(repo, cache))
query_bus.register(GetTaskQuery, GetTaskQueryHandler(repo, cache))


# In endpoints
@app.endpoint("/tasks", methods=["POST"])
@require_auth
async def create_task(ctx: RequestContext, data: CreateTaskRequest):
    """Create task using CQRS command."""
    # Create command
    command = CreateTaskCommand(
        title=data.title,
        description=data.description,
        priority=data.priority.value,
        due_date=data.due_date.isoformat() if data.due_date else None,
        tags=data.tags,
        user_id=ctx.user_id,
        tenant_id=ctx.tenant_id
    )
    
    # Dispatch command
    task = await command_bus.dispatch(command)
    
    return {
        "message": "Task created",
        "task": task.model_dump()
    }


@app.endpoint("/tasks", methods=["GET"])
@require_auth
async def list_tasks(ctx: RequestContext):
    """List tasks using CQRS query."""
    # Create query
    query = ListTasksQuery(
        tenant_id=ctx.tenant_id,
        user_id=ctx.user_id,
        status=ctx.query.get("status"),
        priority=ctx.query.get("priority"),
        page=int(ctx.query.get("page", 1)),
        page_size=int(ctx.query.get("page_size", 20))
    )
    
    # Dispatch query
    tasks = await query_bus.dispatch(query)
    
    return {
        "tasks": [t.model_dump() for t in tasks]
    }
```

[↑ Back to TOC](#-table-of-contents)

---

## 🔗 Microservices Communication

### Service-to-Service Communication Patterns

```mermaid
graph TB
    subgraph SYNC ["⚡    Synchronous    Communication"]
        HTTP[🌐 HTTP/REST<br/>Request-Response]
        GRPC[⚡ gRPC<br/>High performance]
        GQL[🔮 GraphQL<br/>Federation]
    end

    subgraph ASYNC ["📨    Asynchronous    Communication"]
        MQ[📬 Message Queue<br/>RabbitMQ/SQS]
        PS[📢 Pub/Sub<br/>Redis/Kafka]
        ES[📦 Event Streaming<br/>Event sourcing]
    end

    subgraph PATTERNS ["🎯    Communication    Patterns"]
        RR[🔄 Request-Reply<br/>Direct response]
        SA[🔥 Scatter-Gather<br/>Fan-out/Fan-in]
        SAGA[📖 Saga Pattern<br/>Distributed transactions]
        CB[🔌 Circuit Breaker<br/>Fault tolerance]
    end

    subgraph DISCOVERY ["🔍    Service    Discovery"]
        DNS[🌐 DNS<br/>Service names]
        CONSUL[📋 Consul<br/>Service registry]
        K8S[☸️ Kubernetes<br/>Service mesh]
    end

    %% Styling
    style SYNC fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style ASYNC fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style PATTERNS fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style DISCOVERY fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
```

### HTTP Client for Service Communication

Create `app/services/http_client.py`:

```python
"""
HTTP client for inter-service communication.
"""
import httpx
from typing import Optional, Dict, Any
from tenacity import (
    retry,
    stop_after_attempt,
    wait_exponential,
    retry_if_exception_type
)


class ServiceClient:
    """
    HTTP client for calling other microservices.
    
    Features:
    - Automatic retries
    - Circuit breaker
    - Request tracing
    - Timeout handling
    """
    
    def __init__(
        self,
        base_url: str,
        timeout: int = 30,
        max_retries: int = 3
    ):
        self.base_url = base_url
        self.timeout = timeout
        self.max_retries = max_retries
        
        # Create persistent client
        self.client = httpx.AsyncClient(
            base_url=base_url,
            timeout=timeout,
            headers={
                "User-Agent": "TaskService/1.0"
            }
        )
    
    @retry(
        stop=stop_after_attempt(3),
        wait=wait_exponential(multiplier=1, min=1, max=10),
        retry=retry_if_exception_type(httpx.RequestError)
    )
    async def get(
        self,
        path: str,
        params: Optional[Dict] = None,
        headers: Optional[Dict] = None
    ) -> Dict[str, Any]:
        """
        GET request with retries.
        
        Args:
            path: API path
            params: Query parameters
            headers: Additional headers
        
        Returns:
            Response JSON
        """
        response = await self.client.get(
            path,
            params=params,
            headers=headers or {}
        )
        
        response.raise_for_status()
        return response.json()
    
    @retry(
        stop=stop_after_attempt(3),
        wait=wait_exponential(multiplier=1, min=1, max=10)
    )
    async def post(
        self,
        path: str,
        json: Optional[Dict] = None,
        headers: Optional[Dict] = None
    ) -> Dict[str, Any]:
        """
        POST request with retries.
        
        Args:
            path: API path
            json: Request body
            headers: Additional headers
        
        Returns:
            Response JSON
        """
        response = await self.client.post(
            path,
            json=json,
            headers=headers or {}
        )
        
        response.raise_for_status()
        return response.json()
    
    async def close(self):
        """Close client connection."""
        await self.client.aclose()


# Service clients
class UserServiceClient(ServiceClient):
    """Client for User Service."""
    
    def __init__(self):
        super().__init__(base_url="http://user-service:8001")
    
    async def get_user(self, user_id: str) -> Dict:
        """Get user by ID."""
        return await self.get(f"/users/{user_id}")
    
    async def verify_user_exists(self, user_id: str) -> bool:
        """Check if user exists."""
        try:
            await self.get(f"/users/{user_id}")
            return True
        except httpx.HTTPStatusError as e:
            if e.response.status_code == 404:
                return False
            raise


class NotificationServiceClient(ServiceClient):
    """Client for Notification Service."""
    
    def __init__(self):
        super().__init__(base_url="http://notification-service:8002")
    
    async def send_notification(
        self,
        user_id: str,
        message: str,
        notification_type: str = "info"
    ):
        """Send notification to user."""
        return await self.post(
            "/notifications",
            json={
                "user_id": user_id,
                "message": message,
                "type": notification_type
            }
        )


class AnalyticsServiceClient(ServiceClient):
    """Client for Analytics Service."""
    
    def __init__(self):
        super().__init__(base_url="http://analytics-service:8003")
    
    async def track_event(
        self,
        event_type: str,
        user_id: str,
        properties: Dict
    ):
        """Track analytics event."""
        return await self.post(
            "/events",
            json={
                "event_type": event_type,
                "user_id": user_id,
                "properties": properties
            }
        )
```

### gRPC Communication

```bash
# Install gRPC dependencies
pip install grpcio grpcio-tools
```

Create `protos/task_service.proto`:

```protobuf
syntax = "proto3";

package taskservice;

service TaskService {
    rpc GetTask(GetTaskRequest) returns (Task);
    rpc CreateTask(CreateTaskRequest) returns (Task);
    rpc ListTasks(ListTasksRequest) returns (TaskList);
}

message Task {
    string id = 1;
    string title = 2;
    string description = 3;
    string priority = 4;
    string status = 5;
    bool completed = 6;
}

message GetTaskRequest {
    string task_id = 1;
    string tenant_id = 2;
}

message CreateTaskRequest {
    string title = 1;
    string description = 2;
    string priority = 3;
    string tenant_id = 4;
}

message ListTasksRequest {
    string tenant_id = 1;
    int32 page = 2;
    int32 page_size = 3;
}

message TaskList {
    repeated Task tasks = 1;
    int32 total = 2;
}
```

Generate Python code:

```bash
python -m grpc_tools.protoc \
    -I./protos \
    --python_out=./app/grpc \
    --grpc_python_out=./app/grpc \
    ./protos/task_service.proto
```

### Saga Pattern for Distributed Transactions

Create `app/patterns/saga.py`:

```python
"""
Saga pattern for distributed transactions.
"""
from abc import ABC, abstractmethod
from typing import List, Optional
from enum import Enum


class SagaStatus(str, Enum):
    """Saga execution status."""
    PENDING = "pending"
    IN_PROGRESS = "in_progress"
    COMPLETED = "completed"
    FAILED = "failed"
    COMPENSATING = "compensating"
    COMPENSATED = "compensated"


class SagaStep(ABC):
    """
    Single step in a saga.
    
    Each step must implement:
    - execute: Forward transaction
    - compensate: Rollback transaction
    """
    
    @abstractmethod
    async def execute(self, context: dict) -> dict:
        """
        Execute step.
        
        Args:
            context: Saga context
        
        Returns:
            Updated context
        """
        pass
    
    @abstractmethod
    async def compensate(self, context: dict):
        """
        Compensate (rollback) step.
        
        Args:
            context: Saga context
        """
        pass


class Saga:
    """
    Saga orchestrator.
    
    Coordinates distributed transaction across services.
    
    Example:
        saga = Saga("create_order")
        saga.add_step(ReserveInventoryStep())
        saga.add_step(ChargePaymentStep())
        saga.add_step(CreateShipmentStep())
        
        result = await saga.execute({"order_id": "123"})
    """
    
    def __init__(self, name: str):
        self.name = name
        self.steps: List[SagaStep] = []
        self.status = SagaStatus.PENDING
        self.executed_steps: List[SagaStep] = []
    
    def add_step(self, step: SagaStep):
        """Add step to saga."""
        self.steps.append(step)
    
    async def execute(self, initial_context: dict) -> dict:
        """
        Execute saga.
        
        Args:
            initial_context: Initial context
        
        Returns:
            Final context
        """
        self.status = SagaStatus.IN_PROGRESS
        context = initial_context.copy()
        
        try:
            # Execute each step
            for step in self.steps:
                context = await step.execute(context)
                self.executed_steps.append(step)
            
            self.status = SagaStatus.COMPLETED
            return context
        
        except Exception as e:
            # Compensation phase
            self.status = SagaStatus.COMPENSATING
            await self._compensate(context)
            self.status = SagaStatus.COMPENSATED
            raise SagaFailure(
                f"Saga '{self.name}' failed: {e}",
                context=context
            )
    
    async def _compensate(self, context: dict):
        """Execute compensation (rollback) for executed steps."""
        # Compensate in reverse order
        for step in reversed(self.executed_steps):
            try:
                await step.compensate(context)
            except Exception as e:
                # Log compensation failure but continue
                print(f"Compensation failed for {step}: {e}")


class SagaFailure(Exception):
    """Saga execution failure."""
    
    def __init__(self, message: str, context: dict):
        super().__init__(message)
        self.context = context


# Example: Order Creation Saga
class ReserveInventoryStep(SagaStep):
    """Reserve inventory for order."""
    
    async def execute(self, context: dict) -> dict:
        order_id = context["order_id"]
        items = context["items"]
        
        # Call inventory service
        inventory_client = InventoryServiceClient()
        reservation = await inventory_client.reserve_items(items)
        
        context["reservation_id"] = reservation["id"]
        return context
    
    async def compensate(self, context: dict):
        """Release inventory reservation."""
        if "reservation_id" in context:
            inventory_client = InventoryServiceClient()
            await inventory_client.cancel_reservation(
                context["reservation_id"]
            )


class ChargePaymentStep(SagaStep):
    """Charge payment for order."""
    
    async def execute(self, context: dict) -> dict:
        user_id = context["user_id"]
        amount = context["amount"]
        
        # Call payment service
        payment_client = PaymentServiceClient()
        charge = await payment_client.charge(user_id, amount)
        
        context["charge_id"] = charge["id"]
        return context
    
    async def compensate(self, context: dict):
        """Refund payment."""
        if "charge_id" in context:
            payment_client = PaymentServiceClient()
            await payment_client.refund(context["charge_id"])


class CreateShipmentStep(SagaStep):
    """Create shipment for order."""
    
    async def execute(self, context: dict) -> dict:
        order_id = context["order_id"]
        address = context["shipping_address"]
        
        # Call shipping service
        shipping_client = ShippingServiceClient()
        shipment = await shipping_client.create_shipment(
            order_id,
            address
        )
        
        context["shipment_id"] = shipment["id"]
        return context
    
    async def compensate(self, context: dict):
        """Cancel shipment."""
        if "shipment_id" in context:
            shipping_client = ShippingServiceClient()
            await shipping_client.cancel_shipment(
                context["shipment_id"]
            )
```

[↑ Back to TOC](#-table-of-contents)

---

## 💾 Advanced Caching Strategies

### Multi-Level Caching Architecture

```mermaid
graph TB
    subgraph REQUEST ["📥    Request"]
        REQ[🌐 HTTP Request<br/>GET /tasks/123]
    end

    subgraph L1_CACHE ["⚡    L1    Cache    (In-Memory)"]
        L1[🧠 Application Cache<br/>LRU Cache 1000 items]
    end

    subgraph L2_CACHE ["🔴    L2    Cache    (Redis)"]
        L2[🔴 Redis<br/>Distributed cache]
    end

    subgraph L3_CACHE ["💾    L3    Cache    (Read Replica)"]
        L3[(🐘 PostgreSQL<br/>Read replica)]
    end

    subgraph PRIMARY ["💿    Primary    Database"]
        PRIMARY_DB[(🐘 PostgreSQL<br/>Primary)]
    end

    %% Request flow
    REQ --> L1
    
    L1 -->|Cache Hit| RETURN1[✅ Return from L1<br/>~1ms]
    L1 -->|Cache Miss| L2
    
    L2 -->|Cache Hit| RETURN2[✅ Return from L2<br/>~5ms]
    L2 -->|Cache Miss| L3
    
    L3 -->|Data Found| RETURN3[✅ Return from L3<br/>~20ms]
    L3 -->|Data Not Found| PRIMARY_DB
    
    PRIMARY_DB --> RETURN4[✅ Return from Primary<br/>~50ms]

    %% Update L1 and L2 on cache miss
    L2 -.->|Update| L1
    L3 -.->|Update| L2
    PRIMARY_DB -.->|Update| L2

    linkStyle 0,1,2,3,4,5,6 stroke:#1976d2,stroke-width:3px
    linkStyle 7,8,9 stroke:#388e3c,stroke-width:2px

    style REQUEST fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style L1_CACHE fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style L2_CACHE fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style L3_CACHE fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style PRIMARY fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    
    style RETURN1 fill:#c8e6c9,stroke:#388e3c,stroke-width:2px,color:#000
    style RETURN2 fill:#c8e6c9,stroke:#388e3c,stroke-width:2px,color:#000
    style RETURN3 fill:#fff9c4,stroke:#f57c00,stroke-width:2px,color:#000
    style RETURN4 fill:#ffcdd2,stroke:#c2185b,stroke-width:2px,color:#000
```

### Multi-Level Cache Implementation

Create `app/cache/multi_level_cache.py`:

```python
"""
Multi-level caching system.
"""
from typing import Optional, Any
from functools import lru_cache
import json
import hashlib


class CacheKey:
    """Generate consistent cache keys."""
    
    @staticmethod
    def generate(prefix: str, *args, **kwargs) -> str:
        """
        Generate cache key from arguments.
        
        Args:
            prefix: Key prefix
            args: Positional arguments
            kwargs: Keyword arguments
        
        Returns:
            Cache key string
        """
        # Combine all arguments
        key_parts = [prefix] + [str(arg) for arg in args]
        
        # Add sorted kwargs
        for k, v in sorted(kwargs.items()):
            key_parts.append(f"{k}={v}")
        
        # Join with colons
        key_str = ":".join(key_parts)
        
        # Hash if too long
        if len(key_str) > 200:
            hash_value = hashlib.sha256(key_str.encode()).hexdigest()[:16]
            return f"{prefix}:{hash_value}"
        
        return key_str


class MultiLevelCache:
    """
    Multi-level caching system.
    
    L1: In-memory LRU cache (fast, limited size)
    L2: Redis (distributed, persistent)
    L3: Database read replica
    
    Example:
        cache = MultiLevelCache(redis_client, db_session)
        
        # Get with automatic fallback
        value = await cache.get("task:123")
        
        # Set across all levels
        await cache.set("task:123", task_data, ttl=300)
    """
    
    def __init__(self, redis_client, db_session=None):
        self.redis = redis_client
        self.db = db_session
        
        # L1 cache (in-memory)
        self._l1_cache = {}
        self._l1_max_size = 1000
    
    async def get(self, key: str) -> Optional[Any]:
        """
        Get value from cache with fallback.
        
        Tries L1 -> L2 -> L3 in order.
        
        Args:
            key: Cache key
        
        Returns:
            Cached value or None
        """
        # Try L1 (in-memory)
        if key in self._l1_cache:
            return self._l1_cache[key]
        
        # Try L2 (Redis)
        redis_value = await self.redis.get(key)
        if redis_value:
            # Deserialize
            value = json.loads(redis_value)
            
            # Update L1
            self._set_l1(key, value)
            
            return value
        
        # L3 would be read replica query (application-specific)
        return None
    
    async def set(
        self,
        key: str,
        value: Any,
        ttl: Optional[int] = None
    ):
        """
        Set value in all cache levels.
        
        Args:
            key: Cache key
            value: Value to cache
            ttl: Time to live in seconds
        """
        # Set L1
        self._set_l1(key, value)
        
        # Set L2 (Redis)
        serialized = json.dumps(value)
        if ttl:
            await self.redis.setex(key, ttl, serialized)
        else:
            await self.redis.set(key, serialized)
    
    async def delete(self, key: str):
        """
        Delete from all cache levels.
        
        Args:
            key: Cache key
        """
        # Delete from L1
        self._l1_cache.pop(key, None)
        
        # Delete from L2
        await self.redis.delete(key)
    
    async def invalidate_pattern(self, pattern: str):
        """
        Invalidate all keys matching pattern.
        
        Args:
            pattern: Key pattern (e.g., "tasks:*")
        """
        # Clear matching keys from L1
        keys_to_delete = [
            k for k in self._l1_cache.keys()
            if self._matches_pattern(k, pattern)
        ]
        
        for key in keys_to_delete:
            del self._l1_cache[key]
        
        # Clear from Redis
        keys = await self.redis.keys(pattern)
        if keys:
            await self.redis.delete(*keys)
    
    def _set_l1(self, key: str, value: Any):
        """Set value in L1 cache with LRU eviction."""
        # Check size limit
        if len(self._l1_cache) >= self._l1_max_size:
            # Remove oldest item (simple LRU)
            oldest_key = next(iter(self._l1_cache))
            del self._l1_cache[oldest_key]
        
        self._l1_cache[key] = value
    
    def _matches_pattern(self, key: str, pattern: str) -> bool:
        """Check if key matches pattern."""
        import re
        regex_pattern = pattern.replace("*", ".*")
        return re.match(regex_pattern, key) is not None


# Cache decorators
def cached(
    ttl: int = 300,
    key_prefix: str = "cache"
):
    """
    Decorator for caching function results.
    
    Example:
        @cached(ttl=300, key_prefix="tasks")
        async def get_task(task_id: str):
            return await db.query(...)
    """
    def decorator(func):
        async def wrapper(*args, **kwargs):
            # Get cache from context or global
            cache = kwargs.pop("_cache", None)
            if not cache:
                return await func(*args, **kwargs)
            
            # Generate cache key
            key = CacheKey.generate(key_prefix, *args, **kwargs)
            
            # Try cache
            cached_value = await cache.get(key)
            if cached_value is not None:
                return cached_value
            
            # Execute function
            result = await func(*args, **kwargs)
            
            # Cache result
            await cache.set(key, result, ttl=ttl)
            
            return result
        
        return wrapper
    
    return decorator
```

### Cache Warming Strategy

Create `app/cache/cache_warmer.py`:

```python
"""
Cache warming to pre-populate cache.
"""
from typing import List
from datetime import datetime, timedelta


class CacheWarmer:
    """
    Pre-populate cache with frequently accessed data.
    
    Run on startup or periodically.
    """
    
    def __init__(self, cache, repository):
        self.cache = cache
        self.repo = repository
    
    async def warm_popular_tasks(self, limit: int = 100):
        """
        Pre-load most accessed tasks.
        
        Args:
            limit: Number of tasks to pre-load
        """
        # Get most popular tasks (by view count, recent, etc.)
        tasks = await self.repo.list(limit=limit)
        
        # Cache each task
        for task in tasks:
            cache_key = f"task:{task.id}"
            await self.cache.set(
                cache_key,
                task.model_dump(),
                ttl=600  # 10 minutes
            )
        
        print(f"✓ Warmed cache with {len(tasks)} tasks")
    
    async def warm_statistics(self, tenant_ids: List[str]):
        """
        Pre-calculate and cache statistics.
        
        Args:
            tenant_ids: List of tenant IDs
        """
        for tenant_id in tenant_ids:
            # Calculate stats
            stats = await self._calculate_stats(tenant_id)
            
            # Cache for longer (stats change less)
            cache_key = f"stats:{tenant_id}"
            await self.cache.set(
                cache_key,
                stats,
                ttl=1800  # 30 minutes
            )
        
        print(f"✓ Warmed statistics for {len(tenant_ids)} tenants")
    
    async def _calculate_stats(self, tenant_id: str) -> dict:
        """Calculate statistics for tenant."""
        total = await self.repo.count()
        completed = await self.repo.count(completed=True)
        
        return {
            "total": total,
            "completed": completed,
            "pending": total - completed
        }
```

### Cache Invalidation Strategy

```python
# Invalidate cache on write operations

class TaskService:
    """Task service with cache invalidation."""
    
    def __init__(self, repository, cache):
        self.repo = repository
        self.cache = cache
    
    async def create_task(self, data: CreateTaskRequest) -> Task:
        """Create task and invalidate caches."""
        task = await self.repo.create(data)
        
        # Invalidate related caches
        await self._invalidate_caches(task.tenant_id)
        
        return task
    
    async def update_task(
        self,
        task_id: str,
        data: UpdateTaskRequest
    ) -> Task:
        """Update task and invalidate caches."""
        task = await self.repo.update(task_id, data)
        
        # Invalidate specific task cache
        await self.cache.delete(f"task:{task_id}")
        
        # Invalidate list caches
        await self._invalidate_caches(task.tenant_id)
        
        return task
    
    async def _invalidate_caches(self, tenant_id: str):
        """Invalidate all caches for tenant."""
        # Invalidate list caches
        await self.cache.invalidate_pattern(f"tasks:{tenant_id}:*")
        
        # Invalidate statistics
        await self.cache.delete(f"stats:{tenant_id}")
```

[↑ Back to TOC](#-table-of-contents)

---

## 🔌 Circuit Breakers & Resilience

### Circuit Breaker Pattern

```mermaid
graph TB
    subgraph STATES ["🔄    Circuit    Breaker    States"]
        CLOSED[✅ CLOSED<br/>Normal operation<br/>Requests pass through]
        OPEN[❌ OPEN<br/>Service failing<br/>Requests rejected]
        HALF[⚠️ HALF-OPEN<br/>Testing recovery<br/>Limited requests]
    end

    subgraph TRANSITIONS ["🔀    State    Transitions"]
        T1[🔴 Failure Threshold<br/>5 failures in 10s]
        T2[⏰ Timeout<br/>30 seconds]
        T3[✅ Success<br/>Request succeeds]
        T4[❌ Failure<br/>Request fails]
    end

    %% State transitions
    CLOSED -->|Failures exceed threshold| T1
    T1 --> OPEN
    
    OPEN -->|After timeout| T2
    T2 --> HALF
    
    HALF -->|Request succeeds| T3
    T3 --> CLOSED
    
    HALF -->|Request fails| T4
    T4 --> OPEN

    linkStyle 0,1,2,3,4,5,6,7 stroke:#1976d2,stroke-width:3px

    style STATES fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style TRANSITIONS fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    
    style CLOSED fill:#c8e6c9,stroke:#388e3c,stroke-width:3px,color:#000
    style OPEN fill:#ffcdd2,stroke:#c2185b,stroke-width:3px,color:#000
    style HALF fill:#fff9c4,stroke:#f57c00,stroke-width:3px,color:#000
```

### Circuit Breaker Implementation

Create `app/resilience/circuit_breaker.py`:

```python
"""
Circuit breaker pattern for service resilience.
"""
import time
from enum import Enum
from typing import Callable, TypeVar, Optional
from functools import wraps


T = TypeVar('T')


class CircuitState(str, Enum):
    """Circuit breaker states."""
    CLOSED = "closed"  # Normal operation
    OPEN = "open"  # Failing, reject requests
    HALF_OPEN = "half_open"  # Testing recovery


class CircuitBreakerError(Exception):
    """Circuit breaker is open."""
    pass


class CircuitBreaker:
    """
    Circuit breaker for service calls.
    
    Prevents cascading failures by stopping requests to failing services.
    
    Example:
        circuit_breaker = CircuitBreaker(
            failure_threshold=5,
            recovery_timeout=30,
            expected_exception=httpx.HTTPError
        )
        
        @circuit_breaker
        async def call_external_service():
            return await client.get("/api/data")
    """
    
    def __init__(
        self,
        failure_threshold: int = 5,
        recovery_timeout: int = 30,
        expected_exception: type = Exception,
        name: str = "circuit_breaker"
    ):
        """
        Initialize circuit breaker.
        
        Args:
            failure_threshold: Number of failures before opening
            recovery_timeout: Seconds before attempting recovery
            expected_exception: Exception type to catch
            name: Circuit breaker name
        """
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.expected_exception = expected_exception
        self.name = name
        
        # State
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.last_failure_time: Optional[float] = None
        self.success_count = 0
    
    def __call__(self, func: Callable) -> Callable:
        """Decorator to wrap function with circuit breaker."""
        
        @wraps(func)
        async def wrapper(*args, **kwargs):
            # Check if circuit is open
            if self.state == CircuitState.OPEN:
                if self._should_attempt_reset():
                    self.state = CircuitState.HALF_OPEN
                    self.success_count = 0
                else:
                    raise CircuitBreakerError(
                        f"Circuit breaker '{self.name}' is OPEN"
                    )
            
            try:
                # Execute function
                result = await func(*args, **kwargs)
                
                # Record success
                self._on_success()
                
                return result
            
            except self.expected_exception as e:
                # Record failure
                self._on_failure()
                raise
        
        return wrapper
    
    def _on_success(self):
        """Handle successful call."""
        if self.state == CircuitState.HALF_OPEN:
            self.success_count += 1
            
            # If enough successes, close circuit
            if self.success_count >= 3:
                self.state = CircuitState.CLOSED
                self.failure_count = 0
                print(f"✓ Circuit breaker '{self.name}' CLOSED")
        
        elif self.state == CircuitState.CLOSED:
            # Reset failure count on success
            self.failure_count = 0
    
    def _on_failure(self):
        """Handle failed call."""
        self.failure_count += 1
        self.last_failure_time = time.time()
        
        # Open circuit if threshold exceeded
        if self.failure_count >= self.failure_threshold:
            self.state = CircuitState.OPEN
            print(f"❌ Circuit breaker '{self.name}' OPEN")
    
    def _should_attempt_reset(self) -> bool:
        """Check if enough time has passed to attempt recovery."""
        if self.last_failure_time is None:
            return True
        
        return (time.time() - self.last_failure_time) >= self.recovery_timeout
    
    def reset(self):
        """Manually reset circuit breaker."""
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.success_count = 0
        self.last_failure_time = None


# Global circuit breakers for services
user_service_breaker = CircuitBreaker(
    failure_threshold=5,
    recovery_timeout=30,
    name="user_service"
)

notification_service_breaker = CircuitBreaker(
    failure_threshold=3,
    recovery_timeout=60,
    name="notification_service"
)


# Usage example
class UserServiceClient:
    """User service client with circuit breaker."""
    
    @user_service_breaker
    async def get_user(self, user_id: str):
        """Get user with circuit breaker protection."""
        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"http://user-service/users/{user_id}"
            )
            response.raise_for_status()
            return response.json()
```

### Bulkhead Pattern

Create `app/resilience/bulkhead.py`:

```python
"""
Bulkhead pattern for resource isolation.
"""
import asyncio
from typing import Callable, TypeVar


T = TypeVar('T')


class Bulkhead:
    """
    Bulkhead pattern implementation.
    
    Limits concurrent requests to prevent resource exhaustion.
    
    Example:
        bulkhead = Bulkhead(max_concurrent=10)
        
        @bulkhead
        async def call_service():
            return await client.get("/api/data")
    """
    
    def __init__(self, max_concurrent: int = 10):
        """
        Initialize bulkhead.
        
        Args:
            max_concurrent: Maximum concurrent requests
        """
        self.semaphore = asyncio.Semaphore(max_concurrent)
    
    def __call__(self, func: Callable) -> Callable:
        """Decorator to limit concurrency."""
        
        async def wrapper(*args, **kwargs):
            async with self.semaphore:
                return await func(*args, **kwargs)
        
        return wrapper


# Usage
database_bulkhead = Bulkhead(max_concurrent=20)
external_api_bulkhead = Bulkhead(max_concurrent=5)


@external_api_bulkhead
@user_service_breaker
async def call_user_service(user_id: str):
    """Protected with both bulkhead and circuit breaker."""
    # Only 5 concurrent calls allowed
    # Circuit breaker prevents cascading failures
    pass
```

### Retry Strategy with Exponential Backoff

```python
from tenacity import (
    retry,
    stop_after_attempt,
    wait_exponential,
    retry_if_exception_type
)


@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=1, max=10),
    retry=retry_if_exception_type(httpx.RequestError)
)
async def resilient_api_call():
    """
    API call with automatic retries.
    
    Retry strategy:
    - Attempt 1: Immediate
    - Attempt 2: Wait 1 second
    - Attempt 3: Wait 2 seconds
    """
    async with httpx.AsyncClient() as client:
        response = await client.get("http://api.example.com/data")
        response.raise_for_status()
        return response.json()
```

[↑ Back to TOC](#-table-of-contents)

---

## 📦 Database Sharding

### Sharding Strategy

```mermaid
graph TB
    subgraph APPLICATION ["📱    Application    Layer"]
        APP[🖥️ API Service<br/>Shard router]
    end

    subgraph SHARDING ["🔀    Sharding    Logic"]
        ROUTER[🎯 Shard Router<br/>tenant_id % 4]
        MAP[📋 Shard Mapping<br/>tenant -> shard]
    end

    subgraph SHARDS ["💾    Database    Shards"]
        SHARD0[(🗄️ Shard 0<br/>Tenants 0,4,8...)]
        SHARD1[(🗄️ Shard 1<br/>Tenants 1,5,9...)]
        SHARD2[(🗄️ Shard 2<br/>Tenants 2,6,10...)]
        SHARD3[(🗄️ Shard 3<br/>Tenants 3,7,11...)]
    end

    %% Flow
    APP --> ROUTER
    ROUTER --> MAP
    
    MAP -->|tenant_id % 4 = 0| SHARD0
    MAP -->|tenant_id % 4 = 1| SHARD1
    MAP -->|tenant_id % 4 = 2| SHARD2
    MAP -->|tenant_id % 4 = 3| SHARD3

    linkStyle 0,1,2,3,4,5 stroke:#1976d2,stroke-width:3px

    style APPLICATION fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style SHARDING fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style SHARDS fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
```

### Shard Router Implementation

Create `app/sharding/shard_router.py`:

```python
"""
Database sharding router.
"""
from typing import Dict, Optional
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker
import hashlib


class ShardRouter:
    """
    Route queries to appropriate database shard.
    
    Sharding strategy: Hash-based partitioning by tenant_id.
    
    Example:
        router = ShardRouter({
            0: "postgresql://host1/db",
            1: "postgresql://host2/db",
            2: "postgresql://host3/db",
            3: "postgresql://host4/db"
        })
        
        session = await router.get_session(tenant_id="tenant-123")
    """
    
    def __init__(self, shard_urls: Dict[int, str]):
        """
        Initialize shard router.
        
        Args:
            shard_urls: Map of shard ID to database URL
        """
        self.shard_count = len(shard_urls)
        self.engines = {}
        self.session_makers = {}
        
        # Create engine for each shard
        for shard_id, url in shard_urls.items():
            engine = create_async_engine(url, pool_pre_ping=True)
            self.engines[shard_id] = engine
            
            self.session_makers[shard_id] = sessionmaker(
                engine,
                class_=AsyncSession,
                expire_on_commit=False
            )
    
    def get_shard_id(self, tenant_id: str) -> int:
        """
        Determine shard ID for tenant.
        
        Uses consistent hashing for even distribution.
        
        Args:
            tenant_id: Tenant identifier
        
        Returns:
            Shard ID (0 to shard_count-1)
        """
        # Hash tenant ID
        hash_value = int(
            hashlib.md5(tenant_id.encode()).hexdigest(),
            16
        )
        
        # Modulo to get shard
        return hash_value % self.shard_count
    
    async def get_session(self, tenant_id: str) -> AsyncSession:
        """
        Get database session for tenant.
        
        Args:
            tenant_id: Tenant identifier
        
        Returns:
            Database session for tenant's shard
        """
        shard_id = self.get_shard_id(tenant_id)
        session_maker = self.session_makers[shard_id]
        
        return session_maker()
    
    async def close_all(self):
        """Close all shard connections."""
        for engine in self.engines.values():
            await engine.dispose()


# Global shard router
_shard_router: Optional[ShardRouter] = None


def init_sharding(shard_urls: Dict[int, str]):
    """Initialize global shard router."""
    global _shard_router
    _shard_router = ShardRouter(shard_urls)


def get_shard_router() -> ShardRouter:
    """Get global shard router."""
    if _shard_router is None:
        raise RuntimeError("Shard router not initialized")
    return _shard_router
```

### Using Sharded Database

```python
# Initialize sharding
init_sharding({
    0: "postgresql://shard0-host/taskdb",
    1: "postgresql://shard1-host/taskdb",
    2: "postgresql://shard2-host/taskdb",
    3: "postgresql://shard3-host/taskdb"
})


# In endpoints
@app.endpoint("/tasks", methods=["GET"])
@require_auth
async def list_tasks(ctx: RequestContext):
    """List tasks from correct shard."""
    router = get_shard_router()
    
    # Get session for tenant's shard
    async with router.get_session(ctx.tenant_id) as session:
        repo = TaskRepository(session)
        tasks = await repo.list()
    
    return {"tasks": [t.model_dump() for t in tasks]}
```

[↑ Back to TOC](#-table-of-contents)

---

## 🚀 Production Deployment Guide

### Final Production Checklist

| Category | Item | Status |
|----------|------|--------|
| **Security** | HTTPS only | ✅ |
| | JWT tokens with short expiry | ✅ |
| | Rate limiting enabled | ✅ |
| | Security headers configured | ✅ |
| | Secrets in vault | ✅ |
| **Performance** | Database indexes created | ✅ |
| | Redis caching enabled | ✅ |
| | CDN for static assets | ✅ |
| | Connection pooling | ✅ |
| **Reliability** | Circuit breakers configured | ✅ |
| | Health checks enabled | ✅ |
| | Auto-scaling configured | ✅ |
| | Database backups automated | ✅ |
| **Monitoring** | Prometheus metrics | ✅ |
| | Grafana dashboards | ✅ |
| | Error tracking (Sentry) | ✅ |
| | Audit logging enabled | ✅ |
| **Compliance** | GDPR data export | ✅ |
| | Data retention policy | ✅ |
| | Audit trail complete | ✅ |

### Production Environment Variables

```bash
# Production .env
NODE_ENV=production
DEBUG=false

# Application
APP_NAME=TaskAPI
APP_VERSION=1.0.0
HOST=0.0.0.0
PORT=8000
WORKERS=4

# Database (use connection pooler like PgBouncer)
DATABASE_URL=postgresql+asyncpg://user:pass@pgbouncer:6432/taskdb
DATABASE_POOL_SIZE=20
DATABASE_MAX_OVERFLOW=10

# Redis Cluster
REDIS_URL=redis://redis-cluster:6379/0
REDIS_MAX_CONNECTIONS=50

# Message Queue
RABBITMQ_URL=amqp://user:pass@rabbitmq:5672/
CELERY_BROKER_URL=redis://redis:6379/1
CELERY_RESULT_BACKEND=redis://redis:6379/2

# Security
SECRET_KEY=${SECRET_KEY}  # From secrets manager
JWT_SECRET=${JWT_SECRET}
ENCRYPTION_KEY=${ENCRYPTION_KEY}
ALLOWED_ORIGINS=https://app.example.com,https://www.example.com

# Monitoring
SENTRY_DSN=${SENTRY_DSN}
LOG_LEVEL=INFO
PROMETHEUS_PORT=9090

# External Services
AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
AWS_REGION=us-east-1
S3_BUCKET=taskapi-production

# Email
SENDGRID_API_KEY=${SENDGRID_API_KEY}
FROM_EMAIL=noreply@example.com

# Feature Flags
ENABLE_ANALYTICS=true
ENABLE_NOTIFICATIONS=true
ENABLE_WEBSOCKETS=true
```

### Kubernetes Production Deployment

Create `k8s/production/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: taskapi
  namespace: production
  labels:
    app: taskapi
    version: v1.0.0
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: taskapi
  template:
    metadata:
      labels:
        app: taskapi
        version: v1.0.0
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9090"
        prometheus.io/path: "/metrics"
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - taskapi
              topologyKey: kubernetes.io/hostname
      
      containers:
      - name: api
        image: yourusername/taskapi:v1.0.0
        imagePullPolicy: Always
        
        ports:
        - containerPort: 8000
          name: http
          protocol: TCP
        - containerPort: 9090
          name: metrics
          protocol: TCP
        
        env:
        - name: NODE_ENV
          value: "production"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: taskapi-secrets
              key: database-url
        - name: REDIS_URL
          valueFrom:
            configMapKeyRef:
              name: taskapi-config
              key: redis-url
        
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        
        readinessProbe:
          httpGet:
            path: /health/ready
            port: 8000
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 2
        
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 10"]
---
apiVersion: v1
kind: Service
metadata:
  name: taskapi
  namespace: production
spec:
  type: ClusterIP
  selector:
    app: taskapi
  ports:
  - port: 80
    targetPort: 8000
    protocol: TCP
    name: http
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: taskapi-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: taskapi
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

[↑ Back to TOC](#-table-of-contents)

---

## 🎓 Course Completion & Next Steps

### 🎉 Congratulations!

You've completed the **Python API Starter Complete Implementation Guide**! You've built a production-ready, enterprise-grade API with:

### ✅ What You've Mastered

#### **Core Framework (Section 1-2)**
- ✅ FastAPI-style framework architecture
- ✅ Request/response handling
- ✅ Middleware system
- ✅ Plugin architecture
- ✅ Error handling

#### **RESTful API Design (Section 3)**
- ✅ Resource-based endpoints
- ✅ HTTP methods & status codes
- ✅ Pagination & filtering
- ✅ API versioning
- ✅ Content negotiation

#### **Advanced Features (Section 4)**
- ✅ GraphQL integration
- ✅ gRPC support
- ✅ File uploads/downloads
- ✅ Streaming responses
- ✅ Server-Sent Events

#### **Observability (Section 5)**
- ✅ Prometheus metrics
- ✅ Distributed tracing
- ✅ Structured logging
- ✅ Health checks
- ✅ Performance profiling

#### **Production Features (Section 6)**
- ✅ Database integration (PostgreSQL, MongoDB)
- ✅ WebSocket support
- ✅ Background tasks (Celery)
- ✅ Testing strategies
- ✅ Docker deployment
- ✅ Kubernetes orchestration
- ✅ CI/CD pipelines

#### **Security & Compliance (Section 7)**
- ✅ OAuth2 & JWT authentication
- ✅ RBAC authorization
- ✅ API key management
- ✅ Rate limiting
- ✅ Data encryption
- ✅ Audit logging
- ✅ GDPR compliance

#### **Scalability (Section 8)**
- ✅ Multi-tenancy
- ✅ Event-driven architecture
- ✅ CQRS pattern
- ✅ Microservices communication
- ✅ Advanced caching
- ✅ Circuit breakers
- ✅ Database sharding
- ✅ High availability

### 📊 Final Architecture Overview

```mermaid
graph TB
    subgraph USERS ["👥 Users"]
        WEB[🌐 Web App]
        MOBILE[📱 Mobile App]
        API_CLIENT[🔧 API Client]
    end

    subgraph EDGE ["🌍 Edge Layer"]
        CDN[📡 CDN]
        WAF[🛡️ WAF]
        LB[⚖️ Load Balancer]
    end

    subgraph API ["🚀 API Layer"]
        GATEWAY[🚪 API Gateway]
        AUTH[🔐 Auth Service]
        TASKS[📋 Task Service]
        USERS_SVC[👤 User Service]
        NOTIFY[📧 Notification]
    end

    subgraph DATA ["💾 Data Layer"]
        PG[(🐘 PostgreSQL)]
        MONGO[(🍃 MongoDB)]
        REDIS[(🔴 Redis)]
        ES[(🔍 Elasticsearch)]
    end

    subgraph ASYNC ["⚡ Async Layer"]
        MQ[📬 Message Queue]
        CELERY[⚙️ Celery Workers]
        WS[🔌 WebSocket Server]
    end

    subgraph OBS ["📊 Observability"]
        PROM[📈 Prometheus]
        GRAF[📊 Grafana]
        JAEGER[🔍 Jaeger]
        SENTRY[🚨 Sentry]
    end

    WEB --> CDN
    MOBILE --> LB
    API_CLIENT --> LB
    CDN --> LB
    LB --> WAF
    WAF --> GATEWAY
    
    GATEWAY --> AUTH
    GATEWAY --> TASKS
    GATEWAY --> USERS_SVC
    GATEWAY --> NOTIFY
    
    TASKS --> PG
    TASKS --> REDIS
    USERS_SVC --> PG
    NOTIFY --> MQ
    
    MQ --> CELERY
    CELERY --> ES
    
    TASKS --> WS
    
    TASKS --> PROM
    PROM --> GRAF
    TASKS --> JAEGER
    TASKS --> SENTRY

    style USERS fill:#e8f4fd,stroke:#1976d2,stroke-width:2px
    style EDGE fill:#f8f0ff,stroke:#7b1fa2,stroke-width:2px
    style API fill:#f0fffe,stroke:#00695c,stroke-width:2px
    style DATA fill:#fff4e6,stroke:#f57c00,stroke-width:2px
    style ASYNC fill:#f0f8f0,stroke:#388e3c,stroke-width:2px
    style OBS fill:#e8eaf6,stroke:#3f51b5,stroke-width:2px
```

### 🚀 Next Steps & Resources

#### **1. Build Your Own Projects**
- 🎯 Start with a simple CRUD API
- 📦 Add features incrementally
- 🚢 Deploy to production
- 📊 Monitor and optimize

#### **2. Explore Advanced Topics**
- 📚 Service mesh (Istio, Linkerd)
- 🔄 Event sourcing patterns
- 🌊 Stream processing (Kafka)
- 🤖 ML model serving
- 📱 Mobile backend as a service

#### **3. Community & Learning**
- 💬 Join FastAPI Discord
- 📝 Read API design blogs
- 🎥 Watch conference talks
- 📖 Read "Designing Data-Intensive Applications"
- 🏆 Contribute to open source

#### **4. Certifications**
- ☁️ AWS Certified Solutions Architect
- ☁️ Google Cloud Professional Cloud Architect
- ☁️ Kubernetes (CKA/CKAD)
- 🔒 Certified Information Systems Security Professional (CISSP)

### 📚 Recommended Reading

| Book | Topic | Why Read It |
|------|-------|-------------|
| **Designing Data-Intensive Applications** | System Design | Best practices for scalable systems |
| **Building Microservices** | Architecture | Microservices patterns |
| **Site Reliability Engineering** | Operations | Google's SRE practices |
| **Clean Architecture** | Code Design | SOLID principles |
| **Database Internals** | Databases | Deep dive into DB engines |

### 🛠️ Production Deployment Commands

```bash
# Build production image
docker build -t taskapi:v1.0.0 .

# Push to registry
docker push yourusername/taskapi:v1.0.0

# Deploy to Kubernetes
kubectl apply -f k8s/production/

# Check deployment
kubectl get pods -n production
kubectl logs -f deployment/taskapi -n production

# Scale up
kubectl scale deployment taskapi --replicas=5 -n production

# Rolling update
kubectl set image deployment/taskapi \
  api=yourusername/taskapi:v1.1.0 \
  -n production

# Rollback if needed
kubectl rollout undo deployment/taskapi -n production
```

### 🎯 Performance Benchmarks

Target metrics for production:

| Metric | Target | Excellent |
|--------|--------|-----------|
| **Response Time (p50)** | < 100ms | < 50ms |
| **Response Time (p95)** | < 500ms | < 200ms |
| **Response Time (p99)** | < 1000ms | < 500ms |
| **Throughput** | > 1000 req/s | > 5000 req/s |
| **Error Rate** | < 0.1% | < 0.01% |
| **Availability** | > 99.9% | > 99.99% |
| **CPU Usage** | < 70% | < 50% |
| **Memory Usage** | < 80% | < 60% |

### 💡 Final Best Practices

1. **Always use type hints** - Makes code self-documenting
2. **Write tests first** - TDD leads to better design
3. **Monitor everything** - You can't improve what you don't measure
4. **Automate deployments** - Manual deployments lead to errors
5. **Document your APIs** - Good docs = happy developers
6. **Handle errors gracefully** - Never expose internal errors
7. **Optimize for readability** - Code is read more than written
8. **Security by default** - Never add security as an afterthought
9. **Scale horizontally** - Easier than vertical scaling
10. **Keep it simple** - Complexity is the enemy of reliability

### 🌟 You Are Now Ready To

- ✅ Build production-ready APIs
- ✅ Design scalable architectures
- ✅ Implement security best practices
- ✅ Deploy to cloud platforms
- ✅ Monitor and optimize performance
- ✅ Lead API development teams
- ✅ Pass system design interviews

### 🎊 Thank You!

You've completed one of the most comprehensive API development courses available. You now have the knowledge and tools to build world-class APIs that can scale to millions of users.

**Remember**: Great APIs are not built in a day. Start small, iterate fast, and always focus on the user experience.

### 📬 Stay Connected

- ⭐ Star the repository
- 🐛 Report issues on GitHub
- 💡 Share your implementations
- 🤝 Help others learn

**Happy Coding! 🚀**

---

[↑ Back to TOC](#-table-of-contents)

---

**🎓 Course Complete!** You've mastered advanced topics including multi-tenancy, event-driven architecture, CQRS, microservices, caching strategies, resilience patterns, and production deployment. You're now equipped to build and scale enterprise-grade APIs! 🎉✨
