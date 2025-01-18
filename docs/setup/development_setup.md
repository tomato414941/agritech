# Development Environment Setup

## Prerequisites

### Required Software
- Python 3.11 or higher
- Node.js 18.x or higher
- PostgreSQL 15.x
- Git
- Docker (optional, for containerized development)

### Development Tools
- Visual Studio Code (recommended)
- PyCharm (alternative for Python development)
- DBeaver (database management)

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

2. Update the following variables in `.env`:
```env
# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=agritech_dev
DB_USER=your_username
DB_PASSWORD=your_password

# API Configuration
API_HOST=localhost
API_PORT=8000
DEBUG=true

# JWT Settings
JWT_SECRET_KEY=your_secret_key
JWT_ALGORITHM=HS256
```

### Database Setup
```bash
# Create database
createdb agritech_dev

# Run migrations
poetry run alembic upgrade head

# (Optional) Load sample data
poetry run python scripts/load_sample_data.py
```

### Running Backend Server
```bash
# Start development server
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

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

## Docker Setup (Optional)

### Prerequisites
- Docker
- Docker Compose

### Running with Docker
```bash
# Build and start all services
docker-compose up --build

# Run in background
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

## IDE Setup

### VS Code Extensions
- Python
- Pylance
- ESLint
- Prettier
- GitLens
- Docker
- Thunder Client

### VS Code Settings
```json
{
    "python.formatting.provider": "black",
    "python.linting.enabled": true,
    "python.linting.pylintEnabled": true,
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    }
}
```

## Development Workflow

### Starting Development
1. Start the database server
2. Start the backend server
3. Start the frontend server
4. Access the application at `http://localhost:5173`

### Running Tests
```bash
# Backend tests
cd backend
poetry run pytest

# Frontend tests
cd frontend
pnpm test
```

### Code Formatting
```bash
# Backend (Black)
cd backend
poetry run black .

# Frontend (Prettier)
cd frontend
pnpm format
```

### Linting
```bash
# Backend
cd backend
poetry run pylint app/

# Frontend
cd frontend
pnpm lint
```

## Troubleshooting

### Common Issues

#### Database Connection
- Ensure PostgreSQL is running
- Verify database credentials in `.env`
- Check database exists

#### Backend Server
- Verify Python version
- Check virtual environment is activated
- Confirm all dependencies are installed

#### Frontend Server
- Verify Node.js version
- Check for port conflicts
- Ensure API URL is correct

### Getting Help
- Check project documentation
- Review issue tracker
- Contact development team

## Security Notes

### Local Development
- Never commit `.env` files
- Use strong passwords
- Keep dependencies updated
- Follow security guidelines

### API Keys
- Use development API keys
- Never share production keys
- Rotate keys regularly
