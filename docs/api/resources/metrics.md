# Metrics API

## Overview
The Metrics API provides comprehensive monitoring, performance tracking, and usage analytics for the platform. It enables real-time monitoring of system health, resource utilization, and business metrics.

## Endpoints

### System Metrics

#### Get System Health
```http
GET /api/v1/metrics/health
```

##### Response
```json
{
    "data": {
        "timestamp": "2025-01-19T00:46:27Z",
        "status": "healthy",
        "components": {
            "api": {
                "status": "healthy",
                "latency": {
                    "p50": 85,
                    "p95": 150,
                    "p99": 250,
                    "unit": "ms"
                }
            },
            "database": {
                "status": "healthy",
                "connections": {
                    "active": 25,
                    "idle": 10,
                    "max": 100
                },
                "replication_lag": {
                    "value": 50,
                    "unit": "ms"
                }
            },
            "cache": {
                "status": "healthy",
                "hit_rate": 0.95,
                "memory_usage": {
                    "used": "2.5GB",
                    "total": "4GB"
                }
            },
            "storage": {
                "status": "healthy",
                "usage": {
                    "used": "750GB",
                    "total": "1TB"
                }
            }
        },
        "incidents": []
    }
}
```

#### Get Performance Metrics
```http
GET /api/v1/metrics/performance
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| start_time | string | Start time | -1h |
| end_time | string | End time | now |
| interval | string | Aggregation interval | 1m |
| metrics | array | Metrics to include | all |

##### Response
```json
{
    "data": {
        "period": {
            "start": "2025-01-19T00:16:27Z",
            "end": "2025-01-19T00:46:27Z",
            "interval": "1m"
        },
        "metrics": {
            "request_rate": {
                "current": 150,
                "mean": 125,
                "peak": 200,
                "unit": "requests/second"
            },
            "response_time": {
                "mean": 85,
                "p50": 75,
                "p95": 150,
                "p99": 250,
                "unit": "ms"
            },
            "error_rate": {
                "total": 0.1,
                "by_type": {
                    "4xx": 0.08,
                    "5xx": 0.02
                },
                "unit": "percent"
            },
            "cpu_usage": {
                "mean": 45,
                "peak": 65,
                "unit": "percent"
            },
            "memory_usage": {
                "mean": 75,
                "peak": 85,
                "unit": "percent"
            }
        }
    }
}
```

### Usage Metrics

#### Get API Usage
```http
GET /api/v1/metrics/usage/api
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| organization_id | string | Filter by organization | - |
| start_date | string | Start date | -30d |
| end_date | string | End date | now |
| group_by | string | Grouping (endpoint, method) | endpoint |

##### Response
```json
{
    "data": {
        "period": {
            "start": "2024-12-20",
            "end": "2025-01-19"
        },
        "total_requests": 1250000,
        "by_endpoint": [
            {
                "endpoint": "/fields",
                "method": "GET",
                "requests": 500000,
                "data_transferred": {
                    "in": "10GB",
                    "out": "50GB"
                },
                "response_time": {
                    "mean": 85,
                    "p95": 150,
                    "unit": "ms"
                },
                "error_rate": 0.1,
                "top_consumers": [
                    {
                        "organization_id": "org12345",
                        "requests": 100000
                    }
                ]
            }
        ],
        "trends": {
            "daily_requests": {
                "mean": 41666,
                "trend": "+5%"
            },
            "error_rate": {
                "mean": 0.1,
                "trend": "-2%"
            }
        }
    }
}
```

#### Get Resource Usage
```http
GET /api/v1/metrics/usage/resources
```

##### Response
```json
{
    "data": {
        "storage": {
            "total": {
                "used": "750GB",
                "allocated": "1TB"
            },
            "by_type": {
                "images": "500GB",
                "documents": "200GB",
                "sensor_data": "50GB"
            },
            "growth_rate": {
                "value": 20,
                "unit": "GB/month"
            }
        },
        "compute": {
            "api_requests": {
                "current": 150,
                "limit": 1000,
                "unit": "requests/second"
            },
            "background_jobs": {
                "active": 25,
                "queued": 10,
                "capacity": 100
            }
        },
        "database": {
            "rows": {
                "total": 15000000,
                "by_table": {
                    "fields": 5000,
                    "sensor_data": 10000000
                }
            },
            "size": {
                "data": "500GB",
                "indexes": "100GB"
            }
        }
    }
}
```

### Business Metrics

#### Get Organization Metrics
```http
GET /api/v1/metrics/business/organizations
```

