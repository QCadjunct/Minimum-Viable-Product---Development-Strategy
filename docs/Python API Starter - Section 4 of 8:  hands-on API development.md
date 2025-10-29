# 🚀 Python API Starter - Complete Implementation Guide

**Section 4 of 8: Building Your First API**

---

## 📑 Table of Contents

- [🚀 Building Your First API](#-building-your-first-api)
  - [Project Planning & Design](#-project-planning--design)
  - [API Design Workflow](#-api-design-workflow)
  - [Creating the Project Structure](#-creating-the-project-structure)
  - [Defining Data Models](#-defining-data-models)
  - [Building the Service Layer](#-building-the-service-layer)
  - [Implementing CRUD Endpoints](#-implementing-crud-endpoints)
  - [Adding Input Validation](#-adding-input-validation)
  - [Implementing Error Handling](#-implementing-error-handling)
  - [Testing Your API](#-testing-your-api)
  - [Complete Example: Todo API](#-complete-example-todo-api)
  - [Advanced Features](#-advanced-features)
  - [Deployment Preparation](#-deployment-preparation)

---

## 📋 Project Planning & Design

### Choosing Your First Project

Before writing code, plan your API by answering these questions:

**1. What problem are you solving?**
```
✅ Good: "Users need to manage their daily tasks"
❌ Bad: "I want to make an API"
```

**2. Who are your users?**
```
✅ Good: "Web app frontend, mobile app, automation scripts"
❌ Bad: "Everyone"
```

**3. What are the core entities?**
```
✅ Good: "User, Task, Project, Comment"
❌ Bad: "Everything related to productivity"
```

**4. What operations are needed?**
```
✅ Good: "Create task, list tasks, mark complete, delete task"
❌ Bad: "Do everything with tasks"
```

### API Design Checklist

Before coding, ensure you have:

- [ ] **Clear purpose**: One-sentence description of what the API does
- [ ] **Defined entities**: List of data models (User, Post, Order, etc.)
- [ ] **Relationships mapped**: How entities relate (User has many Posts)
- [ ] **Operations identified**: CRUD operations for each entity
- [ ] **Validation rules**: Field constraints and business rules
- [ ] **Error scenarios**: What can go wrong and how to handle it
- [ ] **Success criteria**: How to know when you're done

### Example: Todo API Planning

**Purpose**: Help users manage their daily tasks with priorities and due dates.

**Entities**:
- **Task**: Individual todo item
- **User**: Task owner (simplified for now)

**Operations**:
- Create new task
- List all tasks (with filtering)
- Get single task
- Update task (mark complete, change priority)
- Delete task

**Validation Rules**:
- Title: Required, 1-200 characters
- Description: Optional, max 1000 characters
- Priority: Must be "low", "medium", or "high"
- Due date: Must be future date
- Completed: Boolean

**Error Scenarios**:
- Task not found (404)
- Invalid data (422)
- Duplicate task title (409)

[↑ Back to TOC](#-table-of-contents)

---

## 🎨 API Design Workflow

### Complete Development Process

```mermaid
graph TB
    subgraph PLANNING ["📋    Planning    &    Design    Phase"]
        P1[📝 Define requirements<br/>What problem to solve]
        P2[🎯 Identify entities<br/>Data models needed]
        P3[🔗 Map relationships<br/>How entities connect]
        P4[📊 Design endpoints<br/>URL patterns & methods]
    end

    subgraph MODELING ["📐    Data    Modeling    Phase"]
        M1[📦 Create Pydantic models<br/>Request/Response DTOs]
        M2[🆔 Add UUID7 fields<br/>Unique identifiers]
        M3[✅ Define validation<br/>Field constraints]
        M4[🔍 Test models<br/>Validate examples]
    end

    subgraph SERVICE ["💼    Service    Layer    Phase"]
        S1[🏗️ Create service class<br/>Business logic container]
        S2[📝 Implement CRUD methods<br/>Create, Read, Update, Delete]
        S3[🔐 Add business rules<br/>Domain validation]
        S4[🧪 Unit test services<br/>Test business logic]
    end

    subgraph ENDPOINTS ["🛣️    Endpoint    Implementation    Phase"]
        E1[🎯 Register endpoints<br/>@app.endpoint decorators]
        E2[📥 Handle requests<br/>Parse & validate input]
        E3[💼 Call services<br/>Execute business logic]
        E4[📤 Return responses<br/>Serialize output]
    end

    subgraph VALIDATION ["✅    Validation    &    Error    Handling"]
        V1[🔍 Input validation<br/>Pydantic checks]
        V2[⚠️ Error handling<br/>Try-except blocks]
        V3[📋 Error formatting<br/>Structured responses]
        V4[🧪 Test error cases<br/>Invalid inputs]
    end

    subgraph TESTING ["🧪    Testing    &    Verification    Phase"]
        T1[📡 Manual testing<br/>curl/Postman requests]
        T2[🤖 Automated tests<br/>pytest test suite]
        T3[📊 Coverage check<br/>Ensure >80% coverage]
        T4[🐛 Fix bugs<br/>Iterate and improve]
    end

    subgraph REFINEMENT ["✨    Refinement    &    Enhancement    Phase"]
        R1[🎨 Add pagination<br/>Handle large datasets]
        R2[🔍 Add filtering<br/>Search & filter data]
        R3[📊 Add sorting<br/>Order results]
        R4[💾 Add caching<br/>Improve performance]
    end

    subgraph DEPLOYMENT ["🚀    Deployment    Preparation    Phase"]
        D1[📝 Write documentation<br/>API usage guide]
        D2[⚙️ Environment config<br/>Production settings]
        D3[🔒 Security review<br/>Auth, rate limits]
        D4[✅ Ready to deploy<br/>Launch preparation]
    end

    %% Planning flow - Blue
    P1 --> P2
    P2 --> P3
    P3 --> P4
    linkStyle 0 stroke:#1976d2,stroke-width:3px
    linkStyle 1 stroke:#1976d2,stroke-width:3px
    linkStyle 2 stroke:#1976d2,stroke-width:3px

    %% Planning to Modeling - Blue
    P4 --> M1
    linkStyle 3 stroke:#1976d2,stroke-width:3px

    %% Modeling flow - Purple
    M1 --> M2
    M2 --> M3
    M3 --> M4
    linkStyle 4 stroke:#7b1fa2,stroke-width:3px
    linkStyle 5 stroke:#7b1fa2,stroke-width:3px
    linkStyle 6 stroke:#7b1fa2,stroke-width:3px

    %% Modeling to Service - Purple
    M4 --> S1
    linkStyle 7 stroke:#7b1fa2,stroke-width:3px

    %% Service flow - Teal
    S1 --> S2
    S2 --> S3
    S3 --> S4
    linkStyle 8 stroke:#00695c,stroke-width:3px
    linkStyle 9 stroke:#00695c,stroke-width:3px
    linkStyle 10 stroke:#00695c,stroke-width:3px

    %% Service to Endpoints - Teal
    S4 --> E1
    linkStyle 11 stroke:#00695c,stroke-width:3px

    %% Endpoints flow - Orange
    E1 --> E2
    E2 --> E3
    E3 --> E4
    linkStyle 12 stroke:#f57c00,stroke-width:3px
    linkStyle 13 stroke:#f57c00,stroke-width:3px
    linkStyle 14 stroke:#f57c00,stroke-width:3px

    %% Endpoints to Validation - Orange
    E4 --> V1
    linkStyle 15 stroke:#f57c00,stroke-width:3px

    %% Validation flow - Green
    V1 --> V2
    V2 --> V3
    V3 --> V4
    linkStyle 16 stroke:#388e3c,stroke-width:3px
    linkStyle 17 stroke:#388e3c,stroke-width:3px
    linkStyle 18 stroke:#388e3c,stroke-width:3px

    %% Validation to Testing - Green
    V4 --> T1
    linkStyle 19 stroke:#388e3c,stroke-width:3px

    %% Testing flow - Pink
    T1 --> T2
    T2 --> T3
    T3 --> T4
    linkStyle 20 stroke:#c2185b,stroke-width:3px
    linkStyle 21 stroke:#c2185b,stroke-width:3px
    linkStyle 22 stroke:#c2185b,stroke-width:3px

    %% Testing to Refinement - Pink (dashed for optional)
    T4 -.-> R1
    linkStyle 23 stroke:#c2185b,stroke-width:2px

    %% Refinement flow - Indigo
    R1 --> R2
    R2 --> R3
    R3 --> R4
    linkStyle 24 stroke:#3f51b5,stroke-width:3px
    linkStyle 25 stroke:#3f51b5,stroke-width:3px
    linkStyle 26 stroke:#3f51b5,stroke-width:3px

    %% Refinement to Deployment - Indigo (final)
    R4 --> D1
    linkStyle 27 stroke:#3f51b5,stroke-width:4px

    %% Deployment flow - Indigo (final, thickest)
    D1 --> D2
    D2 --> D3
    D3 --> D4
    linkStyle 28 stroke:#3f51b5,stroke-width:4px
    linkStyle 29 stroke:#3f51b5,stroke-width:4px
    linkStyle 30 stroke:#3f51b5,stroke-width:4px

    %% Error handling feedback loops - Pink (dashed)
    T4 -.-> S2
    T4 -.-> E2
    T4 -.-> V2
    linkStyle 31 stroke:#c2185b,stroke-width:2px
    linkStyle 32 stroke:#c2185b,stroke-width:2px
    linkStyle 33 stroke:#c2185b,stroke-width:2px

    %% Subgraph styling
    style PLANNING fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style MODELING fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style SERVICE fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style ENDPOINTS fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style VALIDATION fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    style TESTING fill:#fef7f7,stroke:#c2185b,stroke-width:3px,color:#000
    style REFINEMENT fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style DEPLOYMENT fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000

    %% Node styling
    style P1 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style P2 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style P3 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style P4 fill:#e3f2fd,stroke:#1976d2,stroke-width:3px,color:#000
    
    style M1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style M2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style M3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style M4 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:3px,color:#000
    
    style S1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style S2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style S3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style S4 fill:#e0f2f1,stroke:#00695c,stroke-width:3px,color:#000
    
    style E1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style E2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style E3 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style E4 fill:#fff8e1,stroke:#f57c00,stroke-width:3px,color:#000
    
    style V1 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style V2 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style V3 fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000
    style V4 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    
    style T1 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style T2 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style T3 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style T4 fill:#fce4ec,stroke:#c2185b,stroke-width:3px,color:#000
    
    style R1 fill:#e8eaf6,stroke:#3f51b5,stroke-width:2px,color:#000
    style R2 fill:#e8eaf6,stroke:#3f51b5,stroke-width:2px,color:#000
    style R3 fill:#e8eaf6,stroke:#3f51b5,stroke-width:2px,color:#000
    style R4 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    
    style D1 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style D2 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style D3 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
    style D4 fill:#e8eaf6,stroke:#3f51b5,stroke-width:3px,color:#000
```

### Development Time Estimates

| Phase | Simple API | Medium API | Complex API |
|-------|-----------|------------|-------------|
| **Planning** | 30 min | 1 hour | 2 hours |
| **Modeling** | 30 min | 1 hour | 3 hours |
| **Service Layer** | 1 hour | 3 hours | 8 hours |
| **Endpoints** | 1 hour | 2 hours | 4 hours |
| **Validation** | 30 min | 1 hour | 2 hours |
| **Testing** | 1 hour | 2 hours | 4 hours |
| **Refinement** | 1 hour | 3 hours | 6 hours |
| **Documentation** | 30 min | 1 hour | 2 hours |
| **Total** | **6.5 hours** | **14 hours** | **31 hours** |

[↑ Back to TOC](#-table-of-contents)

---

## 📁 Creating the Project Structure

### Recommended Directory Layout

```bash
todo-api/
├── app/
│   ├── __init__.py
│   ├── main.py                 # Application entry point
│   ├── models/
│   │   ├── __init__.py
│   │   └── task.py            # Pydantic models
│   ├── services/
│   │   ├── __init__.py
│   │   └── task_service.py    # Business logic
│   ├── endpoints/
│   │   ├── __init__.py
│   │   └── tasks.py           # API endpoints
│   └── config.py              # Configuration
├── tests/
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_services.py
│   └── test_endpoints.py
├── requirements.txt
├── .env
├── .gitignore
└── README.md
```

### Step-by-Step Setup

```bash
# 1. Create project directory
mkdir todo-api
cd todo-api

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Create directory structure
mkdir -p app/{models,services,endpoints}
mkdir tests

# 5. Create __init__.py files
touch app/__init__.py
touch app/models/__init__.py
touch app/services/__init__.py
touch app/endpoints/__init__.py
touch tests/__init__.py

# 6. Create main files
touch app/main.py
touch app/config.py
touch app/models/task.py
touch app/services/task_service.py
touch app/endpoints/tasks.py
```

### Configuration File

Create `app/config.py`:

```python
"""
Application configuration.
"""
from typing import Optional
from pydantic import BaseModel

class Settings(BaseModel):
    """Application settings."""
    
    # API settings
    api_title: str = "Todo API"
    api_version: str = "1.0.0"
    api_description: str = "Simple todo list management API"
    
    # Server settings
    host: str = "0.0.0.0"
    port: int = 8000
    debug: bool = True
    
    # Database settings (for future use)
    database_url: Optional[str] = None
    
    # Caching settings
    redis_url: Optional[str] = None
    cache_ttl: int = 300

# Global settings instance
settings = Settings()
```

[↑ Back to TOC](#-table-of-contents)

---

## 📐 Defining Data Models

### Model Design Principles

1. **Separate concerns**: Request, Response, and Entity models
2. **Validate strictly**: Use Pydantic constraints
3. **Document fields**: Add descriptions for clarity
4. **Use UUID7**: Time-ordered identifiers
5. **Immutable responses**: Prevent accidental modification

### Complete Task Models

Create `app/models/task.py`:

```python
"""
Task data models with comprehensive validation.
"""
from datetime import datetime
from typing import Optional, Literal
from enum import Enum

from pydantic import BaseModel, Field, field_validator
from api_starter.models import EntityModel, generate_id


class TaskPriority(str, Enum):
    """Task priority levels."""
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"


class TaskStatus(str, Enum):
    """Task completion status."""
    TODO = "todo"
    IN_PROGRESS = "in_progress"
    COMPLETED = "completed"


class TaskBase(BaseModel):
    """Base task fields shared across models."""
    
    title: str = Field(
        min_length=1,
        max_length=200,
        description="Task title",
        examples=["Buy groceries", "Finish project report"]
    )
    
    description: Optional[str] = Field(
        default=None,
        max_length=1000,
        description="Detailed task description"
    )
    
    priority: TaskPriority = Field(
        default=TaskPriority.MEDIUM,
        description="Task priority level"
    )
    
    due_date: Optional[datetime] = Field(
        default=None,
        description="Task due date and time"
    )
    
    tags: list[str] = Field(
        default_factory=list,
        max_length=10,
        description="Task tags for organization"
    )
    
    @field_validator('due_date')
    @classmethod
    def validate_due_date(cls, v):
        """Ensure due date is in the future."""
        if v and v < datetime.utcnow():
            raise ValueError('Due date must be in the future')
        return v
    
    @field_validator('tags')
    @classmethod
    def validate_tags(cls, v):
        """Ensure no duplicate tags."""
        if len(v) != len(set(v)):
            raise ValueError('Tags must be unique')
        # Convert to lowercase for consistency
        return [tag.lower() for tag in v]


class CreateTaskRequest(TaskBase):
    """Request model for creating a new task."""
    
    # All fields inherited from TaskBase
    # No ID or timestamps (generated by server)
    pass


class UpdateTaskRequest(BaseModel):
    """Request model for updating an existing task."""
    
    # All fields are optional for partial updates
    title: Optional[str] = Field(
        default=None,
        min_length=1,
        max_length=200
    )
    
    description: Optional[str] = Field(
        default=None,
        max_length=1000
    )
    
    priority: Optional[TaskPriority] = None
    
    status: Optional[TaskStatus] = None
    
    due_date: Optional[datetime] = None
    
    tags: Optional[list[str]] = None
    
    completed: Optional[bool] = None
    
    @field_validator('due_date')
    @classmethod
    def validate_due_date(cls, v):
        """Ensure due date is in the future."""
        if v and v < datetime.utcnow():
            raise ValueError('Due date must be in the future')
        return v


class Task(EntityModel, TaskBase):
    """
    Task entity with full metadata.
    
    Includes UUID7 ID and timestamps from EntityModel.
    """
    
    status: TaskStatus = Field(
        default=TaskStatus.TODO,
        description="Current task status"
    )
    
    completed: bool = Field(
        default=False,
        description="Whether task is completed"
    )
    
    completed_at: Optional[datetime] = Field(
        default=None,
        description="Timestamp when task was completed"
    )
    
    def mark_completed(self):
        """Mark task as completed."""
        self.completed = True
        self.status = TaskStatus.COMPLETED
        self.completed_at = datetime.utcnow()
        self.update_timestamp()
    
    def mark_incomplete(self):
        """Mark task as incomplete."""
        self.completed = False
        self.status = TaskStatus.TODO
        self.completed_at = None
        self.update_timestamp()


class TaskResponse(Task):
    """
    Response model for task data.
    
    Same as Task entity, used for clarity in API responses.
    """
    pass


class TaskListResponse(BaseModel):
    """Response model for list of tasks with metadata."""
    
    tasks: list[TaskResponse]
    total: int = Field(ge=0, description="Total number of tasks")
    filtered: int = Field(ge=0, description="Number after filtering")
    page: int = Field(ge=1, description="Current page")
    page_size: int = Field(ge=1, le=100, description="Items per page")
    has_next: bool = Field(description="More pages available")


class TaskStatsResponse(BaseModel):
    """Response model for task statistics."""
    
    total: int = Field(ge=0, description="Total tasks")
    completed: int = Field(ge=0, description="Completed tasks")
    pending: int = Field(ge=0, description="Pending tasks")
    overdue: int = Field(ge=0, description="Overdue tasks")
    by_priority: dict[str, int] = Field(
        default_factory=dict,
        description="Count by priority level"
    )
    by_status: dict[str, int] = Field(
        default_factory=dict,
        description="Count by status"
    )
```

### Model Testing

Create `tests/test_models.py`:

```python
"""
Test task models.
"""
import pytest
from datetime import datetime, timedelta
from pydantic import ValidationError

from app.models.task import (
    CreateTaskRequest,
    UpdateTaskRequest,
    Task,
    TaskPriority,
    TaskStatus
)


class TestCreateTaskRequest:
    """Test CreateTaskRequest validation."""
    
    def test_valid_task(self):
        """Test creating valid task."""
        task = CreateTaskRequest(
            title="Test task",
            description="Test description",
            priority=TaskPriority.HIGH,
            tags=["work", "urgent"]
        )
        
        assert task.title == "Test task"
        assert task.priority == TaskPriority.HIGH
        assert len(task.tags) == 2
    
    def test_title_required(self):
        """Test title is required."""
        with pytest.raises(ValidationError) as exc:
            CreateTaskRequest(title="")
        
        assert "at least 1 character" in str(exc.value)
    
    def test_title_max_length(self):
        """Test title max length."""
        with pytest.raises(ValidationError):
            CreateTaskRequest(title="x" * 201)
    
    def test_future_due_date(self):
        """Test due date must be in future."""
        future = datetime.utcnow() + timedelta(days=1)
        task = CreateTaskRequest(
            title="Test",
            due_date=future
        )
        assert task.due_date == future
    
    def test_past_due_date_fails(self):
        """Test past due date raises error."""
        past = datetime.utcnow() - timedelta(days=1)
        
        with pytest.raises(ValidationError) as exc:
            CreateTaskRequest(title="Test", due_date=past)
        
        assert "future" in str(exc.value).lower()
    
    def test_unique_tags(self):
        """Test tags must be unique."""
        with pytest.raises(ValidationError) as exc:
            CreateTaskRequest(
                title="Test",
                tags=["work", "work"]
            )
        
        assert "unique" in str(exc.value).lower()
    
    def test_tags_lowercase(self):
        """Test tags are converted to lowercase."""
        task = CreateTaskRequest(
            title="Test",
            tags=["Work", "URGENT"]
        )
        
        assert task.tags == ["work", "urgent"]


class TestTask:
    """Test Task entity."""
    
    def test_task_has_id(self):
        """Test task gets UUID7 ID."""
        task = Task(title="Test")
        
        assert task.id is not None
        assert len(task.id) == 36  # UUID format
    
    def test_mark_completed(self):
        """Test marking task as completed."""
        task = Task(title="Test")
        
        assert task.completed is False
        assert task.status == TaskStatus.TODO
        
        task.mark_completed()
        
        assert task.completed is True
        assert task.status == TaskStatus.COMPLETED
        assert task.completed_at is not None
    
    def test_mark_incomplete(self):
        """Test marking task as incomplete."""
        task = Task(title="Test")
        task.mark_completed()
        
        task.mark_incomplete()
        
        assert task.completed is False
        assert task.status == TaskStatus.TODO
        assert task.completed_at is None
```

[↑ Back to TOC](#-table-of-contents)

---

## 💼 Building the Service Layer

### Service Layer Architecture

```mermaid
graph TB
    subgraph ENDPOINTS ["🛣️    API    Endpoints    Layer"]
        EP1[POST /tasks<br/>Create task]
        EP2[GET /tasks<br/>List tasks]
        EP3[GET /tasks/:id<br/>Get task]
        EP4[PUT /tasks/:id<br/>Update task]
        EP5[DELETE /tasks/:id<br/>Delete task]
    end

    subgraph SERVICE ["💼    Task    Service    Layer"]
        SV1[📝 create_task<br/>Business logic]
        SV2[📋 list_tasks<br/>Query & filter]
        SV3[🔍 get_task<br/>Single retrieval]
        SV4[✏️ update_task<br/>Modification logic]
        SV5[🗑️ delete_task<br/>Removal logic]
        SV6[📊 get_stats<br/>Statistics]
    end

    subgraph VALIDATION ["✅    Business    Validation"]
        VL1[🔍 Check duplicates<br/>Title uniqueness]
        VL2[📅 Validate dates<br/>Due date logic]
        VL3[🏷️ Validate tags<br/>Tag constraints]
        VL4[🔐 Check ownership<br/>Access control]
    end

    subgraph STORAGE ["💾    Data    Storage    Layer"]
        ST1[(📦 In-Memory Store<br/>Dict storage)]
        ST2[(🗄️ PostgreSQL<br/>Future: Real DB)]
        ST3[(📦 Redis Cache<br/>Future: Caching)]
    end

    %% Endpoints to Service - Blue
    EP1 --> SV1
    EP2 --> SV2
    EP3 --> SV3
    EP4 --> SV4
    EP5 --> SV5
    linkStyle 0 stroke:#1976d2,stroke-width:3px
    linkStyle 1 stroke:#1976d2,stroke-width:3px
    linkStyle 2 stroke:#1976d2,stroke-width:3px
    linkStyle 3 stroke:#1976d2,stroke-width:3px
    linkStyle 4 stroke:#1976d2,stroke-width:3px

    %% Service to Validation - Purple
    SV1 --> VL1
    SV1 --> VL2
    SV1 --> VL3
    SV4 --> VL2
    SV4 --> VL4
    linkStyle 5 stroke:#7b1fa2,stroke-width:3px
    linkStyle 6 stroke:#7b1fa2,stroke-width:3px
    linkStyle 7 stroke:#7b1fa2,stroke-width:3px
    linkStyle 8 stroke:#7b1fa2,stroke-width:3px
    linkStyle 9 stroke:#7b1fa2,stroke-width:3px

    %% Service internal operations - Teal
    SV2 --> SV6
    SV3 --> VL4
    linkStyle 10 stroke:#00695c,stroke-width:3px
    linkStyle 11 stroke:#00695c,stroke-width:3px

    %% Service to Storage - Orange
    SV1 --> ST1
    SV2 --> ST1
    SV3 --> ST1
    SV4 --> ST1
    SV5 --> ST1
    linkStyle 12 stroke:#f57c00,stroke-width:3px
    linkStyle 13 stroke:#f57c00,stroke-width:3px
    linkStyle 14 stroke:#f57c00,stroke-width:3px
    linkStyle 15 stroke:#f57c00,stroke-width:3px
    linkStyle 16 stroke:#f57c00,stroke-width:3px

    %% Future storage connections - Pink (dashed)
    ST1 -.-> ST2
    ST1 -.-> ST3
    linkStyle 17 stroke:#c2185b,stroke-width:2px
    linkStyle 18 stroke:#c2185b,stroke-width:2px

    %% Subgraph styling
    style ENDPOINTS fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style SERVICE fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style VALIDATION fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style STORAGE fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000

    %% Node styling
    style EP1 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style EP2 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style EP3 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style EP4 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style EP5 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    
    style SV1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV4 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV5 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style SV6 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    
    style VL1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style VL2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style VL3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style VL4 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    
    style ST1 fill:#fff8e1,stroke:#f57c00,stroke-width:3px,color:#000
    style ST2 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style ST3 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
```

### Complete Service Implementation

Create `app/services/task_service.py`:

```python
"""
Task service with business logic.
"""
from datetime import datetime
from typing import Optional

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
    Task service handling all task-related business logic.
    
    This service is protocol-independent and can be used by
    HTTP, GraphQL, or gRPC endpoints.
    """
    
    def __init__(self):
        """Initialize service with in-memory storage."""
        self._tasks: dict[str, Task] = {}
    
    async def create_task(
        self,
        data: CreateTaskRequest,
        user_id: Optional[str] = None
    ) -> Task:
        """
        Create a new task.
        
        Args:
            data: Task creation data
            user_id: Optional user ID for ownership
        
        Returns:
            Created task
        
        Raises:
            ValueError: If task with same title exists
        """
        # Check for duplicate title
        existing = await self.get_task_by_title(data.title)
        if existing:
            raise ValueError(f"Task with title '{data.title}' already exists")
        
        # Create task entity
        task = Task(
            title=data.title,
            description=data.description,
            priority=data.priority,
            due_date=data.due_date,
            tags=data.tags
        )
        
        # Store task
        self._tasks[task.id] = task
        
        return task
    
    async def get_task(self, task_id: str) -> Optional[Task]:
        """
        Get task by ID.
        
        Args:
            task_id: Task identifier
        
        Returns:
            Task if found, None otherwise
        """
        return self._tasks.get(task_id)
    
    async def get_task_by_title(self, title: str) -> Optional[Task]:
        """
        Get task by title.
        
        Args:
            title: Task title
        
        Returns:
            Task if found, None otherwise
        """
        for task in self._tasks.values():
            if task.title.lower() == title.lower():
                return task
        return None
    
    async def list_tasks(
        self,
        status: Optional[TaskStatus] = None,
        priority: Optional[TaskPriority] = None,
        tag: Optional[str] = None,
        completed: Optional[bool] = None,
        overdue_only: bool = False,
        skip: int = 0,
        limit: int = 100
    ) -> list[Task]:
        """
        List tasks with optional filtering.
        
        Args:
            status: Filter by status
            priority: Filter by priority
            tag: Filter by tag
            completed: Filter by completion status
            overdue_only: Show only overdue tasks
            skip: Number of tasks to skip
            limit: Maximum tasks to return
        
        Returns:
            List of filtered tasks
        """
        tasks = list(self._tasks.values())
        
        # Apply filters
        if status is not None:
            tasks = [t for t in tasks if t.status == status]
        
        if priority is not None:
            tasks = [t for t in tasks if t.priority == priority]
        
        if tag is not None:
            tasks = [t for t in tasks if tag.lower() in t.tags]
        
        if completed is not None:
            tasks = [t for t in tasks if t.completed == completed]
        
        if overdue_only:
            now = datetime.utcnow()
            tasks = [
                t for t in tasks
                if t.due_date and t.due_date < now and not t.completed
            ]
        
        # Sort by created_at (newest first)
        tasks.sort(key=lambda t: t.created_at, reverse=True)
        
        # Apply pagination
        return tasks[skip:skip + limit]
    
    async def update_task(
        self,
        task_id: str,
        data: UpdateTaskRequest
    ) -> Optional[Task]:
        """
        Update an existing task.
        
        Args:
            task_id: Task identifier
            data: Update data
        
        Returns:
            Updated task if found, None otherwise
        
        Raises:
            ValueError: If update would create duplicate title
        """
        task = await self.get_task(task_id)
        if not task:
            return None
        
        # Check for duplicate title if changing title
        if data.title and data.title != task.title:
            existing = await self.get_task_by_title(data.title)
            if existing:
                raise ValueError(
                    f"Task with title '{data.title}' already exists"
                )
        
        # Update fields (only if provided)
        if data.title is not None:
            task.title = data.title
        
        if data.description is not None:
            task.description = data.description
        
        if data.priority is not None:
            task.priority = data.priority
        
        if data.status is not None:
            task.status = data.status
        
        if data.due_date is not None:
            task.due_date = data.due_date
        
        if data.tags is not None:
            task.tags = data.tags
        
        if data.completed is not None:
            if data.completed and not task.completed:
                task.mark_completed()
            elif not data.completed and task.completed:
                task.mark_incomplete()
        
        task.update_timestamp()
        
        return task
    
    async def delete_task(self, task_id: str) -> bool:
        """
        Delete a task.
        
        Args:
            task_id: Task identifier
        
        Returns:
            True if deleted, False if not found
        """
        if task_id in self._tasks:
            del self._tasks[task_id]
            return True
        return False
    
    async def get_statistics(self) -> TaskStatsResponse:
        """
        Get task statistics.
        
        Returns:
            Statistics about all tasks
        """
        tasks = list(self._tasks.values())
        
        completed = sum(1 for t in tasks if t.completed)
        pending = len(tasks) - completed
        
        # Count overdue tasks
        now = datetime.utcnow()
        overdue = sum(
            1 for t in tasks
            if t.due_date and t.due_date < now and not t.completed
        )
        
        # Count by priority
        by_priority = {
            priority.value: sum(1 for t in tasks if t.priority == priority)
            for priority in TaskPriority
        }
        
        # Count by status
        by_status = {
            status.value: sum(1 for t in tasks if t.status == status)
            for status in TaskStatus
        }
        
        return TaskStatsResponse(
            total=len(tasks),
            completed=completed,
            pending=pending,
            overdue=overdue,
            by_priority=by_priority,
            by_status=by_status
        )
    
    async def search_tasks(self, query: str) -> list[Task]:
        """
        Search tasks by title or description.
        
        Args:
            query: Search query
        
        Returns:
            List of matching tasks
        """
        query_lower = query.lower()
        
        return [
            task for task in self._tasks.values()
            if (query_lower in task.title.lower() or
                (task.description and query_lower in task.description.lower()))
        ]
```

[↑ Back to TOC](#-table-of-contents)

---

## 🛣️ Implementing CRUD Endpoints

### Endpoint Implementation Pattern

Create `app/endpoints/tasks.py`:

```python
"""
Task API endpoints.
"""
from typing import Optional
from starlette.responses import JSONResponse

from api_starter import APIStarter, RequestContext
from app.models.task import (
    CreateTaskRequest,
    UpdateTaskRequest,
    TaskResponse,
    TaskListResponse,
    TaskPriority,
    TaskStatus
)
from app.services.task_service import TaskService


# Initialize service
task_service = TaskService()


def register_task_endpoints(app: APIStarter):
    """Register all task endpoints with the application."""
    
    @app.endpoint("/tasks", methods=["POST"])
    async def create_task(ctx: RequestContext, data: CreateTaskRequest):
        """
        Create a new task.
        
        Request body:
        {
            "title": "Buy groceries",
            "description": "Milk, eggs, bread",
            "priority": "high",
            "due_date": "2025-01-20T10:00:00",
            "tags": ["shopping", "urgent"]
        }
        """
        try:
            task = await task_service.create_task(data)
            
            return JSONResponse(
                {
                    "message": "Task created successfully",
                    "task": task.model_dump()
                },
                status_code=201
            )
        
        except ValueError as e:
            return JSONResponse(
                {
                    "error": str(e),
                    "request_id": ctx.request_id
                },
                status_code=409  # Conflict
            )
    
    @app.endpoint("/tasks", methods=["GET"])
    async def list_tasks(ctx: RequestContext):
        """
        List all tasks with optional filtering.
        
        Query parameters:
        - status: Filter by status (todo, in_progress, completed)
        - priority: Filter by priority (low, medium, high)
        - tag: Filter by tag
        - completed: Filter by completion (true/false)
        - overdue: Show only overdue tasks (true/false)
        - page: Page number (default: 1)
        - page_size: Items per page (default: 20)
        """
        # Parse query parameters
        status_str = ctx.query.get("status")
        priority_str = ctx.query.get("priority")
        tag = ctx.query.get("tag")
        completed_str = ctx.query.get("completed")
        overdue_str = ctx.query.get("overdue")
        page = int(ctx.query.get("page", 1))
        page_size = int(ctx.query.get("page_size", 20))
        
        # Convert string parameters to enums/booleans
        status = TaskStatus(status_str) if status_str else None
        priority = TaskPriority(priority_str) if priority_str else None
        completed = completed_str.lower() == "true" if completed_str else None
        overdue_only = overdue_str.lower() == "true" if overdue_str else False
        
        # Calculate skip
        skip = (page - 1) * page_size
        
        # Get tasks
        tasks = await task_service.list_tasks(
            status=status,
            priority=priority,
            tag=tag,
            completed=completed,
            overdue_only=overdue_only,
            skip=skip,
            limit=page_size + 1  # Get one extra to check if more exist
        )
        
        # Check if more pages exist
        has_next = len(tasks) > page_size
        if has_next:
            tasks = tasks[:page_size]
        
        # Get total count (for all tasks)
        all_tasks = await task_service.list_tasks(limit=10000)
        
        return {
            "tasks": [t.model_dump() for t in tasks],
            "total": len(all_tasks),
            "filtered": len(tasks),
            "page": page,
            "page_size": page_size,
            "has_next": has_next
        }
    
    @app.endpoint("/tasks/{task_id}", methods=["GET"])
    async def get_task(ctx: RequestContext, task_id: str):
        """
        Get a specific task by ID.
        
        Path parameters:
        - task_id: Task UUID
        """
        task = await task_service.get_task(task_id)
        
        if not task:
            return JSONResponse(
                {
                    "error": "Task not found",
                    "request_id": ctx.request_id
                },
                status_code=404
            )
        
        return {"task": task.model_dump()}
    
    @app.endpoint("/tasks/{task_id}", methods=["PUT"])
    async def update_task(ctx: RequestContext, task_id: str):
        """
        Update an existing task.
        
        Path parameters:
        - task_id: Task UUID
        
        Request body (all fields optional):
        {
            "title": "Updated title",
            "status": "completed",
            "completed": true
        }
        """
        # Parse request body
        from starlette.requests import Request
        request = Request(ctx._request)  # Access underlying Starlette request
        body = await request.json()
        
        try:
            data = UpdateTaskRequest(**body)
            task = await task_service.update_task(task_id, data)
            
            if not task:
                return JSONResponse(
                    {
                        "error": "Task not found",
                        "request_id": ctx.request_id
                    },
                    status_code=404
                )
            
            return {
                "message": "Task updated successfully",
                "task": task.model_dump()
            }
        
        except ValueError as e:
            return JSONResponse(
                {
                    "error": str(e),
                    "request_id": ctx.request_id
                },
                status_code=409
            )
    
    @app.endpoint("/tasks/{task_id}", methods=["DELETE"])
    async def delete_task(ctx: RequestContext, task_id: str):
        """
        Delete a task.
        
        Path parameters:
        - task_id: Task UUID
        """
        deleted = await task_service.delete_task(task_id)
        
        if not deleted:
            return JSONResponse(
                {
                    "error": "Task not found",
                    "request_id": ctx.request_id
                },
                status_code=404
            )
        
        return {
            "message": "Task deleted successfully",
            "task_id": task_id
        }
    
    @app.endpoint("/tasks/stats", methods=["GET"])
    async def get_statistics(ctx: RequestContext):
        """
        Get task statistics.
        
        Returns counts by status, priority, and completion.
        """
        stats = await task_service.get_statistics()
        return {"stats": stats.model_dump()}
    
    @app.endpoint("/tasks/search", methods=["GET"])
    async def search_tasks(ctx: RequestContext):
        """
        Search tasks by title or description.
        
        Query parameters:
        - q: Search query
        """
        query = ctx.query.get("q", "")
        
        if not query:
            return JSONResponse(
                {
                    "error": "Query parameter 'q' is required",
                    "request_id": ctx.request_id
                },
                status_code=400
            )
        
        tasks = await task_service.search_tasks(query)
        
        return {
            "query": query,
            "results": [t.model_dump() for t in tasks],
            "count": len(tasks)
        }
```

### Application Entry Point

Create `app/main.py`:

```python
"""
Application entry point.
"""
from api_starter import APIStarter
from app.config import settings
from app.endpoints.tasks import register_task_endpoints


# Create application
app = APIStarter(
    title=settings.api_title,
    version=settings.api_version,
    description=settings.api_description,
    debug=settings.debug
)

# Register endpoints
register_task_endpoints(app)


# Root endpoint
@app.endpoint("/", methods=["GET"])
async def root(ctx):
    """API information."""
    return {
        "message": f"Welcome to {settings.api_title}!",
        "version": settings.api_version,
        "endpoints": {
            "tasks": "/tasks",
            "stats": "/tasks/stats",
            "search": "/tasks/search",
            "health": "/health",
            "docs": "/openapi.json"
        }
    }


if __name__ == "__main__":
    app.run(
        host=settings.host,
        port=settings.port
    )
```

[↑ Back to TOC](#-table-of-contents)

---

## ✅ Adding Input Validation

### Validation Flow

Input validation happens at three levels:

```mermaid
graph TB
    subgraph INPUT ["📥    Raw    Input    Reception"]
        I1[🌐 HTTP request<br/>JSON body]
        I2[📦 Parse JSON<br/>Convert to dict]
    end

    subgraph PYDANTIC ["🔷    Pydantic    Validation    Level    1"]
        P1[📋 Type checking<br/>str, int, datetime, etc]
        P2[📏 Field constraints<br/>min/max length, regex]
        P3[🔢 Numeric constraints<br/>ge, le, gt, lt]
        P4{Types valid?}
    end

    subgraph CUSTOM ["🔶    Custom    Validators    Level    2"]
        C1[🏷️ Tag uniqueness<br/>No duplicates]
        C2[📅 Future dates<br/>Due date check]
        C3[📝 Format validation<br/>Email, URL patterns]
        C4{Business<br/>rules valid?}
    end

    subgraph SERVICE ["💼    Service    Validation    Level    3"]
        S1[🔍 Uniqueness checks<br/>No duplicate titles]
        S2[🔐 Authorization<br/>User can access]
        S3[📊 Consistency<br/>Data integrity]
        S4{Domain<br/>rules valid?}
    end

    subgraph SUCCESS ["✅    Validation    Success"]
        OK1[📦 Validated object<br/>Type-safe instance]
        OK2[💼 Execute logic<br/>Process request]
    end

    subgraph ERROR ["❌    Validation    Errors"]
        E1[422 Unprocessable<br/>Pydantic errors]
        E2[422 Unprocessable<br/>Custom validator errors]
        E3[409 Conflict<br/>Business rule violations]
        E4[📤 Return error<br/>Structured response]
    end

    %% Input flow - Blue
    I1 --> I2
    linkStyle 0 stroke:#1976d2,stroke-width:3px

    %% Pydantic validation - Purple
    I2 --> P1
    P1 --> P2
    P2 --> P3
    P3 --> P4
    linkStyle 1 stroke:#7b1fa2,stroke-width:3px
    linkStyle 2 stroke:#7b1fa2,stroke-width:3px
    linkStyle 3 stroke:#7b1fa2,stroke-width:3px
    linkStyle 4 stroke:#7b1fa2,stroke-width:3px

    %% Pydantic branching
    P4 -->|No| E1
    P4 -->|Yes| C1
    linkStyle 5 stroke:#c2185b,stroke-width:2px
    linkStyle 6 stroke:#7b1fa2,stroke-width:3px

    %% Custom validation - Orange
    C1 --> C2
    C2 --> C3
    C3 --> C4
    linkStyle 7 stroke:#f57c00,stroke-width:3px
    linkStyle 8 stroke:#f57c00,stroke-width:3px
    linkStyle 9 stroke:#f57c00,stroke-width:3px

    %% Custom branching
    C4 -->|No| E2
    C4 -->|Yes| S1
    linkStyle 10 stroke:#c2185b,stroke-width:2px
    linkStyle 11 stroke:#f57c00,stroke-width:3px

    %% Service validation - Teal
    S1 --> S2
    S2 --> S3
    S3 --> S4
    linkStyle 12 stroke:#00695c,stroke-width:3px
    linkStyle 13 stroke:#00695c,stroke-width:3px
    linkStyle 14 stroke:#00695c,stroke-width:3px

    %% Service branching
    S4 -->|No| E3
    S4 -->|Yes| OK1
    linkStyle 15 stroke:#c2185b,stroke-width:2px
    linkStyle 16 stroke:#388e3c,stroke-width:3px

    %% Success flow - Green
    OK1 --> OK2
    linkStyle 17 stroke:#388e3c,stroke-width:3px

    %% Error consolidation - Pink (dashed)
    E1 -.-> E4
    E2 -.-> E4
    E3 -.-> E4
    linkStyle 18 stroke:#c2185b,stroke-width:2px
    linkStyle 19 stroke:#c2185b,stroke-width:2px
    linkStyle 20 stroke:#c2185b,stroke-width:2px

    %% Subgraph styling
    style INPUT fill:#e8f4fd,stroke:#1976d2,stroke-width:3px,color:#000
    style PYDANTIC fill:#f8f0ff,stroke:#7b1fa2,stroke-width:3px,color:#000
    style CUSTOM fill:#fff4e6,stroke:#f57c00,stroke-width:3px,color:#000
    style SERVICE fill:#f0fffe,stroke:#00695c,stroke-width:3px,color:#000
    style SUCCESS fill:#f0f8f0,stroke:#388e3c,stroke-width:3px,color:#000
    style ERROR fill:#fef7f7,stroke:#c2185b,stroke-width:3px,color:#000

    %% Node styling
    style I1 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    style I2 fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000
    
    style P1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style P2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style P3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    style P4 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:3px,color:#000
    
    style C1 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style C2 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style C3 fill:#fff8e1,stroke:#f57c00,stroke-width:2px,color:#000
    style C4 fill:#fff8e1,stroke:#f57c00,stroke-width:3px,color:#000
    
    style S1 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style S2 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style S3 fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:#000
    style S4 fill:#e0f2f1,stroke:#00695c,stroke-width:3px,color:#000
    
    style OK1 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    style OK2 fill:#e8f5e8,stroke:#388e3c,stroke-width:3px,color:#000
    
    style E1 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style E2 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style E3 fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000
    style E4 fill:#fce4ec,stroke:#c2185b,stroke-width:3px,color:#000
```

### Validation Examples

**Level 1: Pydantic Type/Constraint Validation**
```python
# Automatic validation by Pydantic
class CreateTaskRequest(BaseModel):
    title: str = Field(min_length=1, max_length=200)
    priority: TaskPriority = Field(default=TaskPriority.MEDIUM)
    tags: list[str] = Field(max_length=10)

# Invalid request automatically rejected:
# {"title": "", "tags": ["a"] * 11}
# Error: title min_length, tags max_length
```

**Level 2: Custom Field Validators**
```python
@field_validator('due_date')
@classmethod
def validate_due_date(cls, v):
    """Ensure due date is in the future."""
    if v and v < datetime.utcnow():
        raise ValueError('Due date must be in the future')
    return v

@field_validator('tags')
@classmethod
def validate_tags(cls, v):
    """Ensure no duplicate tags."""
    if len(v) != len(set(v)):
        raise ValueError('Tags must be unique')
    return [tag.lower() for tag in v]
```

**Level 3: Service-Level Business Rules**
```python
async def create_task(self, data: CreateTaskRequest):
    # Check for duplicate title
    existing = await self.get_task_by_title(data.title)
    if existing:
        raise ValueError(f"Task with title '{data.title}' already exists")
    
    # Create task...
```

[↑ Back to TOC](#-table-of-contents)

---

## ⚠️ Implementing Error Handling

### Error Response Structure

```python
# Validation Error (422)
{
  "error": "Validation failed",
  "request_id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f",
  "details": [
    {
      "field": "title",
      "message": "String should have at least 1 character",
      "type": "string_too_short"
    },
    {
      "field": "tags",
      "message": "List should have at most 10 items",
      "type": "too_long"
    }
  ]
}

# Business Rule Error (409)
{
  "error": "Task with title 'Buy groceries' already exists",
  "request_id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f"
}

# Not Found (404)
{
  "error": "Task not found",
  "request_id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f"
}

# Server Error (500)
{
  "error": "Internal server error",
  "request_id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f",
  "message": "Database connection failed"  # Only in debug mode
}
```

### Error Handling Best Practices

1. **Use appropriate HTTP status codes**
2. **Include request_id for tracing**
3. **Provide actionable error messages**
4. **Don't leak sensitive information**
5. **Log errors for debugging**

[↑ Back to TOC](#-table-of-contents)

---

## 🧪 Testing Your API

### Manual Testing with curl

```bash
# 1. Start the server
python app/main.py

# 2. Test root endpoint
curl http://localhost:8000/

# 3. Create a task
curl -X POST http://localhost:8000/tasks \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Buy groceries",
    "description": "Milk, eggs, bread",
    "priority": "high",
    "tags": ["shopping", "urgent"]
  }'

# Response:
# {
#   "message": "Task created successfully",
#   "task": {
#     "id": "018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f",
#     "title": "Buy groceries",
#     ...
#   }
# }

# 4. List tasks
curl http://localhost:8000/tasks

# 5. Get specific task (use ID from create response)
curl http://localhost:8000/tasks/018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f

# 6. Update task
curl -X PUT http://localhost:8000/tasks/018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f \
  -H "Content-Type: application/json" \
  -d '{
    "completed": true,
    "status": "completed"
  }'

# 7. Search tasks
curl "http://localhost:8000/tasks/search?q=groceries"

# 8. Get statistics
curl http://localhost:8000/tasks/stats

# 9. Delete task
curl -X DELETE http://localhost:8000/tasks/018d2e5f-3b4c-7a9e-8f2d-1c3e4b5a6d7f

# 10. Test filtering
curl "http://localhost:8000/tasks?priority=high&completed=false"
```

### Automated Testing with pytest

Create `tests/test_endpoints.py`:

```python
"""
Test task endpoints.
"""
import pytest
from httpx import AsyncClient

from app.main import app


@pytest.mark.asyncio
class TestTaskEndpoints:
    """Test task API endpoints."""
    
    async def test_create_task(self):
        """Test creating a task."""
        async with AsyncClient(app=app, base_url="http://test") as client:
            response = await client.post(
                "/tasks",
                json={
                    "title": "Test task",
                    "description": "Test description",
                    "priority": "high"
                }
            )
        
        assert response.status_code == 201
        data = response.json()
        assert data["message"] == "Task created successfully"
        assert "task" in data
        assert data["task"]["title"] == "Test task"
    
    async def test_list_tasks(self):
        """Test listing tasks."""
        async with AsyncClient(app=app, base_url="http://test") as client:
            # Create a task first
            await client.post(
                "/tasks",
                json={"title": "Task 1"}
            )
            
            # List tasks
            response = await client.get("/tasks")
        
        assert response.status_code == 200
        data = response.json()
        assert "tasks" in data
        assert len(data["tasks"]) > 0
    
    async def test_duplicate_title_fails(self):
        """Test that duplicate titles are rejected."""
        async with AsyncClient(app=app, base_url="http://test") as client:
            # Create first task
            await client.post(
                "/tasks",
                json={"title": "Unique task"}
            )
            
            # Try to create duplicate
            response = await client.post(
                "/tasks",
                json={"title": "Unique task"}
            )
        
        assert response.status_code == 409
        data = response.json()
        assert "already exists" in data["error"]
```

Run tests:

```bash
# Run all tests
pytest tests/ -v

# Run specific test file
pytest tests/test_endpoints.py -v

# Run with coverage
pytest tests/ --cov=app --cov-report=html
```

[↑ Back to TOC](#-table-of-contents)

---

## 📝 Complete Example: Todo API

### Full Working Example

You now have a complete Todo API with:

✅ **Data Models**
- CreateTaskRequest (input validation)
- UpdateTaskRequest (partial updates)
- Task (entity with UUID7)
- TaskResponse (output)
- TaskListResponse (pagination)
- TaskStatsResponse (statistics)

✅ **Service Layer**
- create_task() - Create with duplicate check
- list_tasks() - With filtering and pagination
- get_task() - Single retrieval
- update_task() - Partial updates
- delete_task() - Removal
- get_statistics() - Statistics
- search_tasks() - Full-text search

✅ **API Endpoints**
- POST /tasks - Create task
- GET /tasks - List with filters
- GET /tasks/{id} - Get single task
- PUT /tasks/{id} - Update task
- DELETE /tasks/{id} - Delete task
- GET /tasks/stats - Statistics
- GET /tasks/search?q=query - Search

✅ **Features**
- UUID7 identifiers
- Input validation (3 levels)
- Error handling
- Pagination
- Filtering
- Searching
- Statistics

### Running the Complete Example

```bash
# Start server
python app/main.py

# Server output:
# INFO:     Started server process [12345]
# INFO:     Waiting for application startup.
# INFO:     Application startup complete.
# INFO:     Uvicorn running on http://0.0.0.0:8000
```

[↑ Back to TOC](#-table-of-contents)

---

## 🚀 Advanced Features

### Adding Pagination

Already implemented in `list_tasks`:

```python
# Request
GET /tasks?page=2&page_size=10

# Response
{
  "tasks": [...],
  "total": 45,
  "filtered": 10,
  "page": 2,
  "page_size": 10,
  "has_next": true
}
```

### Adding Sorting

Extend `list_tasks` to support sorting:

```python
async def list_tasks(
    self,
    sort_by: str = "created_at",
    sort_order: str = "desc",
    ...
):
    # ... existing filtering code ...
    
    # Sort
    reverse = sort_order == "desc"
    if sort_by == "created_at":
        tasks.sort(key=lambda t: t.created_at, reverse=reverse)
    elif sort_by == "due_date":
        tasks.sort(
            key=lambda t: t.due_date or datetime.max,
            reverse=reverse
        )
    elif sort_by == "priority":
        priority_order = {
            TaskPriority.HIGH: 3,
            TaskPriority.MEDIUM: 2,
            TaskPriority.LOW: 1
        }
        tasks.sort(
            key=lambda t: priority_order[t.priority],
            reverse=reverse
        )
    
    return tasks[skip:skip + limit]
```

### Adding Bulk Operations

```python
@app.endpoint("/tasks/bulk-complete", methods=["POST"])
async def bulk_complete(ctx: RequestContext):
    """
    Mark multiple tasks as completed.
    
    Request body:
    {
        "task_ids": ["id1", "id2", "id3"]
    }
    """
    from starlette.requests import Request
    request = Request(ctx._request)
    body = await request.json()
    
    task_ids = body.get("task_ids", [])
    completed_count = 0
    
    for task_id in task_ids:
        task = await task_service.get_task(task_id)
        if task:
            task.mark_completed()
            completed_count += 1
    
    return {
        "message": f"Completed {completed_count} tasks",
        "completed": completed_count,
        "total": len(task_ids)
    }
```

[↑ Back to TOC](#-table-of-contents)

---

## 🎯 Deployment Preparation

### Environment Configuration

Create `.env` file:

```bash
# Application
API_TITLE="Todo API"
API_VERSION="1.0.0"
DEBUG=false

# Server
HOST=0.0.0.0
PORT=8000

# Database (for future)
DATABASE_URL=postgresql://user:pass@localhost/todos

# Redis (for future caching)
REDIS_URL=redis://localhost:6379

# Security (for future auth)
SECRET_KEY=your-secret-key-here
```

### Production Settings

Update `app/config.py` for production:

```python
import os
from pydantic import BaseModel

class Settings(BaseModel):
    """Application settings with environment variable support."""
    
    api_title: str = os.getenv("API_TITLE", "Todo API")
    api_version: str = os.getenv("API_VERSION", "1.0.0")
    debug: bool = os.getenv("DEBUG", "false").lower() == "true"
    
    host: str = os.getenv("HOST", "0.0.0.0")
    port: int = int(os.getenv("PORT", "8000"))
    
    database_url: str = os.getenv("DATABASE_URL", None)
    redis_url: str = os.getenv("REDIS_URL", None)
    secret_key: str = os.getenv("SECRET_KEY", "change-me-in-production")

settings = Settings()
```

### Docker Deployment

Create `Dockerfile`:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/

EXPOSE 8000

CMD ["python", "app/main.py"]
```

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DEBUG=false
      - DATABASE_URL=postgresql://user:pass@db/todos
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
  
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=todos
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

Deploy:

```bash
# Build and run
docker-compose up --build

# Run in background
docker-compose up -d

# View logs
docker-compose logs -f api

# Stop
docker-compose down
```

[↑ Back to TOC](#-table-of-contents)

---

## 🎓 Summary

You've successfully built a complete production-ready API! Here's what you accomplished:

### ✅ What You Built

- **Complete Todo API** with 8 endpoints
- **Data Models** with comprehensive validation
- **Service Layer** with business logic separation
- **CRUD Operations** (Create, Read, Update, Delete)
- **Advanced Features** (filtering, pagination, search, stats)
- **Error Handling** with proper status codes
- **Testing** with manual and automated tests
- **Deployment** configuration for production

### 📊 API Capabilities

| Feature | Status | Description |
|---------|--------|-------------|
| **Create Tasks** | ✅ Complete | With duplicate detection |
| **List Tasks** | ✅ Complete | Pagination and filtering |
| **Update Tasks** | ✅ Complete | Partial updates supported |
| **Delete Tasks** | ✅ Complete | Soft or hard delete |
| **Search** | ✅ Complete | Full-text search |
| **Statistics** | ✅ Complete | By status, priority |
| **Validation** | ✅ Complete | 3-level validation |
| **Error Handling** | ✅ Complete | Structured responses |

### 🚀 Next Steps

**Section 5: Adding Enterprise Plugins**
- Enable caching for better performance
- Add GraphQL for flexible queries
- Implement gRPC for high-performance services
- Add metrics and tracing

**Section 6: Advanced Patterns**
- Database integration (PostgreSQL)
- Authentication and authorization
- WebSocket support for real-time updates
- Advanced error handling patterns

### 💡 Key Takeaways

1. **Plan first, code second** - Good design saves debugging time
2. **Separate concerns** - Models, services, and endpoints are distinct
3. **Validate everything** - Pydantic + custom validators + business rules
4. **Test early and often** - Catch bugs before production
5. **Start simple, scale gradually** - Add features as needed

[↑ Back to TOC](#-table-of-contents)

