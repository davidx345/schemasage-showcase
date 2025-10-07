#  Technical Implementation Guide

## Service Implementation Details

### Authentication Service Architecture

```python
# Core Authentication Flow
@app.post("/login")
async def authenticate_user():
    # 1. Validate credentials
    # 2. Generate JWT token
    # 3. Set security headers
    # 4. Return user session
    pass

# OAuth2 Integration
@app.get("/google/callback")
async def google_oauth_callback():
    # 1. Exchange code for tokens
    # 2. Fetch user profile
    # 3. Create or update user
    # 4. Generate internal JWT
    pass
```

**Key Features**:
- JWT-based authentication with refresh tokens
- OAuth2 integration (Google)
- Rate limiting and security headers
- Role-based access control

### Schema Detection AI Pipeline

```python
# AI-Enhanced Schema Detection
class AISchemaEnhancer:
    async def suggest_relationships(self, tables, sample_data):
        # 1. Rule-based relationship detection
        rule_relationships = self._detect_rule_based_relationships(tables)
        
        # 2. AI-powered analysis with context
        enriched_summary = self._prepare_enriched_summary(tables, sample_data)
        ai_relationships = await self._suggest_relationships_with_ai(enriched_summary)
        
        # 3. Hybrid validation and scoring
        return self._validate_relationships(rule_relationships + ai_relationships, tables)
```

**AI Integration**:
- Dual provider support (OpenAI GPT-4 + Google Gemini)
- Intelligent fallback mechanisms
- Context-aware prompt engineering
- Hybrid rule-based + AI approach

### Code Generation Template Engine

```python
# Multi-Format Code Generation
class CodeGenerator:
    async def generate_code(self, schema, format, options):
        # 1. Load appropriate template
        template = self.env.get_template(self.format_templates[format])
        
        # 2. Prepare context with helper functions
        context = self._prepare_template_context(schema, options)
        
        # 3. Render with Jinja2
        code = await self._render_template(template, context)
        
        return CodeGenerationResult(code=code, metadata=metadata)
```

**Supported Formats**:
- SQLAlchemy, Prisma, TypeORM, Django ORM
- Raw SQL, JSON Schema, DBML
- TypeScript interfaces, Python dataclasses

### Database Migration Engine

```python
# Cloud Migration Orchestration
class CloudDatabaseManager:
    async def migrate_to_cloud(self, source_id, target_config, options):
        # 1. Analyze source schema
        source_metadata = await self.get_schema_metadata(source_id)
        
        # 2. Create migration plan
        migration_plan = self._create_migration_plan(source_metadata, target_config)
        
        # 3. Execute migration phases
        if options.get('execute_migration'):
            return await self._execute_migration(migration_plan)
        
        return migration_plan
```

**Migration Capabilities**:
- Multi-database support (PostgreSQL, MySQL, SQLite, Oracle, SQL Server, MongoDB)
- Cloud provider integration (AWS RDS, Azure Database, Google Cloud SQL)
- Infrastructure-as-Code generation (CloudFormation, Terraform)

### Real-time WebSocket Architecture

```python
# WebSocket Connection Management
class ConnectionManager:
    async def connect(self, websocket, user_id):
        await websocket.accept()
        self.active_connections[user_id].add(websocket)
        
        # Send initial stats after authentication
        stats = await get_current_stats()
        await websocket.send_text(json.dumps({
            "type": "stats_update",
            "data": stats
        }))
    
    async def broadcast_to_all(self, message):
        # Efficient broadcasting to all connected clients
        message_str = json.dumps(message)
        for user_connections in self.active_connections.values():
            for websocket in user_connections:
                await websocket.send_text(message_str)
```

**Real-time Features**:
- Live dashboard statistics
- Collaborative schema editing
- Push notifications
- Activity feeds and progress updates

## API Design Patterns

### RESTful API Structure

```
/api/auth/*              → Authentication Service
/api/schema/*            → Schema Detection Service  
/api/generate/*          → Code Generation Service
/api/database/*          → Database Migration Service
/api/projects/*          → Project Management Service
/api/chat/*              → AI Chat Service
/ws/*                    → WebSocket Real-time Service
```

