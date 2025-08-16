# Customer Support Platform

A comprehensive, enterprise-grade customer support platform with Telegram bot integration, real-time notifications, and multi-role dashboards. Built for campaigns and businesses that need scalable, automated customer support with human agent oversight.

## 🏗️ Architecture Overview

This monorepo contains a full-stack customer support platform:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Telegram Bot  │    │   Frontend      │    │    Backend      │
│   Integration   │◄──►│   Dashboard     │◄──►│   API Server    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
        │                        │                        │
        └────────────────────────┼────────────────────────┘
                                 ▼
        ┌─────────────────────────────────────────────────────────┐
        │                Infrastructure                           │
        │  PostgreSQL  │  Redis  │  Celery  │  WebSocket Server  │
        └─────────────────────────────────────────────────────────┘
```

### System Components

- **Backend** (`/backend`): FastAPI-based API server with Telegram integration
- **Frontend** (`/frontend`): Next.js dashboard for clients and agents
- **Database**: PostgreSQL for persistent data storage
- **Cache & Queue**: Redis for caching and Celery task queues
- **Real-time**: WebSocket server for live notifications

## ✨ Key Features

### 🤖 Telegram Bot Integration
- **Multi-bot Support**: Manage unlimited Telegram bots simultaneously
- **Automatic Ticket Creation**: Convert user messages into support tickets
- **Rate Limiting**: Respect Telegram's API limits automatically
- **Media Support**: Handle photos, documents, and files

### 🎯 Support Platform
- **Multi-tenancy**: Support multiple clients with isolated data
- **Role-based Access**: Client, agent, and admin roles
- **Real-time Notifications**: Live updates via WebSocket
- **Agent Management**: Invite and manage support staff

### 📊 Client Dashboard
- **Campaign Overview**: Monitor all support channels
- **Bot Management**: Configure and manage Telegram bots
- **Team Management**: Create and invite support agents
- **Analytics**: Performance metrics and insights

### 🎧 Agent Interface
- **Ticket Queue**: View and assign customer tickets
- **Real-time Updates**: Live notifications for new tickets
- **Conversation Management**: Complete customer interaction history
- **Status Tracking**: Manage ticket lifecycle

## 🚀 Quick Start

### Prerequisites

- **Docker & Docker Compose** (recommended)
- **Node.js 18+** (for local development)
- **Python 3.11+** (for local development)
- **PostgreSQL 12+** (if not using Docker)
- **Redis 6+** (if not using Docker)

### 1. Clone Repository

```bash
git clone <repository-url>
cd quis-monorepo
```

### 2. Docker Setup (Recommended)

```bash
# Copy environment configuration
cp backend/env.example backend/.env
cp frontend/.env.example frontend/.env.local

# Edit configuration files with your settings
# Edit backend/.env - add your Telegram bot tokens, SMTP settings, etc.
# Edit frontend/.env.local - configure API URLs

# Start all services
docker-compose up -d

# View logs
docker-compose logs -f
```

### 3. Manual Setup (Development)

#### Backend Setup

```bash
cd backend

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp env.example .env
# Edit .env with your configuration

# Start PostgreSQL and Redis (via Docker or locally)
docker-compose up postgres redis -d

# Run database migrations
alembic upgrade head

# Start backend services
uvicorn app.main:app --reload &
celery -A app.workers.celery_app worker --loglevel=info &
```

#### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Configure environment
cp .env.example .env.local
# Edit .env.local with backend API URL

# Start development server
npm run dev
```

### 4. Access the Platform

- **Frontend Dashboard**: http://localhost:3000
- **Backend API**: http://localhost:8000
- **API Documentation**: http://localhost:8000/docs
- **Celery Monitoring**: http://localhost:5555

## 📋 Configuration

### Environment Variables

#### Backend Configuration (`backend/.env`)

