#  SchemaSage Architecture Documentation

## System Overview

SchemaSage is built as a **distributed microservices platform** designed for enterprise-scale schema intelligence and database migration. The architecture emphasizes scalability, security, and real-time collaboration.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Client Layer                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│  Web Application (Next.js 14)                                              │
│  ├── Dashboard & Analytics                                                 │
│  ├── Schema Editor & Visualizer                                            │
│  ├── Migration Planning Interface                                          │
│  ├── Code Generation Studio                                                │
│  └── Real-time Collaboration Tools                                         │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │ HTTPS/WSS
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                             API Gateway Layer                              │
├─────────────────────────────────────────────────────────────────────────────┤
│  API Gateway (FastAPI)                                                     │
│  ├── Request Routing & Load Balancing                                      │
│  ├── Authentication & Authorization                                        │
│  ├── Rate Limiting & CORS                                                  │
│  ├── Request/Response Transformation                                       │
│  └── Service Discovery & Health Checks                                     │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Microservices Layer                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │  Authentication │  │ Schema Detection│  │ Code Generation │            │
│  │                 │  │                 │  │                 │            │
│  │ • JWT/OAuth2    │  │ • AI Analysis   │  │ • Multi-Language│            │
│  │ • User Mgmt     │  │ • File Parsing  │  │ • Template Eng. │            │
│  │ • Role Control  │  │ • Relationships │  │ • API Scaffolds │            │
│  │ • Sessions      │  │ • Data Quality  │  │ • NL to Code    │            │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘            │
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │ Database        │  │ Project         │  │ WebSocket       │            │
│  │ Migration       │  │ Management      │  │ Real-time       │            │
│  │                 │  │                 │  │                 │            │
│  │ • Multi-Cloud   │  │ • Teams/Collab  │  │ • Live Updates  │            │
│  │ • Schema Sync   │  │ • Workflows     │  │ • Push Notifs   │            │
│  │ • Automation    │  │ • Integrations  │  │ • Activity Feed │            │
│  │ • IaC Templates │  │ • Compliance    │  │ • Broadcasting  │            │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘            │
│                                                                             │
│  ┌─────────────────┐                                                       │
│  │ AI Chat         │                                                       │
│  │                 │                                                       │
│  │ • GPT-4 Integration                                                     │
│  │ • Gemini Support                                                        │
│  │ • Context-Aware                                                         │
│  │ • Multi-Provider                                                        │
│  └─────────────────┘                                                       │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Data & Integration Layer                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │ PostgreSQL      │  │ Redis Cache     │  │ AI Services     │            │
│  │                 │  │                 │  │                 │            │
│  │ • User Data     │  │ • Sessions      │  │ • OpenAI GPT-4  │            │
│  │ • Projects      │  │ • Rate Limits   │  │ • Google Gemini │            │
│  │ • Schema History│  │ • WebSocket     │  │ • Custom Models │            │
│  │ • Audit Logs    │  │ • Job Queue     │  │ • Vector Store  │            │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘            │
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │ Cloud Providers │  │ File Storage    │  │ External APIs   │            │
│  │                 │  │                 │  │                 │            │
│  │ • AWS Services  │  │ • S3/CloudStore │  │ • Webhooks      │            │
│  │ • Azure Cloud   │  │ • File Uploads  │  │ • Integrations  │            │
│  │ • Google Cloud  │  │ • Backups       │  │ • Third-party   │            │
│  │ • Database SVCs │  │ • Assets        │  │ • Notifications │            │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘            │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Service Details

### 1. Authentication Service
**Technology**: FastAPI, PostgreSQL, JWT, OAuth2  
**Responsibilities**:
- User registration and login
- JWT token generation and validation
- OAuth2 integration (Google)
- Role-based access control
- Session management
- Rate limiting

### 2. Schema Detection Service
**Technology**: FastAPI, AI Integration (GPT-4/Gemini), Pandas  
**Responsibilities**:
- Multi-format file parsing (JSON, CSV, XML, YAML)
- AI-powered schema inference
- Cross-dataset relationship discovery
- Data quality assessment
- PII detection and compliance scanning
- Schema versioning and history

### 3. Code Generation Service
**Technology**: FastAPI, Jinja2, AI Integration  
**Responsibilities**:
- Multi-language code generation (9+ formats)
- Template engine management
- API scaffolding (FastAPI, Express.js, Spring Boot)
- Natural language to code conversion
- Custom template creation
- Code optimization suggestions

### 4. Database Migration Service
**Technology**: FastAPI, SQLAlchemy, Cloud SDKs  
**Responsibilities**:
- Multi-database connectivity (PostgreSQL, MySQL, etc.)
- Cloud migration planning (AWS, Azure, GCP)
- Schema and data migration
- Infrastructure-as-Code generation
- Migration progress tracking
- Rollback strategies

