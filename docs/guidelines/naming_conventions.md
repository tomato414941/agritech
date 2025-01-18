# Naming Conventions

## Overview

This document defines the naming conventions for the AgriTech project. Following these conventions ensures consistency and maintainability across the project.

## File and Directory Naming

### Documentation Files

1. **Markdown Files**
   - Use lowercase letters
   - Use underscores for spaces
   - Use `.md` extension
   - Example: `naming_conventions.md`, `development_setup.md`

2. **Document Titles**
   - Use Title Case
   - Use spaces between words
   - Example: `# Naming Conventions`, `# Development Environment Setup`

3. **Directory Names**
   - Use lowercase letters
   - Use simple, descriptive names
   - No spaces (use underscores if needed)
   - Example: `docs`, `guidelines`, `api`

### Source Code Files

1. **Python Files**
   - Use lowercase letters
   - Use underscores for spaces
   - Use `.py` extension
   - Example: `field_manager.py`, `sensor_data.py`

2. **TypeScript/JavaScript Files**
   - Use lowercase letters
   - Use hyphens for spaces
   - Use `.ts` or `.tsx` for TypeScript
   - Use `.js` or `.jsx` for JavaScript
   - Example: `field-monitor.tsx`, `sensor-data.ts`

3. **Configuration Files**
   - Use lowercase letters
   - Use descriptive suffixes when needed
   - Example: `.env.example`, `docker-compose.yml`

## Code Naming

### Python

1. **Variables and Functions**
   - Use snake_case
   - Example: `field_id`, `calculate_yield`

2. **Classes**
   - Use PascalCase
   - Example: `FieldManager`, `SensorData`

3. **Constants**
   - Use uppercase with underscores
   - Example: `MAX_TEMPERATURE`, `DEFAULT_TIMEOUT`

### TypeScript/JavaScript

1. **Variables and Functions**
   - Use camelCase
   - Example: `fieldId`, `calculateYield`

2. **Components**
   - Use PascalCase
   - Example: `FieldMonitor`, `SensorDisplay`

3. **Constants**
   - Use uppercase with underscores
   - Example: `MAX_TEMPERATURE`, `DEFAULT_TIMEOUT`

### Database

1. **Table Names**
   - Use snake_case
   - Use plural form
   - Example: `fields`, `sensor_readings`

2. **Column Names**
   - Use snake_case
   - Example: `field_id`, `created_at`

## Branch Names

1. **Feature Branches**
   - Format: `feature/[issue-id]-brief-description`
   - Example: `feature/123-user-authentication`

2. **Release Branches**
   - Format: `release/vX.Y.Z`
   - Example: `release/v1.0.0`

3. **Hotfix Branches**
   - Format: `hotfix/vX.Y.Z`
   - Example: `hotfix/v1.0.1`

## Version Numbers

1. **Format**
   - Use Semantic Versioning (SemVer)
   - Format: `MAJOR.MINOR.PATCH`
   - Example: `1.0.0`, `1.1.2`

2. **Increment Rules**
   - MAJOR: Breaking changes
   - MINOR: New features (backwards compatible)
   - PATCH: Bug fixes (backwards compatible)

## Best Practices

1. **General Guidelines**
   - Be consistent
   - Be descriptive
   - Avoid abbreviations unless commonly known
   - Use English for all names

2. **Documentation**
   - Keep file names short but meaningful
   - Use consistent terminology across documents
   - Update related documents when changing names

3. **Code**
   - Follow language-specific conventions
   - Be consistent within each technology
   - Document exceptions to conventions
