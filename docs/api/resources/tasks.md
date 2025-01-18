# Task Management API

## Overview
The Task Management API enables planning, tracking, and managing agricultural operations. It supports task scheduling, resource allocation, and progress monitoring while integrating with weather forecasts and crop management data.

## Endpoints

### List Tasks
```http
GET /api/v1/tasks
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field | - |
| status | string | Filter by status | all |
| priority | string | Filter by priority | all |
| assigned_to | string | Filter by assignee | - |
| start_date | string | Start date range | today |
| end_date | string | End date range | - |
| type | string | Filter by task type | all |
| sort | string | Sort field (-due_date, priority) | -due_date |
| page | integer | Page number | 1 |
| per_page | integer | Items per page | 20 |

#### Response
```json
{
    "data": [
        {
            "id": "t12345",
            "type": "fertilization",
            "title": "Apply Spring Fertilizer",
            "description": "Apply NPK fertilizer to winter wheat field",
            "field_id": "f12345",
            "crop_id": "c12345",
            "status": "scheduled",
            "priority": "high",
            "assigned_to": "john.smith",
            "schedule": {
                "planned_start": "2025-01-20T08:00:00Z",
                "planned_end": "2025-01-20T12:00:00Z",
                "estimated_duration": "4h"
            },
            "weather_dependent": true,
            "weather_requirements": {
                "no_rain_before": "6h",
                "no_rain_after": "24h",
                "wind_speed_below": {
                    "value": 5,
                    "unit": "m/s"
                }
            },
            "resources": [
                {
                    "type": "equipment",
                    "id": "e12345",
                    "name": "Fertilizer Spreader"
                },
                {
                    "type": "material",
                    "id": "m12345",
                    "name": "NPK 20-20-20",
                    "quantity": {
                        "value": 500,
                        "unit": "kg"
                    }
                }
            ],
            "created_at": "2025-01-19T00:34:58Z",
            "updated_at": "2025-01-19T00:34:58Z"
        }
    ],
    "meta": {
        "page": 1,
        "per_page": 20,
        "total": 45
    }
}
```

### Create Task
```http
POST /api/v1/tasks
```

#### Request Body
```json
{
    "task": {
        "type": "fertilization",
        "title": "Apply Spring Fertilizer",
        "description": "Apply NPK fertilizer to winter wheat field",
        "field_id": "f12345",
        "crop_id": "c12345",
        "priority": "high",
        "assigned_to": "john.smith",
        "schedule": {
            "planned_start": "2025-01-20T08:00:00Z",
            "planned_end": "2025-01-20T12:00:00Z"
        },
        "weather_requirements": {
            "no_rain_before": "6h",
            "no_rain_after": "24h",
            "wind_speed_below": {
                "value": 5,
                "unit": "m/s"
            }
        },
        "resources": [
            {
                "type": "equipment",
                "id": "e12345"
            },
            {
                "type": "material",
                "id": "m12345",
                "quantity": {
                    "value": 500,
                    "unit": "kg"
                }
            }
        ]
    }
}
```

#### Response
```json
{
    "data": {
        "id": "t12345",
        "status": "scheduled",
        "schedule": {
            "planned_start": "2025-01-20T08:00:00Z",
            "planned_end": "2025-01-20T12:00:00Z",
            "estimated_duration": "4h"
        },
        "weather_forecast": {
            "suitable": true,
            "conditions": [
                {
                    "time": "2025-01-20T08:00:00Z",
                    "precipitation_probability": 10,
                    "wind_speed": {
                        "value": 3.2,
                        "unit": "m/s"
                    }
                }
            ]
        },
        "created_at": "2025-01-19T00:34:58Z"
    }
}
```

### Update Task Status
```http
PUT /api/v1/tasks/{task_id}/status
```

#### Request Body
```json
{
    "status": "in_progress",
    "notes": "Started fertilizer application",
    "actual_start": "2025-01-20T08:15:00Z",
    "progress": {
        "completed_area": {
            "value": 2.5,
            "unit": "ha"
        },
        "material_used": {
            "value": 200,
            "unit": "kg"
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "t12345",
        "status": "in_progress",
        "schedule": {
            "planned_start": "2025-01-20T08:00:00Z",
            "planned_end": "2025-01-20T12:00:00Z",
            "actual_start": "2025-01-20T08:15:00Z",
            "estimated_completion": "2025-01-20T12:15:00Z"
        },
        "progress": {
            "percentage": 40,
            "completed_area": {
                "value": 2.5,
                "unit": "ha"
            },
            "material_used": {
                "value": 200,
                "unit": "kg"
            }
        },
        "updated_at": "2025-01-19T00:34:58Z"
    }
}
```

### Complete Task
```http
POST /api/v1/tasks/{task_id}/complete
```

#### Request Body
```json
{
    "completion_details": {
        "actual_end": "2025-01-20T11:45:00Z",
        "outcome": "success",
        "notes": "Completed fertilizer application",
        "measurements": {
            "area_covered": {
                "value": 6.2,
                "unit": "ha"
            },
            "material_used": {
                "value": 480,
                "unit": "kg"
            }
        },
        "observations": [
            {
                "type": "soil_condition",
                "value": "good moisture level"
            }
        ],
        "attachments": [
            {
                "type": "photo",
                "url": "https://storage.agritech.com/tasks/t12345/completion_photo.jpg"
            }
        ]
    }
}
```

#### Response
```json
{
    "data": {
        "id": "t12345",
        "status": "completed",
        "schedule": {
            "planned_start": "2025-01-20T08:00:00Z",
            "planned_end": "2025-01-20T12:00:00Z",
            "actual_start": "2025-01-20T08:15:00Z",
            "actual_end": "2025-01-20T11:45:00Z",
            "duration": "3h30m"
        },
        "measurements": {
            "area_covered": {
                "value": 6.2,
                "unit": "ha"
            },
            "material_used": {
                "value": 480,
                "unit": "kg"
            },
            "application_rate": {
                "value": 77.4,
                "unit": "kg/ha"
            }
        },
        "updated_at": "2025-01-19T00:34:58Z"
    }
}
```

### Get Task Timeline
```http
GET /api/v1/tasks/{task_id}/timeline
```

#### Response
```json
{
    "data": {
        "task_id": "t12345",
        "events": [
            {
                "timestamp": "2025-01-19T00:34:58Z",
                "type": "created",
                "user": "farm.manager",
                "details": "Task scheduled"
            },
            {
                "timestamp": "2025-01-20T08:15:00Z",
                "type": "started",
                "user": "john.smith",
                "details": "Started fertilizer application"
            },
            {
                "timestamp": "2025-01-20T11:45:00Z",
                "type": "completed",
                "user": "john.smith",
                "details": "Completed fertilizer application"
            }
        ]
    }
}
```

## Task Object

### Attributes
| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique identifier |
| type | string | Task type |
| title | string | Task title |
| description | string | Detailed description |
| field_id | string | Associated field |
| crop_id | string | Associated crop |
| status | string | Current status |
| priority | string | Task priority |
| assigned_to | string | Assigned user |
| schedule | object | Timing information |
| weather_dependent | boolean | Weather dependency |
| weather_requirements | object | Required weather conditions |
| resources | array | Required resources |

### Task Types
| Type | Description |
|------|-------------|
| planting | Crop planting operations |
| fertilization | Fertilizer application |
| irrigation | Irrigation operations |
| pest_control | Pest management |
| harvesting | Crop harvesting |
| maintenance | Equipment/field maintenance |
| inspection | Field/crop inspection |

### Status Values
- scheduled: Task is planned
- pending: Waiting for conditions
- in_progress: Currently executing
- completed: Successfully finished
- cancelled: Cancelled before completion
- failed: Unsuccessful completion

### Priority Levels
- critical: Immediate attention required
- high: Important task
- medium: Normal priority
- low: Can be delayed if needed

## Resource Management

### Equipment Types
| Type | Attributes |
|------|------------|
| tractor | power, attachments |
| spreader | capacity, spread width |
| sprayer | tank size, boom width |
| harvester | type, capacity |

### Material Types
| Type | Attributes |
|------|------------|
| seed | variety, treatment |
| fertilizer | npk ratio, form |
| pesticide | active ingredient, category |
| fuel | type, grade |

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid task parameters",
        "details": [
            {
                "field": "planned_start",
                "code": "INVALID_DATE",
                "message": "Start date must be in the future"
            }
        ]
    }
}
```

### Resource Conflict (409)
```json
{
    "error": {
        "code": "RESOURCE_CONFLICT",
        "message": "Resource not available",
        "details": [
            {
                "field": "equipment",
                "code": "ALREADY_SCHEDULED",
                "message": "Fertilizer spreader is already scheduled for this time"
            }
        ]
    }
}
```

### Weather Conditions (403)
```json
{
    "error": {
        "code": "WEATHER_RESTRICTION",
        "message": "Unsuitable weather conditions",
        "details": [
            {
                "field": "wind_speed",
                "code": "EXCEEDS_LIMIT",
                "message": "Forecast wind speed (6.5 m/s) exceeds limit (5 m/s)"
            }
        ]
    }
}
```

## Rate Limiting
- List operations: 1000 requests per hour
- Create/Update operations: 500 requests per hour
- Timeline queries: 200 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## WebSocket API
For real-time task updates, connect to:
```
wss://api.agritech.com/v1/tasks/stream?field_id=f12345
```

### Stream Message Format
```json
{
    "type": "task_update",
    "task_id": "t12345",
    "timestamp": "2025-01-19T00:34:58.123Z",
    "event": {
        "type": "status_change",
        "old_status": "scheduled",
        "new_status": "in_progress",
        "user": "john.smith"
    }
}
```
