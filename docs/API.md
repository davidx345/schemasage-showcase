#  API Reference & Examples

## Core API Endpoints

### Authentication Service

#### POST `/api/auth/signup`
Create a new user account.

```json
{
  "username": "john_doe",
  "password": "SecurePass123!",
  "is_admin": false
}
```

**Response:**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "token_type": "bearer",
  "expires_in": 86400
}
```

#### POST `/api/auth/login`
Authenticate user and receive JWT token.

```json
{
  "username": "john_doe",
  "password": "SecurePass123!"
}
```

#### GET `/api/auth/me`
Get current user profile (requires authentication).

**Headers:** `Authorization: Bearer <token>`

---

### Schema Detection Service

#### POST `/api/schema/detect`
Detect schema from structured data.

```json
{
  "data": "{\"users\": [{\"id\": 1, \"name\": \"John\", \"email\": \"john@example.com\"}]}",
  "settings": {
    "detect_relations": true,
    "infer_types": true,
    "enable_ai_enhancement": true
  },
  "format_hint": "json"
}
```

**Response:**
```json
{
  "detected_schema": {
    "tables": [
      {
        "name": "users",
        "columns": [
          {
            "name": "id",
            "type": "INTEGER",
            "nullable": false,
            "is_primary_key": true
          },
          {
            "name": "name",
            "type": "VARCHAR",
            "nullable": true,
            "max_length": 255
          }
        ]
      }
    ],
    "relationships": [],
    "confidence": 0.95
  },
  "success": true
}
```

#### POST `/api/schema/detect/file`
Upload and analyze file for schema detection.

**Form Data:**
- `file`: CSV, JSON, XML, or YAML file
- `table_name`: Optional table name
- `enable_ai_enhancement`: Boolean

#### POST `/api/schema/relationships/cross-dataset`
Analyze relationships across multiple datasets.

```json
{
  "datasets": [
    [
      {
        "name": "users",
        "columns": [
          {"name": "id", "type": "INTEGER"},
          {"name": "company_id", "type": "INTEGER"}
        ]
      }
    ],
    [
      {
        "name": "companies",
        "columns": [
          {"name": "id", "type": "INTEGER"},
          {"name": "name", "type": "VARCHAR"}
        ]
      }
    ]
  ]
}
```

---

### Code Generation Service

#### POST `/api/generate`
Generate code from database schema.

```json
{
  "db_schema": {
    "tables": [
      {
        "name": "users",
        "columns": [
          {
            "name": "id",
            "type": "INTEGER",
            "is_primary_key": true,
            "nullable": false
          }
        ]
      }
    ]
  },
  "format": "sqlalchemy",
  "options": {
    "use_mypy": true,
    "base_class": "Base"
  }
}
```

**Response:**
```json
{
  "code": "from sqlalchemy import Column, Integer, String\nfrom sqlalchemy.ext.declarative import declarative_base\n\nBase = declarative_base()\n\nclass Users(Base):\n    __tablename__ = 'users'\n    \n    id = Column(Integer, primary_key=True)",
  "format": "sqlalchemy",
  "generated_at": "2025-10-07T10:30:00Z",
  "metadata": {
    "table_count": 1,
    "column_count": 1
  }
}
```

#### GET `/api/generate/formats`
Get list of supported code generation formats.

**Response:**
```json
{
  "formats": [
    {
      "value": "sqlalchemy",
      "name": "SQLAlchemy",
      "description": "SQLAlchemy ORM models with relationships"
    },
    {
      "value": "prisma",
      "name": "Prisma",
      "description": "Prisma schema with relations and types"
    }
  ]
}
```

#### POST `/api/generate/schema-from-description`
Generate schema from natural language description.

```json
{
  "description": "Create a blog system with users, posts, and comments. Users can write multiple posts, and posts can have many comments.",
  "options": {
    "include_relationships": true,
    "add_timestamps": true
  }
}
```

---

### Database Migration Service

#### POST `/api/database/test-connection`
Test database connectivity.

```json
{
  "database_type": "postgresql",
  "connection_config": {
    "host": "localhost",
    "port": 5432,
    "database": "mydb",
    "username": "user",
    "password": "password"
  }
}
```

**Response:**
```json
{
  "status": "success",
  "connection_id": "conn_abc123",
  "database_info": {
    "version": "PostgreSQL 14.2",
    "size_mb": 1024,
    "table_count": 15
  },
  "response_time_ms": 150
}
```

#### POST `/api/database/import/schema`
Import schema from existing database.

```json
{
  "database_type": "postgresql",
  "connection_config": {
    "host": "prod-db.company.com",
    "port": 5432,
    "database": "production",
    "username": "readonly_user",
    "password": "secure_password"
  },
  "import_options": {
    "include_data_samples": true,
    "analyze_relationships": true,
    "max_sample_rows": 1000
  }
}
```

#### POST `/api/database/cloud/migration-plan`
Create cloud migration plan.

```json
{
  "source_database": {
    "type": "postgresql",
    "connection_url": "postgresql://user:pass@localhost:5432/mydb"
  },
  "target_cloud": {
    "provider": "aws",
    "region": "us-east-1",
    "instance_type": "db.t3.medium",
    "storage_gb": 100
  },
  "migration_strategy": "replatform",
  "requirements": {
    "downtime_tolerance_hours": 2,
    "backup_required": true,
    "compliance_requirements": ["SOC2", "GDPR"]
  }
}
```

---

### Project Management Service

#### POST `/api/projects`
Create a new project.

```json
{
  "name": "E-commerce Database",
  "description": "Customer and order management system",
  "project_type": "database_design",
  "team_members": ["user1", "user2"],
  "tags": ["postgresql", "e-commerce"]
}
```

#### GET `/api/projects/{project_id}`
Get project details.

**Response:**
```json
{
  "id": "proj_123",
  "name": "E-commerce Database",
  "description": "Customer and order management system",
  "owner": "john_doe",
  "created_at": "2025-10-01T10:00:00Z",
  "schemas": [
    {
      "id": "schema_456",
      "name": "Customer Schema",
      "version": 3,
      "last_modified": "2025-10-07T09:30:00Z"
    }
  ],
  "team_members": [
    {
      "username": "alice",
      "role": "editor",
      "joined_at": "2025-10-02T14:00:00Z"
    }
  ]
}
```

#### POST `/api/projects/{project_id}/comments`
Add comment to project.

```json
{
  "content": "The user table needs an index on email column for better performance.",
  "type": "suggestion",
  "refers_to": "table:users"
}
```

---

### AI Chat Service

#### POST `/api/chat`
Get AI assistance for schema-related questions.

```json
{
  "db_schema": {
    "tables": [
      {
        "name": "users",
        "columns": [
          {"name": "id", "type": "INTEGER"},
          {"name": "email", "type": "VARCHAR"}
        ]
      }
    ]
  },
  "messages": [
    {
      "role": "user",
      "content": "How can I optimize this schema for better performance?"
    }
  ],
  "question": "What indexes should I add?"
}
```

**Response:**
```json
{
  "response": "For the users table, I recommend adding these indexes:\n1. Unique index on email column for login queries\n2. Consider partial indexes if you have soft deletes\n3. Add composite indexes for frequently queried column combinations",
  "ai_provider": "openai",
  "context_used": true,
  "suggestions": [
    "CREATE UNIQUE INDEX idx_users_email ON users(email);",
    "CREATE INDEX idx_users_active ON users(id) WHERE deleted_at IS NULL;"
  ]
}
```

---

### WebSocket Real-time Service

#### WS `/ws/{user_id}`
Connect to real-time updates.

**Connection Message:**
```json
{
  "type": "auth",
  "token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "user_id": "user123"
}
```

**Incoming Messages:**
```json
{
  "type": "stats_update",
  "data": {
    "totalConnections": 42,
    "activeUsers": 15,
    "totalSchemas": 1250,
    "schemasGenerated": 1180,
    "totalAPIs": 890,
    "systemHealth": "healthy"
  }
}
```

```json
{
  "type": "activity_update",
  "data": {
    "id": "activity_789",
    "activity": "Schema Generated",
    "user": "alice",
    "project": "E-commerce DB",
    "details": "SQLAlchemy models for 5 tables",
    "timestamp": "2025-10-07T10:45:00Z",
    "icon": "database"
  }
}
```

---

## Error Responses

All API endpoints return consistent error responses:

```json
{
  "message": "Validation error",
  "error_code": "VALIDATION_ERROR",
  "details": {
    "field": "email",
    "issue": "Invalid email format"
  },
  "timestamp": "2025-10-07T10:30:00Z"
}
```

### Common HTTP Status Codes

- `200`: Success
- `201`: Created
- `400`: Bad Request (validation error)
- `401`: Unauthorized (authentication required)
- `403`: Forbidden (insufficient permissions)
- `404`: Not Found
- `429`: Too Many Requests (rate limited)
- `500`: Internal Server Error

## Authentication

Most endpoints require authentication. Include the JWT token in the Authorization header:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...
```

