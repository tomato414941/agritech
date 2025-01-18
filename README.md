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
├── backend/                # FastAPI application
│   ├── app/               # Application source code
│   ├── .env.example       # Environment variables template
│   ├── poetry.lock        # Poetry dependency lock file
│   └── pyproject.toml     # Python project configuration
├── frontend/              # React application
│   └── src/              # React source code
├── docker/               # Docker configuration files
├── docs/                 # Project documentation
│   ├── architecture/     # System architecture docs
│   ├── guidelines/       # Development guidelines
│   └── setup/           # Setup instructions
└── .gitignore           # Git ignore rules
```

Each directory serves a specific purpose:
- `backend/`: Contains the FastAPI server implementation, database models, and API endpoints
- `frontend/`: Houses the React-based user interface components and state management
- `docker/`: Contains Docker configuration for development and production environments
- `docs/`: Comprehensive project documentation including architecture, guidelines, and setup instructions

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