### Error Handling Strategy

```python
# Consistent Error Response Format
@app.exception_handler(Exception)
async def global_exception_handler(request, exc):
    return JSONResponse(
        status_code=500,
        content={
            "message": "Internal server error",
            "error_code": "INTERNAL_ERROR",
            "details": {"error": str(exc)},
            "timestamp": datetime.now().isoformat()
        }
    )
```

### Request/Response Models

```python
# Pydantic Models for Type Safety
class CodeGenerationRequest(BaseModel):
    db_schema: SchemaResponse
    format: CodeGenFormat
    options: Optional[Dict[str, Any]] = None

class CodeGenerationResponse(BaseModel):
    code: str
    format: CodeGenFormat
    generated_at: datetime
    metadata: Dict[str, Any]
```

## Database Design

### Core Data Models

```sql
-- Users and Authentication
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE,
    hashed_password VARCHAR(255),
    google_id VARCHAR(255) UNIQUE,
    is_admin BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    last_login TIMESTAMP
);

-- Projects and Schemas
CREATE TABLE projects (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    description TEXT,
    owner_id INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Schema History and Versioning
CREATE TABLE schema_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    project_id UUID REFERENCES projects(id),
    schema_version INTEGER,
    schema_data JSONB,
    created_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Performance Optimizations

```sql
-- Indexes for Query Performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_projects_owner ON projects(owner_id);
CREATE INDEX idx_schema_history_project ON schema_history(project_id);
CREATE INDEX idx_schema_data_gin ON schema_history USING GIN(schema_data);
```

## Security Implementation

### JWT Token Management

```python
# Secure Token Generation
def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(hours=24)
    to_encode.update({
        "exp": expire,
        "iat": datetime.utcnow(),
        "type": "access"
    })
    return jwt.encode(to_encode, JWT_SECRET_KEY, algorithm="HS256")

# Token Validation Middleware
async def verify_token(credentials: HTTPAuthorizationCredentials):
    try:
        payload = jwt.decode(
            credentials.credentials, 
            JWT_SECRET_KEY, 
            algorithms=["HS256"]
        )
        return payload.get("sub")
    except jwt.ExpiredSignatureError:
        raise HTTPException(401, "Token expired")
    except jwt.InvalidTokenError:
        raise HTTPException(401, "Invalid token")
```

### Rate Limiting Implementation

```python
# IP-based Rate Limiting
class RateLimiter:
    def __init__(self, max_requests=100, window_seconds=60):
        self.max_requests = max_requests
        self.window_seconds = window_seconds
        self.requests = defaultdict(list)
    
    def is_allowed(self, identifier: str) -> bool:
        now = time.time()
        window_start = now - self.window_seconds
        
        # Clean old requests
        self.requests[identifier] = [
            req_time for req_time in self.requests[identifier]
            if req_time > window_start
        ]
        
        if len(self.requests[identifier]) >= self.max_requests:
            return False
        
        self.requests[identifier].append(now)
        return True
```

## AI Integration Architecture

### Multi-Provider AI Service

```python
# Intelligent AI Provider Selection
class AIService:
    async def get_ai_response(self, prompt, context=None):
        # Try OpenAI first
        if self.openai_available:
            try:
                return await self._call_openai(prompt, context)
            except Exception as e:
                logger.warning(f"OpenAI failed: {e}")
        
        # Fallback to Gemini
        if self.gemini_available:
            try:
                return await self._call_gemini(prompt, context)
            except Exception as e:
                logger.error(f"All AI providers failed: {e}")
                return self._fallback_response()
        
        raise AIServiceUnavailable("No AI providers available")