## Rate Limiting

API requests are rate limited to prevent abuse:

- **Authentication endpoints**: 5 requests per minute
- **Schema detection**: 10 requests per minute
- **Code generation**: 20 requests per minute
- **Other endpoints**: 100 requests per minute

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1633612800
```

## Pagination

List endpoints support pagination:

```
GET /api/projects?page=1&limit=20&sort=created_at&order=desc
```

Response includes pagination metadata:

```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 156,
    "pages": 8,
    "has_next": true,
    "has_prev": false
  }
}
```

## SDK Examples

### Python SDK Usage

```python
from schemasage import SchemaSageClient

# Initialize client
client = SchemaSageClient(
    api_key="your_jwt_token",
    base_url="https://schemasage-api-gateway.herokuapp.com"
)

# Detect schema from data
schema = await client.schema.detect({
    "users": [
        {"id": 1, "name": "John", "email": "john@example.com"}
    ]
})

# Generate SQLAlchemy code
code = await client.codegen.generate(
    schema=schema,
    format="sqlalchemy",
    options={"use_mypy": True}
)

print(code.code)
```

### JavaScript SDK Usage

```javascript
import { SchemaSageClient } from '@schemasage/sdk';

const client = new SchemaSageClient({
  apiKey: 'your_jwt_token',
  baseUrl: 'https://schemasage-api-gateway.herokuapp.com'
});

// Detect schema from JSON
const schema = await client.schema.detect({
  data: JSON.stringify(userData),
  settings: {
    detect_relations: true,
    enable_ai_enhancement: true
  }
});

// Generate TypeScript interfaces
const code = await client.codegen.generate({
  db_schema: schema,
  format: 'typescript_interfaces'
});

console.log(code.code);
```