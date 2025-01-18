# Notifications API

## Overview
The Notifications API enables real-time alerts, updates, and messages delivery across the platform. It supports multiple notification channels, customizable delivery rules, and notification preferences management.

## Endpoints

### Get Notifications
```http
GET /api/v1/notifications
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| status | string | Filter by status | unread |
| type | string | Filter by type | all |
| priority | string | Filter by priority | all |
| start_date | string | Start date | -7d |
| end_date | string | End date | now |
| sort | string | Sort field (-timestamp) | -timestamp |
| page | integer | Page number | 1 |
| per_page | integer | Items per page | 20 |

#### Response
```json
{
    "data": [
        {
            "id": "n12345",
            "type": "alert",
            "category": "weather",
            "priority": "high",
            "title": "Frost Warning",
            "message": "Potential frost conditions expected tonight",
            "details": {
                "field_id": "f12345",
                "forecast": {
                    "temperature": {
                        "value": -2,
                        "unit": "°C"
                    },
                    "time": "2025-01-20T03:00:00Z"
                },
                "recommended_actions": [
                    "Activate frost protection systems",
                    "Monitor crop conditions"
                ]
            },
            "status": "unread",
            "timestamp": "2025-01-19T00:39:40Z",
            "expires_at": "2025-01-20T09:00:00Z",
            "actions": [
                {
                    "type": "view_forecast",
                    "url": "/weather/forecast/f12345"
                },
                {
                    "type": "create_task",
                    "url": "/tasks/new?template=frost_protection"
                }
            ]
        }
    ],
    "meta": {
        "unread_count": 5,
        "page": 1,
        "per_page": 20,
        "total": 45
    }
}
```

### Update Notification Status
```http
PUT /api/v1/notifications/{notification_id}/status
```

#### Request Body
```json
{
    "status": "read",
    "action_taken": "viewed_forecast"
}
```

#### Response
```json
{
    "data": {
        "id": "n12345",
        "status": "read",
        "read_at": "2025-01-19T00:39:40Z",
        "action_history": [
            {
                "action": "viewed_forecast",
                "timestamp": "2025-01-19T00:39:40Z"
            }
        ]
    }
}
```

### Configure Notification Preferences
```http
PUT /api/v1/notifications/preferences
```

#### Request Body
```json
{
    "preferences": {
        "channels": {
            "email": {
                "enabled": true,
                "address": "farmer@example.com"
            },
            "sms": {
                "enabled": true,
                "number": "+81-90-1234-5678"
            },
            "push": {
                "enabled": true,
                "devices": ["mobile_app", "web_browser"]
            }
        },
        "schedules": {
            "quiet_hours": {
                "start": "22:00",
                "end": "06:00",
                "timezone": "Asia/Tokyo"
            },
            "digest": {
                "enabled": true,
                "frequency": "daily",
                "time": "18:00"
            }
        },
        "filters": {
            "weather": {
                "min_priority": "medium",
                "channels": ["sms", "push"]
            },
            "tasks": {
                "min_priority": "low",
                "channels": ["email"]
            },
            "system": {
                "min_priority": "high",
                "channels": ["email", "sms"]
            }
        }
    }
}
```

#### Response
```json
{
    "data": {
        "preferences": {
            "channels": {
                "email": {
                    "enabled": true,
                    "address": "farmer@example.com",
                    "verified": true
                },
                "sms": {
                    "enabled": true,
                    "number": "+81-90-1234-5678",
                    "verified": true
                },
                "push": {
                    "enabled": true,
                    "devices": ["mobile_app", "web_browser"]
                }
            },
            "schedules": {
                "quiet_hours": {
                    "start": "22:00",
                    "end": "06:00",
                    "timezone": "Asia/Tokyo"
                },
                "digest": {
                    "enabled": true,
                    "frequency": "daily",
                    "time": "18:00",
                    "next_delivery": "2025-01-19T18:00:00+09:00"
                }
            },
            "filters": {
                "weather": {
                    "min_priority": "medium",
                    "channels": ["sms", "push"]
                },
                "tasks": {
                    "min_priority": "low",
                    "channels": ["email"]
                },
                "system": {
                    "min_priority": "high",
                    "channels": ["email", "sms"]
                }
            }
        },
        "updated_at": "2025-01-19T00:39:40Z"
    }
}
```

### Send Custom Notification
```http
POST /api/v1/notifications
```

#### Request Body
```json
{
    "notification": {
        "type": "custom",
        "recipients": [
            {
                "type": "user",
                "id": "u12345"
            },
            {
                "type": "role",
                "id": "field_manager"
            }
        ],
        "priority": "high",
        "title": "Equipment Maintenance Required",
        "message": "Tractor T-123 requires immediate maintenance",
        "details": {
            "equipment_id": "e12345",
            "maintenance_type": "emergency",
            "location": "Field A"
        },
        "actions": [
            {
                "type": "view_equipment",
                "label": "View Details",
                "url": "/equipment/e12345"
            },
            {
                "type": "create_task",
                "label": "Schedule Maintenance",
                "url": "/tasks/new?template=equipment_maintenance"
            }
        ],
        "expiration": "2025-01-20T00:39:40Z"
    }
}
```

#### Response
```json
{
    "data": {
        "id": "n12346",
        "status": "sent",
        "recipients": {
            "total": 3,
            "successful": 3,
            "failed": 0
        },
        "delivery_status": [
            {
                "channel": "email",
                "status": "delivered",
                "timestamp": "2025-01-19T00:39:40Z"
            },
            {
                "channel": "push",
                "status": "delivered",
                "timestamp": "2025-01-19T00:39:40Z"
            }
        ]
    }
}
```

## Notification Types

### System Notifications
| Type | Description | Priority |
|------|-------------|----------|
| system_update | System updates and maintenance | low |
| security_alert | Security-related notifications | high |
| api_status | API service status changes | medium |

### Weather Notifications
| Type | Description | Priority |
|------|-------------|----------|
| frost_warning | Frost risk alerts | high |
| rain_alert | Precipitation alerts | medium |
| weather_condition | General weather updates | low |

### Task Notifications
| Type | Description | Priority |
|------|-------------|----------|
| task_assigned | New task assignments | medium |
| task_due | Task due date reminders | medium |
| task_status | Task status updates | low |

### Equipment Notifications
| Type | Description | Priority |
|------|-------------|----------|
| maintenance_due | Scheduled maintenance reminders | medium |
| equipment_alert | Equipment malfunction alerts | high |
| calibration_needed | Sensor calibration reminders | low |

### Crop Notifications
| Type | Description | Priority |
|------|-------------|----------|
| growth_stage | Crop growth stage updates | low |
| pest_alert | Pest detection warnings | high |
| harvest_timing | Optimal harvest time alerts | medium |

## Delivery Channels

### Email
- HTML and plain text support
- Attachment support
- Click tracking
- Delivery status tracking

### SMS
- Unicode support
- Delivery confirmation
- Reply handling
- Character limit handling

### Push Notifications
- Web push (browsers)
- Mobile push (iOS/Android)
- Rich notifications support
- Action buttons

### In-App Notifications
- Real-time delivery
- Read status tracking
- Action tracking
- Persistent storage

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid notification parameters",
        "details": [
            {
                "field": "recipients",
                "code": "INVALID_FORMAT",
                "message": "At least one recipient must be specified"
            }
        ]
    }
}
```