```

### Prompt Engineering

```python
# Context-Aware Prompt Construction
def build_relationship_prompt(tables, sample_data):
    return f"""
    You are an expert database analyst. Analyze these tables and their data 
    to identify semantic relationships:

    Tables: {format_tables(tables)}
    Sample Data: {format_sample_data(sample_data)}

    Focus on relationships that are NOT obvious from naming conventions 
    but are semantically meaningful. Consider:
    - Data patterns and value distributions
    - Business logic implications
    - Referential integrity possibilities

    Return valid JSON array with relationship suggestions.
    """
```

## Testing Strategy

### Unit Testing

```python
# Service Unit Tests
@pytest.mark.asyncio
async def test_schema_detection():
    detector = SchemaDetector()
    test_data = {"users": [{"id": 1, "name": "John"}]}
    
    result = await detector.detect_schema(
        data=json.dumps(test_data),
        table_name="users"
    )
    
    assert result.tables[0].name == "users"
    assert len(result.tables[0].columns) == 2
    assert result.tables[0].columns[0].name == "id"
```

### Integration Testing

```python
# API Integration Tests
@pytest.mark.asyncio
async def test_code_generation_api():
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post("/generate", json={
            "db_schema": sample_schema,
            "format": "sqlalchemy",
            "options": {}
        })
    
    assert response.status_code == 200
    assert "class" in response.json()["code"]
```

## Deployment Configuration

### Docker Containerization

```dockerfile
# Multi-stage build for optimization
FROM python:3.11-slim as builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY . .

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD python -c "import requests; requests.get('http://localhost:8000/health')" || exit 1

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Environment Configuration

```python
# Environment-specific settings
class Settings:
    # Database Configuration
    DATABASE_URL: str = os.getenv("DATABASE_URL")
    
    # AI Provider Configuration
    OPENAI_API_KEY: str = os.getenv("OPENAI_API_KEY", "")
    GEMINI_API_KEY: str = os.getenv("GEMINI_API_KEY", "")
    
    # Security Configuration
    JWT_SECRET_KEY: str = os.getenv("JWT_SECRET_KEY")
    
    # Service URLs
    REDIS_URL: str = os.getenv("REDIS_URL", "redis://localhost:6379")
    
    def is_ai_enhanced(self) -> bool:
        return bool(self.OPENAI_API_KEY or self.GEMINI_API_KEY)
```

## Monitoring & Observability

### Health Check Implementation

```python
# Comprehensive Health Checks
@app.get("/health")
async def health_check():
    checks = {
        "database": await check_database_connection(),
        "redis": await check_redis_connection(),
        "ai_services": await check_ai_providers(),
        "disk_space": check_disk_space(),
        "memory_usage": check_memory_usage()
    }
    
    overall_status = "healthy" if all(checks.values()) else "unhealthy"
    
    return {
        "status": overall_status,
        "checks": checks,
        "timestamp": datetime.now().isoformat(),
        "version": settings.SERVICE_VERSION
    }
```

### Logging Strategy

```python
# Structured Logging
import structlog

logger = structlog.get_logger()

# Usage throughout the application
logger.info(
    "Schema detection completed",
    user_id=user_id,
    tables_detected=len(tables),
    processing_time_ms=processing_time,
    ai_enhanced=ai_enabled
)
```

## Performance Optimization

### Async/Await Best Practices

```python
# Efficient async operations
async def process_multiple_schemas(schema_requests):
    # Process schemas concurrently
    tasks = [
        detect_schema(request) 
        for request in schema_requests
    ]
    
    results = await asyncio.gather(*tasks, return_exceptions=True)
    
    # Handle results and exceptions
    successful_results = [
        result for result in results 
        if not isinstance(result, Exception)
    ]
    
    return successful_results
```

### Caching Strategy

```python
# Redis-based caching
@cache(expire=3600)  # Cache for 1 hour
async def get_schema_metadata(connection_id: str):
    # Expensive database operation
    return await expensive_schema_analysis(connection_id)

# Cache invalidation
async def update_schema(schema_id: str, new_data):
    result = await update_database(schema_id, new_data)
    await cache.delete(f"schema_metadata:{schema_id}")
    return result
```

This technical documentation provides deep insights into the implementation details, architectural decisions, and engineering best practices used throughout the SchemaSage platform.