### 5. Project Management Service
**Technology**: FastAPI, PostgreSQL, S3 Integration  
**Responsibilities**:
- Multi-tenant project management
- Team collaboration features
- Workflow automation
- Integration management
- Compliance tracking
- Audit trail maintenance

### 6. WebSocket Real-time Service
**Technology**: FastAPI WebSockets, Redis  
**Responsibilities**:
- Real-time dashboard updates
- Live collaboration features
- Push notifications
- Activity broadcasting
- Connection management
- Statistics streaming

### 7. AI Chat Service
**Technology**: FastAPI, OpenAI API, Gemini API  
**Responsibilities**:
- Context-aware AI assistance
- Multi-provider AI integration
- Schema-specific help
- Code explanation and suggestions
- Intelligent recommendations

## Data Flow Architecture

### Schema Detection Flow
```
File Upload → Parser → AI Analysis → Relationship Discovery → Storage → Notification
     │           │         │              │                 │           │
     ▼           ▼         ▼              ▼                 ▼           ▼
Frontend → Detection → GPT-4/Gemini → Rule Engine → PostgreSQL → WebSocket
              Service                                         │
                                                             ▼
                                                        History Log
```

### Code Generation Flow
```
Schema Input → Template Selection → AI Enhancement → Code Generation → Delivery
     │              │                    │               │            │
     ▼              ▼                    ▼               ▼            ▼
Frontend → Generation Service → GPT-4 → Jinja2 → Generated Code → Download
              │                                      │
              ▼                                      ▼
        Template Store                         Version Control
```

### Migration Flow
```
Source DB → Analysis → Planning → Infrastructure → Migration → Validation
    │          │         │           │             │           │
    ▼          ▼         ▼           ▼             ▼           ▼
Connection → Migration → Cloud → IaC Generation → Data Sync → Testing
             Service     APIs                                      │
                                                                  ▼
                                                            Success Report
```

## Security Architecture

### Authentication & Authorization
- **JWT-based authentication** with refresh tokens
- **OAuth2 integration** (Google, extensible to others)
- **Role-based access control** (Admin, User, Viewer)
- **API key management** for service-to-service communication

### Data Security
- **AES-256 encryption** for sensitive data at rest
- **TLS 1.3** for all data in transit
- **Database encryption** with PostgreSQL native features
- **Secure environment variable** management

### Infrastructure Security
- **Container isolation** with Docker
- **Network segmentation** between services
- **Rate limiting** and DDoS protection
- **Security headers** and CORS policies
- **Regular security audits** and vulnerability scanning

## Scalability & Performance

### Horizontal Scaling
- **Microservices independence** allows individual scaling
- **Load balancing** through API Gateway
- **Database connection pooling** for optimal resource usage
- **Redis caching** for frequently accessed data

### Performance Optimization
- **Async/await patterns** throughout the codebase
- **Background job processing** for heavy operations
- **CDN integration** for static assets
- **Database indexing** and query optimization
- **Response caching** strategies

### Monitoring & Observability
- **Health check endpoints** for all services
- **Centralized logging** with structured JSON logs
- **Performance metrics** collection
- **Error tracking** and alerting
- **Real-time dashboard** monitoring

## Deployment Architecture

### Development Environment
```
Local Development → Docker Compose → Integration Testing → Staging
```

### Production Environment
```
GitHub → CI/CD Pipeline → Docker Registry → Heroku Deployment → Monitoring
```

### Infrastructure as Code
- **Docker containerization** for all services
- **Environment-specific configurations**
- **Automated deployment** pipelines
- **Blue-green deployment** strategies
- **Rollback capabilities**

## Technology Decisions & Rationale

### Why FastAPI?
- **High performance** async capabilities
- **Automatic API documentation** generation
- **Type safety** with Pydantic integration
- **Easy testing** and development experience

### Why Microservices?
- **Independent deployments** and scaling
- **Technology diversity** (different services can use different stacks)
- **Fault isolation** (one service failure doesn't bring down the system)
- **Team autonomy** and development speed

### Why PostgreSQL?
- **ACID compliance** for data integrity
- **Advanced features** (JSON support, full-text search)
- **Excellent performance** and reliability
- **Rich ecosystem** and tooling

### Why Dual AI Providers?
- **Redundancy** and reliability
- **Cost optimization** through intelligent routing
- **Feature diversity** (different AI models excel at different tasks)
- **Vendor independence** and risk mitigation

## Future Architecture Considerations

### Planned Enhancements
- **Kubernetes migration** for better orchestration
- **Event-driven architecture** with message queues
- **GraphQL integration** for flexible data fetching
- **Multi-region deployment** for global availability
- **Enhanced AI capabilities** with custom model training

### Scalability Roadmap
- **Auto-scaling** based on demand
- **Database sharding** for large-scale data
- **Caching layer optimization**
- **Performance profiling** and bottleneck identification