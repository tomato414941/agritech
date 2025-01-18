# Development Environment Setup

## Prerequisites

### Required Software
- Python 3.11 or higher
- Docker and Docker Compose
- Git
- Poetry (Python package manager)

### Related Documents
- [System Architecture](../architecture/system_architecture.md)
- [Database Schema](../architecture/database_schema.md)
- [Security Guidelines](../guidelines/security.md)
- [Git Branch Strategy](../git_branch_strategy.md)

## Backend Setup

### Python Environment
```bash
# Install Poetry for dependency management
curl -sSL https://install.python-poetry.org | python3 -

# Navigate to backend directory
cd backend

# Install dependencies
poetry install

# Activate virtual environment
poetry shell
```

### Environment Configuration
1. Copy the example environment file:
```bash
cp .env.example .env
```

2. The default configuration in `.env.example` is set up for Docker:
```env
# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=agritech_dev
DB_USER=postgres
DB_PASSWORD=postgres

# API Configuration
API_HOST=0.0.0.0
API_PORT=8000
API_DEBUG=true

# Security
SECRET_KEY=your-secret-key-here
ALGORITHM=HS256
```

Make sure to update the `SECRET_KEY` with a secure value.

### Database Setup
1. Start PostgreSQL using Docker:
```bash
# Navigate to docker directory
cd docker

# Start PostgreSQL container
docker-compose up -d

# Verify container is running
docker-compose ps
```

2. Initialize database schema:
```bash
# Run migrations (after they are created)
poetry run alembic upgrade head

# (Optional) Load sample data (after script is created)
poetry run python scripts/load_sample_data.py
```

### Running Backend Server
```bash
# Start development server
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`.
API documentation will be available at:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

## Frontend Setup

### Node.js Environment
```bash
# Navigate to frontend directory
cd frontend

# Install pnpm (if not installed)
npm install -g pnpm

# Install dependencies
pnpm install
```

### Environment Configuration
1. Copy the example environment file:
```bash
cp .env.example .env
```

2. Update the following variables in `.env`:
```env
VITE_API_URL=http://localhost:8000
VITE_ENV=development
```

### Running Frontend Server
```bash
# Start development server
pnpm dev
```

The frontend will be available at `http://localhost:5173`.

## Next Steps

1. Set up your IDE:
   - Install Python and JavaScript extensions
   - Configure linting and formatting
   - Set up debugger

2. Familiarize yourself with:
   - [Project Architecture](../architecture/system_architecture.md)
   - [API Documentation](../api/README.md)
   - [Development Guidelines](../guidelines/README.md)

3. Start developing:
   - Create a new feature branch
   - Follow the [Git Branch Strategy](../git_branch_strategy.md)
   - Submit pull requests for review
