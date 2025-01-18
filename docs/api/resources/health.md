# Health API

## Overview
The Health API provides comprehensive system health monitoring, diagnostics, and alerting capabilities. It enables real-time monitoring of system components, proactive issue detection, and automated incident response.

## Endpoints

### Health Checks

#### Get System Health
```http
GET /api/v1/health/status
```

##### Response
```json
{
    "data": {
        "status": "healthy",
        "timestamp": "2025-01-19T00:59:37Z",
        "components": {
            "database": {
                "status": "healthy",
                "latency": "5ms",
                "connections": 50
            },
            "cache": {
                "status": "healthy",
                "hit_rate": 0.95,
                "memory_usage": "80%"
            },
            "storage": {
                "status": "degraded",
                "available_space": "100GB",
                "usage": "85%"
            },
            "services": {
                "scheduler": {
                    "status": "healthy",
                    "active_jobs": 50,
                    "failed_jobs": 2
                },
                "notification": {
                    "status": "healthy",
                    "queue_size": 100,
                    "success_rate": 0.99
                }
            }
        },
        "metrics": {
            "cpu_usage": "60%",
            "memory_usage": "70%",
            "disk_io": "5MB/s",
            "network_io": "10MB/s"
        }
    }
}
```

### Diagnostics

#### Run Diagnostic Test
```http
POST /api/v1/health/diagnostics
```

##### Request Body
```json
{
    "diagnostic": {
        "type": "connectivity",
        "target": "database",
        "options": {
            "timeout": "30s",
            "depth": "full",
            "collect_metrics": true
        }
    }
}
```

##### Response
```json
{
    "data": {
        "diagnostic_id": "diag12345",
        "type": "connectivity",
        "target": "database",
        "status": "completed",
        "results": {
            "connectivity": {
                "status": "passed",
                "latency": "5ms",
                "throughput": "1000ops/s"
            },
            "replication": {
                "status": "passed",
                "lag": "100ms",
                "consistency": "strong"
            },
            "connection_pool": {
                "status": "warning",
                "active": 45,
                "idle": 5,
                "max": 50
            }
        },
        "recommendations": [
            {
                "priority": "medium",
                "message": "Consider increasing connection pool size",
                "details": "Connection pool utilization is at 90%"
            }
        ]
    }
}
```

### Alert Management

#### Get Active Alerts
```http
GET /api/v1/health/alerts
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| severity | string | Alert severity | all |
| status | string | Alert status | active |
| component | string | Component type | all |

##### Response
```json
{
    "data": {
        "alerts": [
            {
                "id": "alert12345",
                "severity": "warning",
                "component": "storage",
                "message": "High storage usage detected",
                "details": {
                    "usage": "85%",
                    "threshold": "80%",
                    "trend": "increasing"
                },
                "created_at": "2025-01-19T00:30:00Z",
                "status": "active",
                "acknowledgement": {
                    "required": true,
                    "acknowledged_by": null
                },
                "metrics": {
                    "current_value": "85%",
                    "baseline": "70%",
                    "change_rate": "+1%/hour"
                }
            }
        ],
        "summary": {
            "total": 1,
            "by_severity": {
                "critical": 0,
                "warning": 1,
                "info": 0
            }
        }
    }
}
```

## Health Components

### Component Types
| Type | Description |
|------|-------------|
| database | Database health |
| cache | Cache system |
| storage | Storage system |
| service | Microservices |
| network | Network health |

### Health States
| State | Description |
|-------|-------------|
| healthy | Fully operational |
| degraded | Partially impaired |
| unhealthy | Not operational |
| unknown | Status unclear |

### Metric Types
| Type | Description |
|------|-------------|
| utilization | Resource usage |
| performance | Speed metrics |
| reliability | Error rates |
| availability | Uptime stats |

## Diagnostic Tests

### Test Types
| Type | Description |
|------|-------------|
| connectivity | Connection tests |
| performance | Speed tests |
| consistency | Data checks |
| security | Security scans |

### Test Depths
| Depth | Description |
|-------|-------------|
| basic | Quick check |
| standard | Normal depth |
| full | Deep analysis |
| custom | Custom scope |

### Collection Options
| Option | Description |
|--------|-------------|
| metrics | Performance data |
| logs | System logs |
| traces | Request traces |
| heap | Memory dumps |

## Alert Management

### Alert Severities
| Severity | Description |
|----------|-------------|
| critical | Immediate action |
| warning | Attention needed |
| info | Informational |
| debug | Debug info |

### Alert States
| State | Description |
|-------|-------------|
| active | Current alert |
| acknowledged | Seen by admin |
| resolved | Fixed issue |
| silenced | Temporarily muted |

### Response Types
| Type | Description |
|------|-------------|
| auto_heal | Automatic fix |
| notification | Alert staff |
| escalation | Increase priority |
| failover | Switch backup |

## Error Responses

### Service Unavailable (503)
```json
{
    "error": {
        "code": "SERVICE_UNAVAILABLE",
        "message": "Health check failed",
        "details": [
            {
                "component": "database",
                "code": "CONNECTION_ERROR",
                "message": "Unable to connect to database"
            }
        ]
    }
}
```

### Diagnostic Failed (500)
```json
{
    "error": {
        "code": "DIAGNOSTIC_FAILED",
        "message": "Diagnostic test failed",
        "details": [
            {
                "test": "connectivity",
                "code": "TIMEOUT",
                "message": "Connection test timed out after 30s"
            }
        ]
    }
}
```

### Invalid Request (400)
```json
{
    "error": {
        "code": "INVALID_REQUEST",
        "message": "Invalid diagnostic configuration",
        "details": [
            {
                "field": "depth",
                "code": "INVALID_VALUE",
                "message": "Invalid test depth: ultra"
            }
        ]
    }
}
```

## Rate Limiting
- Health checks: 60 requests per minute
- Diagnostics: 10 requests per minute
- Alert operations: 30 requests per minute
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For health events, register webhook endpoints at:
```
POST /api/v1/health/webhooks
```

### Webhook Payload Format
```json
{
    "type": "health_event",
    "timestamp": "2025-01-19T00:59:37.123Z",
    "event": {
        "type": "component_degraded",
        "component": "storage",
        "details": {
            "status": "degraded",
            "metric": "disk_usage",
            "value": "85%",
            "threshold": "80%"
        }
    }
}
```

## Best Practices

### Monitoring Strategy
1. Define clear thresholds
2. Monitor key metrics
3. Set up trending
4. Configure alerts

### Diagnostic Procedures
1. Regular health checks
2. Automated testing
3. Performance profiling
4. Incident tracking

### Alert Management
1. Define severity levels
2. Set up escalations
3. Document responses
4. Track resolution

### Resource Optimization
1. Monitor resource usage
2. Set up auto-scaling
3. Implement failover
4. Optimize performance
