# Admin API

## Overview
The Admin API provides system administration capabilities including user management, role-based access control, and system configuration. It enables secure management of the platform's core settings and user permissions.

## Endpoints

### User Management

#### List Users
```http
GET /api/v1/admin/users
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| status | string | Filter by status | active |
| role | string | Filter by role | all |
| search | string | Search by name/email | - |
| page | integer | Page number | 1 |
| per_page | integer | Items per page | 20 |

##### Response
```json
{
    "data": [
        {
            "id": "u12345",
            "email": "john.smith@example.com",
            "name": "John Smith",
            "status": "active",
            "roles": ["farm_manager", "field_operator"],
            "permissions": [
                "read:fields",
                "write:fields",
                "read:crops",
                "write:crops"
            ],
            "organization_id": "org12345",
            "last_login": "2025-01-19T00:44:02Z",
            "created_at": "2025-01-01T00:00:00Z",
            "updated_at": "2025-01-19T00:44:02Z"
        }
    ],
    "meta": {
        "page": 1,
        "per_page": 20,
        "total": 45
    }
}
```

#### Create User
```http
POST /api/v1/admin/users
```

##### Request Body
```json
{
    "user": {
        "email": "jane.doe@example.com",
        "name": "Jane Doe",
        "roles": ["field_operator"],
        "organization_id": "org12345",
        "settings": {
            "language": "en",
            "timezone": "Asia/Tokyo",
            "notifications": {
                "email": true,
                "sms": false
            }
        }
    }
}
```

##### Response
```json
{
    "data": {
        "id": "u12346",
        "status": "pending",
        "invitation_url": "https://app.agritech.com/invite/abc123",
        "expires_at": "2025-01-26T00:44:02Z",
        "created_at": "2025-01-19T00:44:02Z"
    }
}
```

### Role Management

#### List Roles
```http
GET /api/v1/admin/roles
```

##### Response
```json
{
    "data": [
        {
            "id": "r12345",
            "name": "farm_manager",
            "description": "Farm management access",
            "permissions": [
                {
                    "resource": "fields",
                    "actions": ["read", "write", "delete"],
                    "conditions": {
                        "organization_id": "${user.organization_id}"
                    }
                },
                {
                    "resource": "crops",
                    "actions": ["read", "write"],
                    "conditions": {
                        "organization_id": "${user.organization_id}"
                    }
                }
            ],
            "users_count": 5,
            "created_at": "2025-01-01T00:00:00Z",
            "updated_at": "2025-01-19T00:44:02Z"
        }
    ]
}
```

#### Create Role
```http
POST /api/v1/admin/roles
```

##### Request Body
```json
{
    "role": {
        "name": "equipment_manager",
        "description": "Equipment management access",
        "permissions": [
            {
                "resource": "equipment",
                "actions": ["read", "write", "maintain"],
                "conditions": {
                    "organization_id": "${user.organization_id}"
                }
            },
            {
                "resource": "maintenance_logs",
                "actions": ["read", "write"],
                "conditions": {
                    "organization_id": "${user.organization_id}"
                }
            }
        ]
    }
}
```

### Organization Management

#### List Organizations
```http
GET /api/v1/admin/organizations
```

##### Response
```json
{
    "data": [
        {
            "id": "org12345",
            "name": "Green Valley Farms",
            "type": "enterprise",
            "status": "active",
            "subscription": {
                "plan": "professional",
                "status": "active",
                "expires_at": "2026-01-19T00:44:02Z"
            },
            "settings": {
                "timezone": "Asia/Tokyo",
                "currency": "JPY",
                "units": "metric"
            },
            "limits": {
                "users": 50,
                "fields": 1000,
                "storage": "1TB"
            },
            "usage": {
                "users": 35,
                "fields": 450,
                "storage": "650GB"
            },
            "created_at": "2025-01-01T00:00:00Z",
            "updated_at": "2025-01-19T00:44:02Z"
        }
    ]
}
```

### System Configuration

#### Get System Settings
```http
GET /api/v1/admin/settings
```

##### Response
```json
{
    "data": {
        "security": {
            "password_policy": {
                "min_length": 12,
                "require_uppercase": true,
                "require_numbers": true,
                "require_symbols": true,
                "max_age_days": 90
            },
            "session": {
                "timeout_minutes": 30,
                "max_concurrent": 3
            },
            "mfa": {
                "required_for_roles": ["admin", "farm_manager"],
                "methods": ["totp", "sms"]
            }
        },
        "api": {
            "rate_limits": {
                "default": {
                    "requests_per_hour": 1000,
                    "burst": 50
                },
                "upload": {
                    "requests_per_hour": 100,
                    "max_file_size": "100MB"
                }
            },
            "cors": {
                "allowed_origins": ["https://app.agritech.com"],
                "allowed_methods": ["GET", "POST", "PUT", "DELETE"]
            }
        },
        "notifications": {
            "email": {
                "from_address": "no-reply@agritech.com",
                "smtp_settings": {
                    "host": "smtp.agritech.com",
                    "port": 587,
                    "encryption": "tls"
                }
            },
            "sms": {
                "provider": "twilio",
                "default_country": "JP"
            }
        },
        "storage": {
            "provider": "aws_s3",
            "region": "ap-northeast-1",
            "bucket_prefix": "agritech-prod"
        },
        "features": {
            "beta_features": {
                "ai_predictions": true,
                "satellite_imagery": false
            },
            "maintenance_mode": false
        }
    }
}
```

#### Update System Settings
```http
PUT /api/v1/admin/settings
```

##### Request Body
```json
{
    "settings": {
        "security": {
            "password_policy": {
                "min_length": 14,
                "max_age_days": 60
            }
        },
        "features": {
            "beta_features": {
                "satellite_imagery": true
            }
        }
    }
}
```

### Audit Logs

#### Get Audit Logs
```http
GET /api/v1/admin/audit-logs
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| action | string | Filter by action | all |
| user_id | string | Filter by user | - |
| resource | string | Filter by resource | all |
| start_date | string | Start date | -7d |
| end_date | string | End date | now |

