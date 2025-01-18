# Coding Standards

## Python (Backend)

### Style Guide
- Follow PEP 8 style guide
- Use type hints for all functions and methods
- Maximum line length: 88 characters (Black formatter default)
- Docstring format: Google style

### Example
```python
def calculate_field_area(length: float, width: float) -> float:
    """Calculate the area of a rectangular field.

    Args:
        length (float): The length of the field in meters
        width (float): The width of the field in meters

    Returns:
        float: The area of the field in square meters
    """
    return length * width
```

## TypeScript (Frontend)

### Style Guide
- Use ESLint with Airbnb configuration
- Use Prettier for formatting
- Use functional components
- Use TypeScript interfaces for prop types

### Example
```typescript
interface FieldProps {
  length: number;
  width: number;
  cropType: string;
}

const Field: React.FC<FieldProps> = ({ length, width, cropType }) => {
  return (
    // Component implementation
  );
};
```

## Environment Variables and Secrets

### Development
- Use `.env` files for local development
- Never commit `.env` files to version control
- Provide `.env.example` with dummy values

### Production
- Use environment variables in production
- Use secret management service for sensitive data
- Rotate secrets regularly

### Example `.env.example`
```
# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=agritech
DB_USER=user
DB_PASSWORD=password

# API Keys
WEATHER_API_KEY=your_api_key
```

## Code Review Process

### Guidelines
- Review all changes before merging
- Use pull request templates
- Check for:
  - Code style compliance
  - Test coverage
  - Documentation updates
  - Security considerations

### Pull Request Template
```markdown
## Description
[Description of the changes]

## Type of change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Checklist
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Security considerations reviewed
- [ ] Code follows style guidelines
```
