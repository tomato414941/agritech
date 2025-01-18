# Authentication and Authorization

## Overview
This document outlines the authentication and authorization mechanisms for the AgriTech API.

## Authentication Methods

### API Key Authentication
```http
GET /api/v1/fields
Authorization: Bearer your-api-key
```

#### API Key Format
- 32 character alphanumeric string
- Prefix: `at_` for production, `at_test_` for testing
- Example: `at_1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p`

#### Key Management
1. Generate new key:
```http
POST /api/v1/auth/keys
Content-Type: application/json

{
    "name": "Production API Key",
    "expires_at": "2026-01-19T00:00:00Z"
}
```

2. List active keys:
```http
GET /api/v1/auth/keys
```

3. Revoke key:
```http
DELETE /api/v1/auth/keys/{key_id}
```

### OAuth 2.0 Authentication
For user-facing applications, OAuth 2.0 authentication is supported.

#### Authorization Code Flow
1. Redirect to authorization endpoint:
```
GET https://auth.agritech.com/oauth/authorize
    ?client_id=your-client-id
    &redirect_uri=your-redirect-uri
    &response_type=code
    &scope=read write
    &state=random-state-string
```

2. Exchange code for tokens:
```http
POST https://auth.agritech.com/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code=authorization-code
&client_id=your-client-id
&client_secret=your-client-secret
&redirect_uri=your-redirect-uri
```

#### Response
```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLC...",
    "token_type": "Bearer",
    "expires_in": 3600,
    "refresh_token": "eyJ0eXAiOiJKV1QiLC...",
    "scope": "read write"
}
```

## Authorization

### Role-Based Access Control (RBAC)

#### Available Roles
| Role | Description |
|------|-------------|
| admin | Full system access |
| manager | Farm management access |
| operator | Field operations access |
| viewer | Read-only access |

#### Role Permissions
| Endpoint | Admin | Manager | Operator | Viewer |
|----------|-------|----------|-----------|---------|
| GET /fields | ✓ | ✓ | ✓ | ✓ |
| POST /fields | ✓ | ✓ | - | - |
| PUT /fields | ✓ | ✓ | - | - |
| DELETE /fields | ✓ | - | - | - |
| GET /sensors | ✓ | ✓ | ✓ | ✓ |
| POST /sensors | ✓ | ✓ | - | - |
| GET /reports | ✓ | ✓ | ✓ | ✓ |
| POST /reports | ✓ | ✓ | ✓ | - |

### Scope-Based Authorization

#### Available Scopes
| Scope | Description |
|-------|-------------|
| read | Read access to resources |
| write | Write access to resources |
| delete | Delete access to resources |
| admin | Administrative actions |

#### Scope Examples
```
read:fields          # Read field data
write:fields         # Create/update fields
read:sensors         # Read sensor data
write:sensors        # Configure sensors
admin:users          # Manage users
```

## Security Best Practices

### API Key Security
1. Store API keys securely
2. Rotate keys regularly
3. Use environment variables
4. Never commit keys to source control

### OAuth Security
1. Validate redirect URIs
2. Use state parameter
3. Short-lived access tokens
4. Secure token storage
5. HTTPS only

### JWT Security
1. Short expiration times
2. Secure signature verification
3. Validate all claims
4. Include only necessary data

## Rate Limiting

### Default Limits
| Authentication Type | Rate Limit |
|--------------------|------------|
| API Key | 1000 requests/hour |
| OAuth Client | 5000 requests/hour |
| OAuth User | 100 requests/minute |

### Response Headers
```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1705593600
```

## Error Responses

### Invalid Authentication
```json
{
    "error": {
        "code": "AUTHENTICATION_ERROR",
        "message": "Invalid API key provided",
        "details": [
            {
                "code": "INVALID_KEY",
                "message": "The provided API key is invalid or has been revoked"
            }
        ]
    }
}
```

### Insufficient Permissions
```json
{
    "error": {
        "code": "AUTHORIZATION_ERROR",
        "message": "Insufficient permissions",
        "details": [
            {
                "code": "INSUFFICIENT_SCOPE",
                "message": "The 'write:fields' scope is required for this operation"
            }
        ]
    }
}
```

## Implementation Examples

### API Key Validation (Python/FastAPI)
```python
from fastapi import Security, HTTPException
from fastapi.security import APIKeyHeader

api_key_header = APIKeyHeader(name="Authorization", auto_error=False)

async def validate_api_key(api_key: str = Security(api_key_header)):
    if not api_key.startswith("Bearer "):
        raise HTTPException(status_code=401, detail="Invalid authentication scheme")
    
    key = api_key.replace("Bearer ", "")
    if not is_valid_key(key):
        raise HTTPException(status_code=401, detail="Invalid API key")
    
    return key

@app.get("/api/v1/fields")
async def list_fields(api_key: str = Security(validate_api_key)):
    # Implementation
    pass
```

### OAuth Middleware (Python/FastAPI)
```python
from fastapi import Depends
from fastapi_jwt_auth import AuthJWT

async def validate_oauth_token(auth: AuthJWT = Depends()):
    auth.jwt_required()
    user = auth.get_jwt_subject()
    return user

@app.post("/api/v1/fields")
async def create_field(
    field: FieldCreate,
    user: str = Depends(validate_oauth_token)
):
    # Implementation
    pass
```
