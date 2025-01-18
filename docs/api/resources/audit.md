# Audit API

## Overview
The Audit API provides comprehensive audit logging, change tracking, and compliance management capabilities. It enables monitoring of all system activities, maintaining detailed audit trails, and ensuring regulatory compliance.

## Endpoints

### Audit Logs

#### Get Audit Logs
```http
GET /api/v1/audit/logs
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| start_time | string | Start timestamp | -24h |
| end_time | string | End timestamp | now |
| actor_type | string | User or system | all |
| action | string | Action type | all |
| resource_type | string | Resource type | all |
| severity | string | Event severity | all |

##### Response
```json
{
    "data": {
        "logs": [
            {
                "id": "al12345",
                "timestamp": "2025-01-19T00:55:59Z",
                "actor": {
                    "type": "user",
                    "id": "u12345",
                    "name": "John Smith",
                    "role": "farm_manager"
                },
                "action": {
                    "type": "update",
                    "description": "Updated irrigation schedule",
                    "severity": "normal"
                },
                "resource": {
                    "type": "field",
                    "id": "f12345",
                    "name": "North Field"
                },
                "changes": {
                    "irrigation_schedule": {
                        "from": {
                            "frequency": "daily",
                            "duration": 30
                        },
                        "to": {
                            "frequency": "twice_daily",
                            "duration": 20
                        }
                    }
                },
                "context": {
                    "ip_address": "192.168.1.1",
                    "user_agent": "Mozilla/5.0...",
                    "location": "Tokyo, Japan"
                },
                "metadata": {
                    "request_id": "req12345",
                    "correlation_id": "cor12345"
                }
            }
        ],
        "pagination": {
            "total": 1250,
            "page": 1,
            "per_page": 20
        }
    }
}
```

### Change History

#### Get Resource Changes
```http
GET /api/v1/audit/changes/{resource_type}/{resource_id}
```

##### Response
```json
{
    "data": {
        "resource": {
            "type": "field",
            "id": "f12345",
            "name": "North Field"
        },
        "changes": [
            {
                "id": "ch12345",
                "timestamp": "2025-01-19T00:55:59Z",
                "actor": {
                    "type": "user",
                    "id": "u12345",
                    "name": "John Smith"
                },
                "action": "update",
                "changes": {
                    "crop_type": {
                        "from": "wheat",
                        "to": "rice"
                    },
                    "area": {
                        "from": 10.5,
                        "to": 12.0,
                        "unit": "hectares"
                    }
                },
                "reason": "Crop rotation",
                "approval": {
                    "required": true,
                    "approved_by": {
                        "id": "u12346",
                        "name": "Jane Doe",
                        "role": "farm_director"
                    },
                    "approved_at": "2025-01-19T00:54:59Z"
                }
            }
        ]
    }
}
```

### Compliance Management

#### Get Compliance Report
```http
GET /api/v1/audit/compliance/reports
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| framework | string | Compliance framework | all |
| period | string | Reporting period | current_month |
| type | string | Report type | summary |

##### Response
```json
{
    "data": {
        "period": {
            "start": "2025-01-01T00:00:00Z",
            "end": "2025-01-19T00:55:59Z"
        },
        "frameworks": [
            {
                "name": "organic_certification",
                "version": "2025.1",
                "status": "compliant",
                "requirements": [
                    {
                        "id": "org1.1",
                        "description": "Pesticide usage tracking",
                        "status": "compliant",
                        "evidence": [
                            {
                                "type": "audit_log",
                                "count": 150,
                                "coverage": "100%"
                            }
                        ]
                    }
                ],
                "certifications": [
                    {
                        "id": "cert12345",
                        "issuer": "Organic Certifiers Inc",
                        "issued_date": "2025-01-01",
                        "valid_until": "2026-01-01"
                    }
                ]
            }
        ],
        "summary": {
            "total_requirements": 50,
            "compliant": 48,
            "non_compliant": 1,
            "not_applicable": 1,
            "compliance_rate": 98
        }
    }
}
```

## Audit Events

### Event Types
| Type | Description |
|------|-------------|
| create | Resource creation |
| read | Resource access |
| update | Resource modification |
| delete | Resource deletion |
| login | User authentication |
| export | Data export |
| import | Data import |

### Severity Levels
| Level | Description |
|-------|-------------|
| critical | Security-critical events |
| high | Important changes |
| normal | Regular operations |
| low | Informational events |

### Actor Types
| Type | Description |
|------|-------------|
| user | Human user |
| system | System process |
| service | External service |
| automation | Automated task |

## Compliance Frameworks

### Framework Types
| Type | Description |
|------|-------------|
| organic | Organic certification |
| food_safety | Food safety standards |
| environmental | Environmental regulations |
| data_privacy | Privacy regulations |

### Evidence Types
| Type | Description |
|------|-------------|
| audit_log | System audit logs |
| document | Documentation |
| certification | Official certification |
| test_result | Test results |

## Retention Policies

### Log Retention
| Type | Duration |
|------|----------|
| system_logs | 1 year |
| security_logs | 3 years |
| compliance_logs | 7 years |
| change_history | 10 years |

### Archive Policies
| Policy | Description |
|--------|-------------|
| compress | Compress after 30 days |
| archive | Archive after 90 days |
| backup | Weekly backups |
| delete | Delete after retention |

## Error Responses

### Invalid Query (400)
```json
{
    "error": {
        "code": "INVALID_QUERY",
        "message": "Invalid audit log query",
        "details": [
            {
                "field": "start_time",
                "code": "INVALID_FORMAT",
                "message": "Invalid timestamp format"
            }
        ]
    }
}
```

### Access Denied (403)
```json
{
    "error": {
        "code": "ACCESS_DENIED",
        "message": "Insufficient permissions",
        "details": [
            {
                "code": "MISSING_ROLE",
                "message": "Audit log access requires admin role"
            }
        ]
    }
}
```

### Resource Not Found (404)
```json
{
    "error": {
        "code": "NOT_FOUND",
        "message": "Audit record not found",
        "details": [
            {
                "code": "INVALID_ID",
                "message": "No audit record exists with ID: al12345"
            }
        ]
    }
}
```

## Rate Limiting
- Log retrieval: 300 requests per hour
- Change history: 100 requests per hour
- Compliance reports: 50 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For audit event notifications, register webhook endpoints at:
```
POST /api/v1/audit/webhooks
```

### Webhook Payload Format
```json
{
    "type": "audit_event",
    "timestamp": "2025-01-19T00:55:59.123Z",
    "event": {
        "type": "compliance_violation",
        "severity": "high",
        "details": {
            "framework": "organic_certification",
            "requirement": "org1.1",
            "violation": "Unauthorized pesticide usage detected"
        }
    }
}
```

## Best Practices

### Audit Logging
1. Log all security-relevant events
2. Include detailed context
3. Maintain data integrity
4. Ensure proper timestamps

### Compliance Management
1. Regular compliance checks
2. Document evidence collection
3. Monitor certification expiry
4. Track requirement changes

### Data Protection
1. Encrypt sensitive data
2. Implement access controls
3. Regular backups
4. Secure archival

### Performance Optimization
1. Index audit logs
2. Implement log rotation
3. Archive old records
4. Optimize queries