```env
# Core Application
SECRET_KEY=your-secret-key-here-minimum-32-characters
DATABASE_URL=postgresql://postgres:password@localhost:5432/telegram_bot_engine
REDIS_URL=redis://localhost:6379/0

# Telegram Integration
TELEGRAM_WEBHOOK_BASE_URL=https://your-domain.com
TELEGRAM_WEBHOOK_SECRET_TOKEN=your-webhook-secret-token

# Email/SMTP (for agent invitations)
EMAIL_USER=your-email@gmail.com
EMAIL_PASSWORD=your-app-password
FROM_EMAIL=your-email@gmail.com
BASE_LOGIN_URL=https://your-domain.com
```

#### Frontend Configuration (`frontend/.env.local`)

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_WS_URL=ws://localhost:8000
```

## 🛠️ Development

### Project Structure

```
quis-monorepo/
├── backend/                 # FastAPI backend server
│   ├── app/
│   │   ├── api/            # API route handlers
│   │   ├── models/         # Database models
│   │   ├── services/       # Business logic
│   │   ├── workers/        # Celery workers
│   │   └── utils/          # Utility functions
│   ├── alembic/            # Database migrations
│   ├── docker-compose.yml  # Backend services
│   └── requirements.txt    # Python dependencies
├── frontend/               # Next.js frontend application
│   ├── app/               # Next.js 14 App Router
│   ├── components/        # React components
│   ├── contexts/          # React contexts
│   ├── lib/               # Utility libraries
│   └── package.json       # Node.js dependencies
├── docker-compose.yml     # Full stack orchestration
└── README.md             # This file
```

### API Documentation

Once the backend is running, comprehensive API documentation is available:

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

### Database Migrations

```bash
cd backend

# Create new migration
alembic revision --autogenerate -m "Description of changes"

# Apply migrations
alembic upgrade head

# Rollback migration
alembic downgrade -1
```

## 🔧 Usage Examples

### 1. Setting Up a Campaign

```bash
# 1. Create client account (via frontend or API)
curl -X POST "http://localhost:8000/auth/register" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Acme Corp",
       "email": "admin@acme.com",
       "password": "secure_password"
     }'

# 2. Login to get access token
curl -X POST "http://localhost:8000/auth/login" \
     -H "Content-Type: application/json" \
     -d '{
       "email": "admin@acme.com",
       "password": "secure_password"
     }'
```

### 2. Creating a Telegram Bot Integration

```bash
# Create bot integration
curl -X POST "http://localhost:8000/manage/integrations" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
     -d '{
       "client_id": "YOUR_CLIENT_ID",
       "bot_token": "BOT_TOKEN_FROM_BOTFATHER"
     }'
```

### 3. Creating Support Agents

```bash
# Create support agent
curl -X POST "http://localhost:8000/manage/clients/CLIENT_ID/agents" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
     -d '{
       "email": "agent@acme.com",
       "first_name": "John",
       "last_name": "Doe",
       "role": "agent"
     }'
```

## 🔒 Security Features

- **JWT Authentication**: Secure token-based authentication
- **Role-based Access Control**: Multi-level permission system
- **Data Isolation**: Multi-tenant architecture with client data separation
- **Input Validation**: Comprehensive request validation
- **Rate Limiting**: API and bot-specific rate limiting
- **Secure Storage**: Encrypted password storage and secure file handling

## 📊 Monitoring & Observability

### Health Checks

```bash
# Backend health
curl http://localhost:8000/health

