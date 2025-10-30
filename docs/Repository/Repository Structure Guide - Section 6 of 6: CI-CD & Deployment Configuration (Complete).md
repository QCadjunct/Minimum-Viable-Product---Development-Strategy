# 📁 Python API Starter - Repository Structure Guide

**Section 6 of 6: CI/CD & Deployment Configuration (Complete)**

---

## 📑 Table of Contents

- [CI/CD Directory Structure](#-cicd-directory-structure)
- [GitHub Actions Workflows](#-github-actions-workflows)
- [Docker Configuration](#-docker-configuration)
- [Kubernetes Deployment](#-kubernetes-deployment)
- [Deployment Scripts](#-deployment-scripts)
- [Environment Configuration](#-environment-configuration)
- [Complete Repository Summary](#-complete-repository-summary)

---

## 🔄 CI/CD Directory Structure

### Complete CI/CD Structure

```
api-starter/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                           # Continuous Integration
│   │   ├── release.yml                      # Release automation
│   │   ├── docs.yml                         # Documentation build
│   │   ├── security.yml                     # Security scanning
│   │   └── deploy.yml                       # Deployment workflow
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   ├── feature_request.md
│   │   └── documentation.md
│   ├── PULL_REQUEST_TEMPLATE.md
│   ├── CODEOWNERS
│   └── dependabot.yml
│
├── docker/
│   ├── Dockerfile                           # Production image
│   ├── Dockerfile.dev                       # Development image
│   ├── docker-compose.yml                   # Local development
│   ├── docker-compose.prod.yml              # Production compose
│   └── .dockerignore
│
├── k8s/                                     # Kubernetes manifests
│   ├── base/
│   ├── overlays/
│   └── monitoring/
│
└── scripts/
    ├── deploy.sh
    ├── migrate.sh
    └── health-check.sh
```

---

## 🤖 GitHub Actions Workflows

### `.github/workflows/ci.yml`

```yaml
# Continuous Integration Workflow
#
# Runs on every push and pull request to ensure code quality,
# run tests, and verify the build.
#
# Jobs:
#   - lint: Code quality checks
#   - test: Run test suite
#   - build: Build Docker image
#   - security: Security scanning

name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

env:
  PYTHON_VERSION: "3.11"
  POETRY_VERSION: "1.7.0"

jobs:
  # ============================================================================
  # Code Quality Checks
  # ============================================================================
  lint:
    name: Lint Code
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      
      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
          restore-keys: |
            ${{ runner.os }}-pip-
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install black ruff mypy
          pip install -e ".[dev]"
      
      - name: Run Black
        run: black --check api_starter tests
      
      - name: Run Ruff
        run: ruff check api_starter tests
      
      - name: Run MyPy
        run: mypy api_starter
  
  # ============================================================================
  # Test Suite
  # ============================================================================
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        python-version: ["3.11", "3.12"]
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_USER: testuser
          POSTGRES_PASSWORD: testpass
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
      
      redis:
        image: redis:7
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      
      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -e ".[test]"
      
      - name: Run tests
        env:
          DATABASE_URL: postgresql://testuser:testpass@localhost:5432/testdb
          REDIS_URL: redis://localhost:6379/0
        run: |
          pytest tests/ \
            -v \
            --cov=api_starter \
            --cov-report=xml \
            --cov-report=html \
            --cov-report=term-missing
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml
          flags: unittests
          name: codecov-umbrella
          fail_ci_if_error: true
  
  # ============================================================================
  # Build Docker Image
  # ============================================================================
  build:
    name: Build Docker Image
    runs-on: ubuntu-latest
    needs: [lint, test]
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      
      - name: Build image
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./docker/Dockerfile
          push: false
          tags: api-starter:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
  
  # ============================================================================
  # Security Scanning
  # ============================================================================
  security:
    name: Security Scan
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'
      
      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
      
      - name: Run Bandit security linter
        run: |
          pip install bandit
          bandit -r api_starter -f json -o bandit-report.json
      
      - name: Upload Bandit results
        uses: actions/upload-artifact@v3
        with:
          name: bandit-report
          path: bandit-report.json
```

### `.github/workflows/release.yml`

```yaml
# Release Workflow
#
# Automatically creates releases when version tags are pushed.
# Builds and publishes package to PyPI.

name: Release

on:
  push:
    tags:
      - 'v*.*.*'

env:
  PYTHON_VERSION: "3.11"

jobs:
  # ============================================================================
  # Create GitHub Release
  # ============================================================================
  release:
    name: Create Release
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Get version from tag
        id: tag_name
        run: echo "VERSION=${GITHUB_REF#refs/tags/v}" >> $GITHUB_OUTPUT
      
      - name: Extract changelog
        id: changelog
        run: |
          VERSION=${{ steps.tag_name.outputs.VERSION }}
          CHANGELOG=$(awk "/## \[${VERSION}\]/,/## \[/" CHANGELOG.md | sed '1d;$d')
          echo "CHANGELOG<<EOF" >> $GITHUB_OUTPUT
          echo "$CHANGELOG" >> $GITHUB_OUTPUT
          echo "EOF" >> $GITHUB_OUTPUT
      
      - name: Create GitHub Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ steps.tag_name.outputs.VERSION }}
          body: ${{ steps.changelog.outputs.CHANGELOG }}
          draft: false
          prerelease: false
  
  # ============================================================================
  # Publish to PyPI
  # ============================================================================
  publish:
    name: Publish to PyPI
    runs-on: ubuntu-latest
    needs: release
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      
      - name: Install build tools
        run: |
          python -m pip install --upgrade pip
          pip install build twine
      
      - name: Build package
        run: python -m build
      
      - name: Check package
        run: twine check dist/*
      
      - name: Publish to PyPI
        env:
          TWINE_USERNAME: __token__
          TWINE_PASSWORD: ${{ secrets.PYPI_TOKEN }}
        run: twine upload dist/*
  
  # ============================================================================
  # Build and Push Docker Image
  # ============================================================================
  docker:
    name: Build and Push Docker Image
    runs-on: ubuntu-latest
    needs: release
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: yourusername/api-starter
          tags: |
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=semver,pattern={{major}}
            type=raw,value=latest
      
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./docker/Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### `.github/workflows/docs.yml`

```yaml
# Documentation Build Workflow
#
# Builds and deploys documentation to GitHub Pages

name: Documentation

on:
  push:
    branches: [main]
    paths:
      - 'docs/**'
      - 'api_starter/**'
      - '.github/workflows/docs.yml'

jobs:
  build-and-deploy:
    name: Build and Deploy Docs
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      
      - name: Install dependencies
        run: |
          pip install -e ".[docs]"
      
      - name: Build documentation
        run: |
          cd docs
          sphinx-build -b html . _build
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs/_build
```

### `.github/dependabot.yml`

```yaml
# Dependabot configuration
#
# Automatically creates pull requests to update dependencies

version: 2
updates:
  # Python dependencies
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    open-pull-requests-limit: 10
    reviewers:
      - "yourusername"
    labels:
      - "dependencies"
      - "python"
    commit-message:
      prefix: "chore"
      include: "scope"
  
  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    reviewers:
      - "yourusername"
    labels:
      - "dependencies"
      - "github-actions"
  
  # Docker
  - package-ecosystem: "docker"
    directory: "/docker"
    schedule:
      interval: "weekly"
      day: "monday"
    reviewers:
      - "yourusername"
    labels:
      - "dependencies"
      - "docker"
```

### `.github/PULL_REQUEST_TEMPLATE.md`

```markdown
## Description

<!-- Provide a brief description of the changes -->

## Type of Change

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Code refactoring
- [ ] Test updates

## Related Issues

<!-- Link to related issues using #issue_number -->

Closes #

## Changes Made

<!-- List the main changes -->

- 
- 
- 

## Testing

<!-- Describe the tests you ran -->

- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist

- [ ] My code follows the project's code style
- [ ] I have performed a self-review of my code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published

## Screenshots (if applicable)

<!-- Add screenshots to help explain your changes -->

## Additional Notes

<!-- Any additional information -->
```

---

## 🐳 Docker Configuration

### `docker/Dockerfile`

```dockerfile
# Production Dockerfile for API Starter
#
# Multi-stage build for optimal image size and security
#
# Build: docker build -f docker/Dockerfile -t api-starter:latest .
# Run: docker run -p 8000:8000 api-starter:latest

# ============================================================================
# Stage 1: Builder
# ============================================================================
FROM python:3.11-slim as builder

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Create virtual environment
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Copy requirements and install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY api_starter /app/api_starter
COPY setup.py /app/
WORKDIR /app

# Install application
RUN pip install --no-cache-dir -e .

# ============================================================================
# Stage 2: Runtime
# ============================================================================
FROM python:3.11-slim

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PATH="/opt/venv/bin:$PATH"

# Install runtime dependencies only
RUN apt-get update && apt-get install -y --no-install-recommends \
    libpq5 \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user
RUN groupadd -r apiuser && useradd -r -g apiuser apiuser

# Copy virtual environment from builder
COPY --from=builder /opt/venv /opt/venv

# Copy application
COPY --from=builder /app /app

# Create directories and set permissions
RUN mkdir -p /app/logs /app/data && \
    chown -R apiuser:apiuser /app

# Switch to non-root user
USER apiuser

# Set working directory
WORKDIR /app

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# Run application
CMD ["uvicorn", "api_starter.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### `docker/Dockerfile.dev`

```dockerfile
# Development Dockerfile for API Starter
#
# Includes development tools and hot reload
#
# Build: docker build -f docker/Dockerfile.dev -t api-starter:dev .
# Run: docker run -v $(pwd):/app -p 8000:8000 api-starter:dev

FROM python:3.11-slim

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    git \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy requirements
COPY requirements.txt requirements-dev.txt ./

# Install Python dependencies
RUN pip install --upgrade pip && \
    pip install -r requirements.txt && \
    pip install -r requirements-dev.txt

# Copy application (will be overridden by volume mount)
COPY . .

# Install in editable mode
RUN pip install -e .

# Expose port
EXPOSE 8000

# Run with auto-reload
CMD ["uvicorn", "api_starter.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
```

### `docker/docker-compose.yml`

```yaml
# Docker Compose for local development
#
# Usage:
#   docker-compose up -d          # Start all services
#   docker-compose logs -f api    # View logs
#   docker-compose down           # Stop all services

version: '3.8'

services:
  # ==========================================================================
  # API Service
  # ==========================================================================
  api:
    build:
      context: ..
      dockerfile: docker/Dockerfile.dev
    container_name: api-starter-api
    ports:
      - "8000:8000"
    volumes:
      - ..:/app
      - /app/.venv  # Exclude virtual environment
    environment:
      - DEBUG=true
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/apidb
      - REDIS_URL=redis://redis:6379/0
      - SECRET_KEY=dev-secret-key-change-in-production
    depends_on:
      - postgres
      - redis
    networks:
      - api-network
    restart: unless-stopped
  
  # ==========================================================================
  # PostgreSQL Database
  # ==========================================================================
  postgres:
    image: postgres:15-alpine
    container_name: api-starter-postgres
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=apidb
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init-db.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - api-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
  
  # ==========================================================================
  # Redis Cache
  # ==========================================================================
  redis:
    image: redis:7-alpine
    container_name: api-starter-redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - api-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
  
  # ==========================================================================
  # Celery Worker
  # ==========================================================================
  celery-worker:
    build:
      context: ..
      dockerfile: docker/Dockerfile.dev
    container_name: api-starter-celery
    command: celery -A api_starter.celery worker --loglevel=info
    volumes:
      - ..:/app
    environment:
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/apidb
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/1
      - CELERY_RESULT_BACKEND=redis://redis:6379/2
    depends_on:
      - postgres
      - redis
    networks:
      - api-network
    restart: unless-stopped
  
  # ==========================================================================
  # Celery Beat (Scheduler)
  # ==========================================================================
  celery-beat:
    build:
      context: ..
      dockerfile: docker/Dockerfile.dev
    container_name: api-starter-celery-beat
    command: celery -A api_starter.celery beat --loglevel=info
    volumes:
      - ..:/app
    environment:
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/apidb
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/1
    depends_on:
      - postgres
      - redis
    networks:
      - api-network
    restart: unless-stopped

# ==========================================================================
# Volumes
# ==========================================================================
volumes:
  postgres-data:
    driver: local
  redis-data:
    driver: local

# ==========================================================================
# Networks
# ==========================================================================
networks:
  api-network:
    driver: bridge
```

### `docker/.dockerignore`

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# Virtual environments
venv/
env/
ENV/
.venv

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# Testing
.pytest_cache/
.coverage
htmlcov/
.tox/

# Documentation
docs/_build/

# Git
.git/
.gitignore

# Docker
docker-compose.yml
Dockerfile*

# CI/CD
.github/

# Miscellaneous
*.log
.DS_Store
.env
.env.local
*.md
LICENSE
```

---

## ☸️ Kubernetes Deployment

### `k8s/base/deployment.yaml`

```yaml
# Kubernetes Deployment for API Starter
#
# Defines the application deployment with replicas, health checks,
# and resource limits.

apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-starter
  labels:
    app: api-starter
    component: api
    version: v1.0.0
spec:
  replicas: 3
  revisionHistoryLimit: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: api-starter
      component: api
  template:
    metadata:
      labels:
        app: api-starter
        component: api
        version: v1.0.0
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9090"
        prometheus.io/path: "/metrics"
    spec:
      # Security context
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      
      # Anti-affinity for high availability
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
                  - api-starter
              topologyKey: kubernetes.io/hostname
      
      # Init containers
      initContainers:
      - name: wait-for-postgres
        image: busybox:1.35
        command:
        - sh
        - -c
        - |
          until nc -z postgres-service 5432; do
            echo "Waiting for PostgreSQL..."
            sleep 2
          done
      
      # Main container
      containers:
      - name: api
        image: yourusername/api-starter:latest
        imagePullPolicy: IfNotPresent
        
        ports:
        - name: http
          containerPort: 8000
          protocol: TCP
        - name: metrics
          containerPort: 9090
          protocol: TCP
        
        # Environment variables
        env:
        - name: NODE_ENV
          value: "production"
        - name: DEBUG
          value: "false"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: api-starter-secrets
              key: database-url
        - name: REDIS_URL
          valueFrom:
            configMapKeyRef:
              name: api-starter-config
              key: redis-url
        - name: SECRET_KEY
          valueFrom:
            secretKeyRef:
              name: api-starter-secrets
              key: secret-key
        
        # Resource limits
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        
        # Liveness probe
        livenessProbe:
          httpGet:
            path: /health
            port: http
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
          successThreshold: 1
        
        # Readiness probe
        readinessProbe:
          httpGet:
            path: /health/ready
            port: http
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 2
          successThreshold: 1
        
        # Startup probe
        startupProbe:
          httpGet:
            path: /health
            port: http
          initialDelaySeconds: 0
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 30
        
        # Lifecycle hooks
        lifecycle:
          preStop:
            exec:
              command:
              - sh
              - -c
              - sleep 15
        
        # Security context
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
          capabilities:
            drop:
            - ALL
        
        # Volume mounts
        volumeMounts:
        - name: tmp
          mountPath: /tmp
        - name: cache
          mountPath: /app/.cache
      
      # Volumes
      volumes:
      - name: tmp
        emptyDir: {}
      - name: cache
        emptyDir: {}
```

### `k8s/base/service.yaml`

```yaml
# Kubernetes Service for API Starter
#
# Exposes the API deployment within the cluster

apiVersion: v1
kind: Service
metadata:
  name: api-starter-service
  labels:
    app: api-starter
    component: api
spec:
  type: ClusterIP
  selector:
    app: api-starter
    component: api
  ports:
  - name: http
    port: 80
    targetPort: 8000
    protocol: TCP
  - name: metrics
    port: 9090
    targetPort: 9090
    protocol: TCP
  sessionAffinity: None
```

### `k8s/base/configmap.yaml`

```yaml
# ConfigMap for API Starter
#
# Non-sensitive configuration values

apiVersion: v1
kind: ConfigMap
metadata:
  name: api-starter-config
  labels:
    app: api-starter
data:
  # Application settings
  APP_NAME: "API Starter"
  APP_VERSION: "1.0.0"
  LOG_LEVEL: "INFO"
  
  # External service URLs
  redis-url: "redis://redis-service:6379/0"
  
  # Feature flags
  ENABLE_METRICS: "true"
  ENABLE_TRACING: "true"
  
  # CORS settings
  CORS_ORIGINS: "https://app.example.com,https://www.example.com"
```

### `k8s/base/secret.yaml`

```yaml
# Secret for API Starter
#
# Sensitive configuration values
# 
# Note: In production, use a secrets management system like:
#   - HashiCorp Vault
#   - AWS Secrets Manager
#   - Google Secret Manager
#   - Azure Key Vault

apiVersion: v1
kind: Secret
metadata:
  name: api-starter-secrets
  labels:
    app: api-starter
type: Opaque
stringData:
  # Database connection (base64 encoded in production)
  database-url: "postgresql://user:password@postgres-service:5432/apidb"
  
  # Application secrets
  secret-key: "change-this-in-production-to-a-secure-random-string"
  jwt-secret: "change-this-jwt-secret-in-production"
  
  # External service credentials
  sendgrid-api-key: "your-sendgrid-api-key"
  aws-access-key: "your-aws-access-key"
  aws-secret-key: "your-aws-secret-key"
```

### `k8s/overlays/production/hpa.yaml`

```yaml
# Horizontal Pod Autoscaler for production
#
# Automatically scales the number of pods based on metrics

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-starter-hpa
  labels:
    app: api-starter
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-starter
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
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
      - type: Pods
        value: 2
        periodSeconds: 60
      selectPolicy: Min
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 30
      - type: Pods
        value: 4
        periodSeconds: 30
      selectPolicy: Max
```

### `k8s/overlays/production/kustomization.yaml`

```yaml
# Kustomize configuration for production environment
#
# Apply with: kubectl apply -k k8s/overlays/production/

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: production

resources:
- ../../base
- hpa.yaml

# Production-specific patches
patchesStrategicMerge:
- patches.yaml

# Image configuration
images:
- name: yourusername/api-starter
  newTag: v1.0.0

# Common labels
commonLabels:
  environment: production
  managed-by: kustomize

# Config generation
configMapGenerator:
- name: api-starter-config
  behavior: merge
  literals:
  - LOG_LEVEL=WARNING

# Replicas
replicas:
- name: api-starter
  count: 5
```

---

## 📜 Deployment Scripts

### `scripts/deploy.sh`

```bash
#!/bin/bash
# Deployment script for API Starter
#
# Usage:
#   ./scripts/deploy.sh <environment> <version>
#
# Example:
#   ./scripts/deploy.sh production v1.0.0

set -e  # Exit on error
set -u  # Exit on undefined variable
set -o pipefail  # Exit on pipe failure

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Configuration
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_ROOT="$(dirname "$SCRIPT_DIR")"
ENVIRONMENT="${1:-staging}"
VERSION="${2:-latest}"

# Functions
log_info() {
    echo -e "${GREEN}[INFO]${NC} $1"
}

log_warn() {
    echo -e "${YELLOW}[WARN]${NC} $1"
}

log_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

# Validate environment
validate_environment() {
    if [[ ! "$ENVIRONMENT" =~ ^(development|staging|production)$ ]]; then
        log_error "Invalid environment: $ENVIRONMENT"
        log_error "Valid options: development, staging, production"
        exit 1
    fi
    log_info "Deploying to: $ENVIRONMENT"
}

# Build Docker image
build_image() {
    log_info "Building Docker image..."
    docker build \
        -f "$PROJECT_ROOT/docker/Dockerfile" \
        -t "api-starter:$VERSION" \
        "$PROJECT_ROOT"
    
    log_info "Tagging image..."
    docker tag "api-starter:$VERSION" "yourusername/api-starter:$VERSION"
    docker tag "api-starter:$VERSION" "yourusername/api-starter:latest"
}

# Push Docker image
push_image() {
    log_info "Pushing Docker image to registry..."
    docker push "yourusername/api-starter:$VERSION"
    docker push "yourusername/api-starter:latest"
}

# Run database migrations
run_migrations() {
    log_info "Running database migrations..."
    kubectl exec -it deployment/api-starter -n "$ENVIRONMENT" -- \
        alembic upgrade head
}

# Deploy to Kubernetes
deploy_kubernetes() {
    log_info "Deploying to Kubernetes..."
    
    # Apply Kustomize configuration
    kubectl apply -k "$PROJECT_ROOT/k8s/overlays/$ENVIRONMENT"
    
    # Wait for rollout
    log_info "Waiting for rollout to complete..."
    kubectl rollout status deployment/api-starter -n "$ENVIRONMENT" --timeout=5m
}

# Health check
health_check() {
    log_info "Performing health check..."
    
    # Get service endpoint
    ENDPOINT=$(kubectl get service api-starter-service -n "$ENVIRONMENT" \
        -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    
    if [ -z "$ENDPOINT" ]; then
        ENDPOINT="localhost:8000"  # Fallback for development
    fi
    
    # Check health endpoint
    MAX_RETRIES=30
    RETRY_COUNT=0
    
    while [ $RETRY_COUNT -lt $MAX_RETRIES ]; do
        if curl -f -s "http://$ENDPOINT/health" > /dev/null; then
            log_info "Health check passed!"
            return 0
        fi
        
        RETRY_COUNT=$((RETRY_COUNT + 1))
        log_warn "Health check failed, retrying... ($RETRY_COUNT/$MAX_RETRIES)"
        sleep 10
    done
    
    log_error "Health check failed after $MAX_RETRIES attempts"
    return 1
}

# Rollback on failure
rollback() {
    log_error "Deployment failed, initiating rollback..."
    kubectl rollout undo deployment/api-starter -n "$ENVIRONMENT"
    kubectl rollout status deployment/api-starter -n "$ENVIRONMENT"
    log_info "Rollback completed"
}

# Main deployment process
main() {
    log_info "Starting deployment of API Starter"
    log_info "Environment: $ENVIRONMENT"
    log_info "Version: $VERSION"
    
    # Validate
    validate_environment
    
    # Build and push
    build_image
    push_image
    
    # Deploy
    deploy_kubernetes
    
    # Run migrations
    if [ "$ENVIRONMENT" = "production" ]; then
        run_migrations
    fi
    
    # Verify deployment
    if health_check; then
        log_info "Deployment completed successfully! 🎉"
    else
        rollback
        exit 1
    fi
}

# Trap errors
trap 'log_error "Deployment failed at line $LINENO"; rollback' ERR

# Run main
main
```

### `scripts/health-check.sh`

```bash
#!/bin/bash
# Health check script for API Starter
#
# Usage: ./scripts/health-check.sh <url>

set -e

URL="${1:-http://localhost:8000}"
MAX_RETRIES=5
RETRY_DELAY=2

echo "Checking health of API at: $URL"

for i in $(seq 1 $MAX_RETRIES); do
    if curl -f -s "$URL/health" > /dev/null; then
        echo "✓ API is healthy"
        exit 0
    else
        echo "✗ Health check failed (attempt $i/$MAX_RETRIES)"
        if [ $i -lt $MAX_RETRIES ]; then
            sleep $RETRY_DELAY
        fi
    fi
done

echo "✗ API health check failed after $MAX_RETRIES attempts"
exit 1
```

---

## 📊 Complete Repository Summary

### Final Directory Structure

```
api-starter/
├── api_starter/                     # Main package
│   ├── __init__.py
│   ├── __version__.py
│   ├── core/                        # Core framework
│   ├── models/                      # Base models
│   ├── utils/                       # Utilities
│   ├── plugins/                     # Built-in plugins
│   └── middleware/                  # Middleware
│
├── examples/                        # Example applications
│   ├── simple/                      # Simple example
│   └── todo_api/                    # Complete Todo API
│
├── tests/                           # Test suite
│   ├── unit/                        # Unit tests
│   ├── integration/                 # Integration tests
│   ├── fixtures/                    # Test fixtures
│   └── conftest.py                  # Pytest configuration
│
├── docs/                            # Documentation
│   ├── getting-started/
│   ├── user-guide/
│   ├── api-reference/
│   ├── tutorials/
│   └── conf.py                      # Sphinx config
│
├── .github/                         # GitHub configuration
│   ├── workflows/                   # CI/CD workflows
│   │   ├── ci.yml
│   │   ├── release.yml
│   │   └── docs.yml
│   └── PULL_REQUEST_TEMPLATE.md
│
├── docker/                          # Docker configuration
│   ├── Dockerfile
│   ├── Dockerfile.dev
│   └── docker-compose.yml
│
├── k8s/                             # Kubernetes manifests
│   ├── base/
│   └── overlays/
│
├── scripts/                         # Deployment scripts
│   ├── deploy.sh
│   └── health-check.sh
│
├── .gitignore
├── .dockerignore
├── setup.py                         # Package setup
├── pyproject.toml                   # Project configuration
├── requirements.txt                 # Dependencies
├── requirements-dev.txt             # Dev dependencies
├── README.md                        # Main README
├── CONTRIBUTING.md                  # Contribution guide
├── LICENSE                          # License file
└── CHANGELOG.md                     # Version history
```

---

## 🎉 Congratulations!

You now have a **complete, production-ready repository structure** with:

✅ **Comprehensive docstrings** on all code  
✅ **Full type hints** throughout  
✅ **Complete test infrastructure**  
✅ **CI/CD pipelines** for automation  
✅ **Docker containerization**  
✅ **Kubernetes deployment**  
✅ **Documentation site**  
✅ **Deployment scripts**  

This structure follows **industry best practices** and is ready for:
- Team collaboration
- Production deployment
- Open source contribution
- Enterprise use

### Next Steps

1. **Clone and customize** for your project
2. **Update configurations** with your values
3. **Deploy to your infrastructure**
4. **Build something amazing!** 🚀

---

[↑ Back to TOC](#-table-of-contents)

**Repository Structure Guide Complete!** 🎊
