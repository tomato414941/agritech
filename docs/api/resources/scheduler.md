# Scheduler API

## Overview
The Scheduler API enables management of time-based operations and recurring tasks. It provides capabilities for job scheduling, timer management, and periodic execution of various system operations.

## Endpoints

### Job Management

#### Create Schedule
```http
POST /api/v1/scheduler/jobs
```

##### Request Body
```json
{
    "schedule": {
        "name": "Daily Field Monitoring",
        "description": "Collect and analyze daily field sensor data",
        "type": "recurring",
        "timing": {
            "cron": "0 0 * * *",
            "timezone": "Asia/Tokyo"
        },
        "action": {
            "type": "workflow",
            "workflow_id": "wf12345",
            "parameters": {
                "operation": "sensor_data_collection",
                "field_ids": ["f12345", "f12346"]
            }
        },
        "retry": {
            "attempts": 3,
            "interval": "5m",
            "backoff": "exponential"
        },
        "notifications": {
            "on_success": ["email:manager@example.com"],
            "on_failure": ["email:admin@example.com", "webhook:https://example.com/webhook"]
        }
    }
}
```

##### Response
```json
{
    "data": {
        "job_id": "job12345",
        "status": "active",
        "created_at": "2025-01-19T00:57:20Z",
        "next_run": "2025-01-20T00:00:00Z",
        "schedule": {
            "cron": "0 0 * * *",
            "timezone": "Asia/Tokyo"
        }
    }
}
```

#### Get Job Status
```http
GET /api/v1/scheduler/jobs/{job_id}
```

##### Response
```json
{
    "data": {
        "job_id": "job12345",
        "status": "active",
        "statistics": {
            "total_runs": 50,
            "successful_runs": 48,
            "failed_runs": 2,
            "last_run": {
                "status": "success",
                "started_at": "2025-01-19T00:00:00Z",
                "completed_at": "2025-01-19T00:05:00Z",
                "duration": "5m"
            },
            "next_run": "2025-01-20T00:00:00Z"
        },
        "history": [
            {
                "execution_id": "exec12345",
                "status": "success",
                "started_at": "2025-01-19T00:00:00Z",
                "completed_at": "2025-01-19T00:05:00Z",
                "result": {
                    "sensors_processed": 100,
                    "data_points_collected": 1000
                }
            }
        ]
    }
}
```

### Timer Management

#### Create Timer
```http
POST /api/v1/scheduler/timers
```

##### Request Body
```json
{
    "timer": {
        "name": "Irrigation Cycle",
        "type": "interval",
        "interval": {
            "duration": "2h",
            "start_time": "2025-01-19T06:00:00Z",
            "end_time": "2025-01-19T18:00:00Z"
        },
        "action": {
            "type": "api_call",
            "method": "POST",
            "endpoint": "/api/v1/fields/{field_id}/irrigation",
            "parameters": {
                "duration": 30,
                "intensity": "medium"
            }
        },
        "conditions": {
            "weather": {
                "no_rain_forecast": true,
                "temperature_above": 25
            },
            "soil_moisture": {
                "below_percentage": 30
            }
        }
    }
}
```

##### Response
```json
{
    "data": {
        "timer_id": "timer12345",
        "status": "active",
        "next_trigger": "2025-01-19T06:00:00Z",
        "remaining_triggers": 6
    }
}
```

### Calendar Management

#### Get Schedule Calendar
```http
GET /api/v1/scheduler/calendar
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| start_date | string | Start date | today |
| end_date | string | End date | +7d |
| types | array | Event types | all |

##### Response
```json
{
    "data": {
        "period": {
            "start": "2025-01-19",
            "end": "2025-01-26"
        },
        "events": [
            {
                "date": "2025-01-19",
                "events": [
                    {
                        "id": "job12345",
                        "type": "scheduled_job",
                        "name": "Daily Field Monitoring",
                        "time": "00:00",
                        "status": "pending"
                    },
                    {
                        "id": "timer12345",
                        "type": "timer",
                        "name": "Irrigation Cycle",
                        "time": "06:00",
                        "repeats": 6,
                        "interval": "2h"
                    }
                ]
            }
        ]
    }
}
```

## Schedule Types

### Recurring Patterns
| Pattern | Description | Example |
|---------|-------------|---------|
| cron | Cron expression | "0 0 * * *" |
| interval | Time interval | "2h" |
| daily | Daily at time | "09:00" |
| weekly | Weekly on day | "Monday 09:00" |
| monthly | Monthly on date | "1st 09:00" |

### Timer Types
| Type | Description |
|------|-------------|
| oneshot | Single execution |
| interval | Repeated execution |
| window | Time window based |
| condition | Condition based |

### Action Types
| Type | Description |
|------|-------------|
| workflow | Execute workflow |
| api_call | Call API endpoint |
| notification | Send notification |
| batch | Run batch job |

## Job Management

### Job States
| State | Description |
|-------|-------------|
| active | Ready to run |
| paused | Temporarily stopped |
| completed | Finished execution |
| failed | Execution failed |
| disabled | Permanently stopped |

### Retry Strategies
| Strategy | Description |
|----------|-------------|
| immediate | Retry immediately |
| fixed | Fixed interval |
| exponential | Increasing interval |
| custom | Custom strategy |

## Error Responses

### Invalid Schedule (400)
```json
{
    "error": {
        "code": "INVALID_SCHEDULE",
        "message": "Invalid schedule configuration",
        "details": [
            {
                "field": "cron",
                "code": "INVALID_EXPRESSION",
                "message": "Invalid cron expression: * * * *"
            }
        ]
    }
}
```

### Scheduling Conflict (409)
```json
{
    "error": {
        "code": "SCHEDULE_CONFLICT",
        "message": "Schedule conflicts with existing job",
        "details": [
            {
                "job_id": "job12346",
                "time": "2025-01-19T00:00:00Z",
                "resource": "field_f12345"
            }
        ]
    }
}
```

### Execution Error (500)
```json
{
    "error": {
        "code": "EXECUTION_ERROR",
        "message": "Job execution failed",
        "details": [
            {
                "job_id": "job12345",
                "execution_id": "exec12345",
                "code": "ACTION_FAILED",
                "message": "API endpoint not responding"
            }
        ]
    }
}
```

## Rate Limiting
- Schedule creation: 50 requests per hour
- Status checks: 300 requests per hour
- Calendar queries: 100 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For scheduler events, register webhook endpoints at:
```
POST /api/v1/scheduler/webhooks
```

### Webhook Payload Format
```json
{
    "type": "scheduler_event",
    "timestamp": "2025-01-19T00:57:20.123Z",
    "event": {
        "type": "job_completed",
        "job_id": "job12345",
        "execution_id": "exec12345",
        "details": {
            "status": "success",
            "duration": "5m",
            "result": {
                "sensors_processed": 100,
                "data_points_collected": 1000
            }
        }
    }
}
```

## Best Practices

### Schedule Design
1. Use appropriate time zones
2. Consider business hours
3. Avoid overlapping schedules
4. Set realistic intervals

### Resource Management
1. Monitor resource usage
2. Implement job quotas
3. Set execution timeouts
4. Clean up completed jobs

### Error Handling
1. Define retry strategies
2. Set up notifications
3. Monitor failure patterns
4. Maintain audit logs

### Performance
1. Distribute load evenly
2. Optimize execution windows
3. Monitor system impact
4. Handle concurrent executions
