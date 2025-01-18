# AgriTech

Agricultural Management Application

## Overview

AgriTech is a comprehensive management platform for agricultural producers. It provides features for field management, crop tracking, and resource management to support efficient agricultural operations.

## Required Reading

Please review the following documents in order before starting development:

1. [Project Planning](docs/project_planning.md)
   - Project Structure
   - Technology Stack and Rationale
   - Planned Features

2. [MVP Development Plan](docs/mvp_plan.md)
   - Feature Scope (Crop Info, Cultivation Records)
   - Development Approach and Priorities
   - Mobile Strategy

3. [Git Branch Strategy](docs/git_branch_strategy.md)
   - Branch Management Rules
   - Development Flow
   - Merge Strategy


## Tech Stack

### Backend
- FastAPI (Python)
- PostgreSQL
- SQLAlchemy

### Frontend
- React.js
- TypeScript
- Chakra UI

### Infrastructure
- Docker
- GitHub Actions

## Development Setup

### Prerequisites
- Python 3.11 or higher
- Docker and Docker Compose
- Poetry (Python package manager)

### Backend Setup

1. Install Poetry:
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

2. Setup backend environment:
```bash
# Navigate to backend directory
cd backend

# Install dependencies
poetry install

# Copy environment file
cp .env.example .env
```

3. Start PostgreSQL using Docker:
```bash
# Start PostgreSQL container
cd docker
docker-compose up -d
```

4. Start development server:
```bash
cd backend
poetry run uvicorn app.main:app --reload
```

For more detailed setup instructions, see [Development Environment Setup](docs/setup/development_environment_setup.md).

## Project Structure

```
agritech/
├── backend/          # FastAPI application
├── frontend/         # React application
├── docker/          # Docker configuration
└── docs/            # Project documentation
```

## License

This project is maintained as a private repository.
