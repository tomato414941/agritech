# Error Handling Standards

## Overview
This document defines the standard error handling practices for the AgriTech API.

## Error Response Format

### Standard Error Structure
```json
{
    "error": {
        "code": "ERROR_CODE",
        "message": "Human-readable error message",
        "details": [
            {
                "field": "affected_field",
                "code": "FIELD_ERROR_CODE",
                "message": "Field-specific error message"
            }
        ],
        "requestId": "unique-request-id",
        "timestamp": "2025-01-19T00:25:21+09:00"
    }
}
```

## Error Categories

### Validation Errors (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "The request contains invalid parameters",
        "details": [
            {
                "field": "area",
                "code": "INVALID_RANGE",
                "message": "Field area must be between 0.1 and 10000 hectares"
            }
        ]
    }
}
```

### Authentication Errors (401)
```json
{
    "error": {
        "code": "AUTHENTICATION_ERROR",
        "message": "Invalid or expired authentication token",
        "details": [
            {
                "code": "TOKEN_EXPIRED",
                "message": "The access token has expired"
            }
        ]
    }
}
```

### Authorization Errors (403)
```json
{
    "error": {
        "code": "AUTHORIZATION_ERROR",
        "message": "Insufficient permissions to access resource",
        "details": [
            {
                "code": "INSUFFICIENT_ROLE",
                "message": "User role 'viewer' cannot perform 'delete' operation"
            }
        ]
    }
}
```

### Resource Errors (404)
```json
{
    "error": {
        "code": "RESOURCE_NOT_FOUND",
        "message": "The requested resource was not found",
        "details": [
            {
                "field": "fieldId",
                "code": "INVALID_ID",
                "message": "Field with ID 'abc123' does not exist"
            }
        ]
    }
}
```

### Conflict Errors (409)
```json
{
    "error": {
        "code": "RESOURCE_CONFLICT",
        "message": "The request conflicts with the current state",
        "details": [
            {
                "field": "name",
                "code": "DUPLICATE_VALUE",
                "message": "A field with name 'North Field' already exists"
            }
        ]
    }
}
```

### Server Errors (500)
```json
{
    "error": {
        "code": "INTERNAL_ERROR",
        "message": "An unexpected error occurred",
        "requestId": "req-123456",
        "timestamp": "2025-01-19T00:25:21+09:00"
    }
}
```

## Error Codes Reference

### Common Error Codes
| Code | Description |
|------|-------------|
| VALIDATION_ERROR | Request validation failed |
| AUTHENTICATION_ERROR | Authentication failed |
| AUTHORIZATION_ERROR | Authorization failed |
| RESOURCE_NOT_FOUND | Requested resource not found |
| RESOURCE_CONFLICT | Resource state conflict |
| INTERNAL_ERROR | Internal server error |

### Field-Specific Codes
| Code | Description |
|------|-------------|
| INVALID_RANGE | Value outside allowed range |
| INVALID_FORMAT | Value format incorrect |
| REQUIRED_FIELD | Required field missing |
| DUPLICATE_VALUE | Value already exists |

## Best Practices

### Error Messages
1. Be specific but don't reveal system details
2. Use consistent terminology
3. Include actionable information
4. Maintain security (don't leak sensitive data)

### Error Handling
1. Always include a request ID
2. Log errors with stack traces internally
3. Return appropriate HTTP status codes
4. Include timestamp in UTC

## Implementation Guide

### Example Implementation (Python/FastAPI)
```python
from fastapi import HTTPException
from typing import List, Optional

class ErrorDetail:
    def __init__(self, field: str, code: str, message: str):
        self.field = field
        self.code = code
        self.message = message

class APIError(HTTPException):
    def __init__(
        self,
        status_code: int,
        code: str,
        message: str,
        details: Optional[List[ErrorDetail]] = None
    ):
        self.status_code = status_code
        self.code = code
        self.message = message
        self.details = details or []
```

### Usage Example
```python
@app.post("/api/v1/fields")
async def create_field(field: FieldCreate):
    if await field_exists(field.name):
        raise APIError(
            status_code=409,
            code="RESOURCE_CONFLICT",
            message="Field already exists",
            details=[
                ErrorDetail(
                    field="name",
                    code="DUPLICATE_VALUE",
                    message=f"A field with name '{field.name}' already exists"
                )
            ]
        )
```