##### Response
```json
{
    "data": {
        "total_organizations": 150,
        "active_organizations": 145,
        "by_plan": [
            {
                "plan": "enterprise",
                "count": 50,
                "revenue": {
                    "monthly": 250000,
                    "currency": "USD"
                }
            }
        ],
        "growth": {
            "organizations": {
                "new_this_month": 10,
                "churn_rate": 0.5,
                "unit": "percent"
            },
            "revenue": {
                "mrr": 500000,
                "growth_rate": 15,
                "unit": "percent"
            }
        }
    }
}
```

#### Get Feature Usage
```http
GET /api/v1/metrics/business/features
```

##### Response
```json
{
    "data": {
        "features": [
            {
                "name": "weather_alerts",
                "adoption_rate": 85,
                "active_users": 1200,
                "usage_frequency": {
                    "daily": 500,
                    "weekly": 2500,
                    "monthly": 8000
                },
                "satisfaction_score": 4.5
            }
        ],
        "user_engagement": {
            "daily_active_users": 1500,
            "monthly_active_users": 3000,
            "average_session_duration": {
                "value": 25,
                "unit": "minutes"
            }
        }
    }
}
```

### Billing Metrics

#### Get Usage Costs
```http
GET /api/v1/metrics/billing/costs
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| organization_id | string | Filter by organization | - |
| start_date | string | Start date | -30d |
| end_date | string | End date | now |

##### Response
```json
{
    "data": {
        "period": {
            "start": "2024-12-20",
            "end": "2025-01-19"
        },
        "total_cost": {
            "amount": 5250.75,
            "currency": "USD"
        },
        "by_service": [
            {
                "service": "api_requests",
                "usage": {
                    "quantity": 1250000,
                    "unit": "requests"
                },
                "cost": {
                    "amount": 1250.00,
                    "rate": "0.001 USD/request"
                }
            },
            {
                "service": "storage",
                "usage": {
                    "quantity": 750,
                    "unit": "GB"
                },
                "cost": {
                    "amount": 75.00,
                    "rate": "0.10 USD/GB"
                }
            }
        ],
        "forecasted": {
            "next_month": {
                "amount": 5500.00,
                "trend": "+5%"
            }
        }
    }
}
```

## Metric Types

### System Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| cpu_usage | CPU utilization | percent |
| memory_usage | Memory utilization | percent |
| disk_usage | Storage utilization | percent |
| network_io | Network traffic | bytes/sec |

### Performance Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| request_rate | Request throughput | req/sec |
| response_time | Response latency | ms |
| error_rate | Error frequency | percent |
| availability | System uptime | percent |

### Usage Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| api_calls | API request count | count |
| data_transfer | Data transferred | bytes |
| storage_used | Storage consumed | bytes |
| compute_time | Processing time | seconds |

### Business Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| active_users | Active user count | count |
| feature_usage | Feature utilization | percent |
| customer_growth | Customer increase | percent |
| revenue_growth | Revenue increase | percent |

## Error Responses

### Invalid Time Range (400)
```json
{
    "error": {
        "code": "INVALID_TIME_RANGE",
        "message": "Invalid time range specified",
        "details": [
            {
                "field": "start_time",
                "code": "FUTURE_DATE",
                "message": "Start time cannot be in the future"
            }
        ]
    }
}
```

### Data Unavailable (404)
```json
{
    "error": {
        "code": "DATA_UNAVAILABLE",
        "message": "Metric data not available",
        "details": [
            {
                "metric": "cpu_usage",
                "code": "NO_DATA",
                "message": "No data available for specified time range"
            }
        ]
    }
}
```

### Rate Limit Exceeded (429)
```json
{
    "error": {
        "code": "RATE_LIMIT_EXCEEDED",
        "message": "Too many metric requests",
        "details": [
            {
                "limit": "600 per hour",
                "reset_at": "2025-01-19T01:46:27Z"
            }
        ]
    }
}
```

## Rate Limiting
- System metrics: 600 requests per hour
- Usage metrics: 300 requests per hour
- Business metrics: 100 requests per hour
- Billing metrics: 50 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For metric alerts and thresholds, register webhook endpoints at:
```
POST /api/v1/metrics/alerts/webhooks
```

### Webhook Payload Format
```json
{
    "type": "metric_alert",
    "timestamp": "2025-01-19T00:46:27.123Z",
    "alert": {
        "metric": "error_rate",
        "threshold": 5,
        "current_value": 5.5,
        "severity": "warning",
        "description": "Error rate exceeded threshold"
    }
}
```

## Metric Retention
| Metric Type | Resolution | Retention Period |
|-------------|------------|------------------|
| System | 1 minute | 7 days |
| System | 5 minutes | 30 days |
| System | 1 hour | 90 days |
| Usage | 1 hour | 90 days |
| Usage | 1 day | 1 year |
| Business | 1 day | 2 years |
| Billing | 1 day | 7 years |
