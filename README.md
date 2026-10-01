# NamaBackend - Namatube Microservices

Backend microservices ecosystem for the Namatube video sharing platform, built with Django and Django REST Framework.

## 🏗️ Architecture

NamaBackend implements a microservices architecture with the following independent services:

### Core Services

1. **Auth Service** (`auth_service/`)
   - User registration and authentication
   - JWT token management
   - OAuth2 integration (Google, GitHub)
   - Two-Factor Authentication (2FA)
   - Session management

2. **Video Service** (`video_service/`)
   - Video upload handling
   - FFmpeg transcoding pipeline
   - Multiple resolution generation
   - Thumbnail extraction
   - Video metadata management
   - Streaming URL generation

3. **Interaction Service** (`interaction_service/`)
   - Like/Dislike functionality
   - Comment system with threading
   - Subscription management
   - User engagement tracking

4. **Channel Service** (`channel_service/`)
   - Channel profile management
   - Playlist creation and management
   - Video listing and organization
   - Channel analytics

5. **Admin Service** (`admin_service/`)
   - User moderation
   - Content moderation
   - Violation reports
   - System health monitoring
   - Network simulation tools

6. **Search Service** (`search_service/`)
   - Elasticsearch integration
   - Full-text search
   - Advanced filtering
   - Search analytics

7. **Recommendation Service** (`recommendation_service/`)
   - Personalized recommendations
   - Trending content
   - Related video suggestions
   - User behavior analysis

## 🛠️ Tech Stack

- **Framework**: Django 4.x + Django REST Framework
- **Authentication**: djangorestframework-simplejwt, django-allauth
- **Video Processing**: FFmpeg, Celery
- **Task Queue**: Celery + RabbitMQ
- **Databases**: 
  - PostgreSQL (relational data)
  - MongoDB (pymongo for metadata/comments)
  - Redis (django-redis for caching)
- **Object Storage**: boto3 (S3/MinIO)
- **Search**: Elasticsearch
- **API Documentation**: drf-spectacular (OpenAPI/Swagger)
- **Testing**: pytest, pytest-django

## 📋 Prerequisites

- Python 3.10+
- Docker & Docker Compose
- PostgreSQL 14+
- MongoDB 5+
- Redis 6+
- RabbitMQ 3.x
- MinIO (or AWS S3 credentials)
- FFmpeg

## 🚀 Getting Started

### Local Development with Docker

The recommended way to run NamaBackend is through the main project's `docker-compose.yml`:

```bash
# From the project root
docker-compose up -d
```

### Manual Setup (for development)

1. **Clone and setup virtual environment**
```bash
cd NamaBackend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

2. **Install dependencies**
```bash
pip install -r requirements.txt
```

3. **Configure environment variables**
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. **Run database migrations**
```bash
python manage.py migrate
```

5. **Create superuser**
```bash
python manage.py createsuperuser
```

6. **Start Celery worker (separate terminal)**
```bash
celery -A namabackend worker -l info
```

7. **Run development server**
```bash
python manage.py runserver 0.0.0.0:8000
```

## 🔧 Configuration

### Environment Variables

Create a `.env` file with the following variables:

```env
# Django
SECRET_KEY=your-secret-key
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database - PostgreSQL
DB_ENGINE=django.db.backends.postgresql
DB_NAME=namatube
DB_USER=postgres
DB_PASSWORD=your-password
DB_HOST=localhost
DB_PORT=5432

# MongoDB
MONGO_HOST=localhost
MONGO_PORT=27017
MONGO_DB=namatube_metadata
MONGO_USER=mongouser
MONGO_PASSWORD=mongopassword

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DB=0

# RabbitMQ
CELERY_BROKER_URL=amqp://guest:guest@localhost:5672//

# MinIO / S3
AWS_ACCESS_KEY_ID=minioadmin
AWS_SECRET_ACCESS_KEY=minioadmin
AWS_STORAGE_BUCKET_NAME=namatube-videos
AWS_S3_ENDPOINT_URL=http://localhost:9000
AWS_S3_REGION_NAME=us-east-1

# Elasticsearch
ELASTICSEARCH_HOST=localhost
ELASTICSEARCH_PORT=9200

# JWT
JWT_SECRET_KEY=your-jwt-secret
JWT_EXPIRATION_HOURS=24

# OAuth
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
GITHUB_CLIENT_ID=your-github-client-id
GITHUB_CLIENT_SECRET=your-github-client-secret
```

## 🧪 Testing

### Run all tests
```bash
pytest
```

### Run tests for a specific service
```bash
pytest auth_service/tests/
pytest video_service/tests/
```

### Run with coverage
```bash
pytest --cov=. --cov-report=html
```

### Run integration tests
```bash
pytest -m integration
```

## 📚 API Documentation

### Interactive API Documentation

Once the server is running, access the interactive API documentation:

- **Swagger UI**: http://localhost:8000/api/docs/
- **ReDoc**: http://localhost:8000/api/redoc/

### API Contract

All API endpoints are documented in the project root's [API.md](../API.md) file. This serves as the contract between frontend and backend.

## 🏃 Running Services Individually

Each microservice can be run independently for development:

```bash
# Auth Service
python manage.py runserver 8001 --settings=auth_service.settings