##### Response
```json
{
    "data": [
        {
            "id": "al12345",
            "timestamp": "2025-01-19T00:44:02Z",
            "action": "user.create",
            "status": "success",
            "user": {
                "id": "u12345",
                "email": "admin@agritech.com"
            },
            "resource": {
                "type": "user",
                "id": "u12346"
            },
            "changes": {
                "status": {
                    "from": null,
                    "to": "pending"
                },
                "roles": {
                    "from": [],
                    "to": ["field_operator"]
                }
            },
            "metadata": {
                "ip_address": "192.168.1.1",
                "user_agent": "Mozilla/5.0..."
            }
        }
    ]
}
```

## Access Control

### Role Hierarchy
| Role | Level | Inherits |
|------|-------|----------|
| system_admin | 100 | all |
| org_admin | 80 | org_manager |
| farm_manager | 60 | field_operator |
| field_operator | 40 | - |
| viewer | 20 | - |

### Permission Types
| Type | Description |
|------|-------------|
| read | View access |
| write | Create/Update access |
| delete | Delete access |
| admin | Administrative access |

### Resource Types
| Resource | Description |
|----------|-------------|
| users | User accounts |
| roles | Role definitions |
| organizations | Organization settings |
| fields | Field data |
| crops | Crop data |
| equipment | Equipment data |

## Error Responses

### Authentication Error (401)
```json
{
    "error": {
        "code": "AUTH_ERROR",
        "message": "Authentication failed",
        "details": [
            {
                "code": "INVALID_TOKEN",
                "message": "Admin access token is invalid or expired"
            }
        ]
    }
}
```

### Authorization Error (403)
```json
{
    "error": {
        "code": "ACCESS_DENIED",
        "message": "Insufficient permissions",
        "details": [
            {
                "code": "ROLE_REQUIRED",
                "message": "System admin role required for this operation"
            }
        ]
    }
}
```

### Validation Error (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid request parameters",
        "details": [
            {
                "field": "email",
                "code": "INVALID_FORMAT",
                "message": "Invalid email format"
            }
        ]
    }
}
```

## Rate Limiting
- User management: 100 requests per hour
- Role management: 50 requests per hour
- System settings: 20 requests per hour
- Audit logs: 200 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For administrative events, register webhook endpoints at:
```
POST /api/v1/admin/webhooks
```

### Webhook Payload Format
```json
{
    "type": "admin_event",
    "timestamp": "2025-01-19T00:44:02.123Z",
    "event": {
        "type": "user.created",
        "user_id": "u12346",
        "organization_id": "org12345",
        "changes": {
            "status": "pending",
            "roles": ["field_operator"]
        }
    }
}
```

## Security Considerations

### Access Requirements
- Admin API access requires special admin tokens
- All requests must use HTTPS
- IP whitelist restrictions available
- MFA required for sensitive operations

### Audit Requirements
- All administrative actions are logged
- Logs are immutable and retained for 1 year
- Critical changes require approval workflow
- Real-time alerts for suspicious activities