### Channel Error (502)
```json
{
    "error": {
        "code": "DELIVERY_ERROR",
        "message": "Notification delivery failed",
        "details": [
            {
                "channel": "sms",
                "code": "PROVIDER_ERROR",
                "message": "SMS service provider unavailable"
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
        "message": "Too many notifications",
        "details": [
            {
                "limit": "100 per hour",
                "reset_at": "2025-01-19T01:39:40Z"
            }
        ]
    }
}
```

## Rate Limiting
- Get notifications: 1000 requests per hour
- Send notifications: 100 per hour per channel
- Preference updates: 100 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## WebSocket API
For real-time notifications, connect to:
```
wss://api.agritech.com/v1/notifications/stream
```

### Stream Message Format
```json
{
    "type": "notification",
    "id": "n12345",
    "timestamp": "2025-01-19T00:39:40.123Z",
    "data": {
        "type": "alert",
        "priority": "high",
        "title": "Frost Warning",
        "message": "Potential frost conditions expected tonight"
    }
}
```

## Push Notification Service Workers
For web push notifications, register service worker at:
```
/notifications/service-worker.js
```

### Subscription Management
```http
POST /api/v1/notifications/push/subscriptions
```

```json
{
    "subscription": {
        "endpoint": "https://fcm.googleapis.com/fcm/send/...",
        "keys": {
            "p256dh": "base64-encoded-key",
            "auth": "base64-encoded-auth"
        }
    },
    "device_info": {
        "type": "web_browser",
        "platform": "Windows",
        "browser": "Chrome"
    }
}
```