# Video Service
python manage.py runserver 8002 --settings=video_service.settings

# And so on...
```

## 🔄 Database Migrations

### Create new migrations
```bash
python manage.py makemigrations
```

### Apply migrations
```bash
python manage.py migrate
```

### Check migration status
```bash
python manage.py showmigrations
```

## 📦 Project Structure

```
NamaBackend/
├── auth_service/
│   ├── models.py          # User, Token models
│   ├── serializers.py     # API serializers
│   ├── views.py           # API endpoints
│   ├── urls.py            # URL routing
│   └── tests/             # Unit & integration tests
├── video_service/
│   ├── models.py          # Video models
│   ├── tasks.py           # Celery tasks (transcoding)
│   ├── views.py           # Upload, streaming endpoints
│   └── tests/
├── interaction_service/
│   ├── models.py          # Like, Comment models
│   ├── views.py           # Interaction endpoints
│   └── tests/
├── channel_service/
│   ├── models.py          # Channel, Playlist models
│   ├── views.py           # Channel management
│   └── tests/
├── admin_service/
│   ├── views.py           # Admin operations
│   └── tests/
├── search_service/
│   ├── search.py          # Elasticsearch integration
│   ├── views.py           # Search endpoints
│   └── tests/
├── recommendation_service/
│   ├── recommender.py     # Recommendation algorithms
│   ├── views.py           # Recommendation endpoints
│   └── tests/
├── common/                # Shared utilities
│   ├── middleware.py      # Custom middleware
│   ├── permissions.py     # Permission classes
│   └── utils.py           # Helper functions
├── namabackend/           # Main Django project
│   ├── settings.py        # Django settings
│   ├── urls.py            # Root URL configuration
│   └── celery.py          # Celery configuration
├── requirements.txt       # Python dependencies
├── pytest.ini             # Pytest configuration
├── Dockerfile             # Container definition
└── README.md              # This file
```

## 🔒 Security

- All endpoints use JWT authentication
- Rate limiting enforced at API Gateway level
- Input validation on all endpoints
- SQL injection protection via Django ORM
- CORS configured for frontend origin only
- Secrets managed via environment variables
- TLS 1.3 required for production

## 📊 Monitoring & Logging

### Structured Logging

All services use structured JSON logging:

```python
import logging
logger = logging.getLogger(__name__)

logger.info("video_uploaded", extra={
    "user_id": user.id,
    "video_id": video.id,
    "file_size": file.size
})
```

### Health Check Endpoints

Each service exposes a health check:

```
GET /health
```

Response:
```json
{
  "status": "healthy",
  "service": "video_service",
  "database": "connected",
  "redis": "connected",
  "timestamp": "2026-10-02T00:00:00Z"
}
```

## 🚀 Deployment

### Production Considerations

1. **Environment**: Set `DEBUG=False`
2. **Static Files**: Configure static file serving
3. **Database**: Use managed PostgreSQL service
4. **Redis**: Use managed Redis service (ElastiCache)
5. **Message Queue**: Use managed RabbitMQ (Amazon MQ)
6. **Object Storage**: Use AWS S3 or equivalent
7. **CDN**: Configure CloudFront for video delivery
8. **Monitoring**: Set up CloudWatch or equivalent

### Docker Production Build

```bash
docker build -t namabackend:production .
docker push your-registry/namabackend:production
```

## 🤝 Contributing

1. Follow the project [Constitution](../.specify/memory/constitution.md)
2. Read [AGENTS.md](../AGENTS.md) for implementation guidelines
3. Update [API.md](../API.md) before changing endpoints
4. Write tests for all new features
5. Ensure CI/CD checks pass

## 📄 License

This project is part of Namatube and follows the same custom license. See [LICENSE](https://github.com/mahajialirezaei/NamaTube/blob/main/LICENSE) for details.

## 👤 Author

**Mohammad Amin Haji Alirezaei**
- Email: m.a.hajialirezaei05@gmail.com

## 🔗 Related Documentation

- [Main Project README](https://www.github.com/mahajialirezaei/NamaTube/blob/main/README.md)
- [API Contract](https://www.github.com/mahajialirezaei/NamaTube/blob/main/API.md)
- [System Architecture](https://www.github.com/mahajialirezaei/NamaTube/blob/main/docs/System-Architecture-Document.md)
- [Functional Requirements](https://www.github.com/mahajialirezaei/NamaTube/blob/main/docs/Functional.md)
- [Non-Functional Requirements](https://www.github.com/mahajialirezaei/NamaTube/blob/main/docs/Non-Functional.md)