# Database connectivity
curl http://localhost:8000/health/db
```

### Monitoring Tools

- **Celery Flower**: http://localhost:5555 (Worker monitoring)
- **Redis Commander**: http://localhost:8081 (Redis management)
- **Application Logs**: `docker-compose logs -f`

### Performance Metrics

- **Response Time**: Track API response times
- **Ticket Volume**: Monitor ticket creation and resolution
- **Agent Performance**: Track agent response times and resolution rates
- **System Resources**: Monitor CPU, memory, and database performance

## 🚀 Deployment

### Production Deployment

1. **Prepare Environment**:
   ```bash
   # Set production environment variables
   export DEBUG=false
   export DATABASE_URL="postgresql://user:pass@prod-db:5432/db"
   export REDIS_URL="redis://prod-redis:6379/0"
   ```

2. **Deploy with Docker**:
   ```bash
   # Build and deploy
   docker-compose -f docker-compose.prod.yml up -d
   
   # Apply database migrations
   docker-compose exec backend alembic upgrade head
   ```

3. **Configure SSL/TLS**:
   - Set up reverse proxy (nginx, Traefik, or cloud load balancer)
   - Configure SSL certificates (Let's Encrypt, CloudFlare, etc.)
   - Update webhook URLs to use HTTPS

### Scaling Considerations

- **Horizontal Scaling**: Scale API servers and Celery workers
- **Database**: Use PostgreSQL replicas for read scaling
- **Redis**: Configure Redis Cluster for high availability
- **Load Balancing**: Distribute traffic across multiple API instances

## 📚 Documentation

- **Backend API**: Detailed API documentation in `/backend/README.md`
- **Frontend Guide**: Complete frontend documentation in `/frontend/README.md`
- **API Reference**: Interactive docs at `/docs` when backend is running

## 🐛 Troubleshooting

### Common Issues

**Backend Won't Start**:
- Check PostgreSQL and Redis are accessible
- Verify environment variables are set correctly
- Check logs: `docker-compose logs backend`

**Frontend Connection Issues**:
- Verify `NEXT_PUBLIC_API_URL` points to backend
- Check backend is running and accessible
- Verify CORS settings in backend

**Telegram Webhook Issues**:
- Ensure webhook URL is publicly accessible via HTTPS
- Check webhook secret token matches configuration
- Verify bot token is valid

**Database Migration Errors**:
- Check database connectivity
- Ensure user has proper permissions
- Review migration files for conflicts

### Debug Mode

```bash
# Enable debug logging
export DEBUG=true
export LOG_LEVEL=DEBUG

# Start services with debug output
docker-compose up
```

## 🤝 Contributing

1. **Fork the repository**
2. **Create feature branch**: `git checkout -b feature/amazing-feature`
3. **Follow code style**: Use existing patterns and conventions
4. **Add tests**: Ensure new features are tested
5. **Update documentation**: Update relevant README files
6. **Submit pull request**: Include detailed description of changes

### Development Guidelines

- **Backend**: Follow FastAPI and Python best practices
- **Frontend**: Use TypeScript, React hooks, and Tailwind CSS
- **Database**: Use Alembic for schema changes
- **Testing**: Add unit and integration tests
- **Documentation**: Keep README files up to date

## 📄 License

[Add your license information here]

## 🆘 Support

For help and support:

1. **Check Documentation**: Review relevant README files
2. **Check Logs**: Use `docker-compose logs -f` to check system logs
3. **API Status**: Verify backend health at `/health` endpoint
4. **Common Issues**: Review troubleshooting section above
5. **Create Issue**: Open GitHub issue with detailed problem description

## 📈 Roadmap

- **Multi-channel Support**: WhatsApp, SMS, and email integrations
- **AI-powered Responses**: Automated response suggestions
- **Advanced Analytics**: Detailed performance insights
- **Mobile Apps**: Native iOS and Android applications
- **API Rate Limiting**: Enhanced rate limiting and quotas
- **Advanced Routing**: Intelligent ticket routing based on content

---

**Built with ❤️ for enterprise customer support automation**

### Quick Links

- [Backend Documentation](./backend/README.md)
- [Frontend Documentation](./frontend/README.md)
- [API Documentation](http://localhost:8000/docs) (when running)
- [Contributing Guidelines](#-contributing)
- [Deployment Guide](#-deployment)
