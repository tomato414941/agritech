# Workflow API

## Overview
The Workflow API enables definition, execution, and management of complex business processes. It supports workflow templates, task management, state transitions, and integrations with other system components.

## Endpoints

### Workflow Definition

#### Create Workflow Definition
```http
POST /api/v1/workflows/definitions
```

##### Request Body
```json
{
    "workflow": {
        "name": "crop_planting_process",
        "version": "1.0",
        "description": "Standard process for crop planting",
        "trigger": {
            "type": "manual",
            "roles": ["farm_manager", "field_operator"]
        },
        "states": [
            {
                "name": "soil_preparation",
                "type": "task",
                "assignee": {
                    "role": "field_operator"
                },
                "actions": [
                    {
                        "name": "soil_test",
                        "type": "api_call",
                        "endpoint": "/api/v1/fields/{field_id}/soil-tests",
                        "method": "POST"
                    },
                    {
                        "name": "fertilizer_calculation",
                        "type": "service",
                        "service": "fertilizer_optimizer",
                        "input": {
                            "soil_test_id": "${soil_test.id}"
                        }
                    }
                ],
                "transitions": [
                    {
                        "to": "planting",
                        "condition": "soil_test.ph >= 6.0 AND soil_test.ph <= 7.5"
                    },
                    {
                        "to": "soil_treatment",
                        "condition": "soil_test.ph < 6.0 OR soil_test.ph > 7.5"
                    }
                ]
            }
        ],
        "timeouts": {
            "total": "7d",
            "per_state": "24h"
        }
    }
}
```

##### Response
```json
{
    "data": {
        "workflow_id": "wf12345",
        "version": "1.0",
        "created_at": "2025-01-19T00:53:33Z",
        "status": "active",
        "states": [
            {
                "name": "soil_preparation",
                "type": "task",
                "actions": ["soil_test", "fertilizer_calculation"]
            }
        ]
    }
}
```

### Workflow Execution

#### Start Workflow
```http
POST /api/v1/workflows/instances
```

##### Request Body
```json
{
    "instance": {
        "workflow_id": "wf12345",
        "context": {
            "field_id": "f12345",
            "crop_type": "rice",
            "planned_date": "2025-02-01"
        },
        "priority": "normal",
        "notifications": {
            "email": ["manager@example.com"],
            "webhook": "https://example.com/webhook"
        }
    }
}
```

##### Response
```json
{
    "data": {
        "instance_id": "wfi12345",
        "workflow_id": "wf12345",
        "status": "running",
        "current_state": "soil_preparation",
        "started_at": "2025-01-19T00:53:33Z",
        "context": {
            "field_id": "f12345",
            "crop_type": "rice"
        }
    }
}
```

#### Get Workflow Instance Status
```http
GET /api/v1/workflows/instances/{instance_id}
```

##### Response
```json
{
    "data": {
        "instance_id": "wfi12345",
        "workflow_id": "wf12345",
        "status": "running",
        "progress": {
            "completed_states": 2,
            "total_states": 5,
            "percentage": 40
        },
        "current_state": {
            "name": "soil_preparation",
            "started_at": "2025-01-19T00:53:33Z",
            "status": "in_progress",
            "assignee": {
                "role": "field_operator",
                "user_id": "u12345"
            }
        },
        "history": [
            {
                "state": "initiation",
                "started_at": "2025-01-19T00:53:33Z",
                "completed_at": "2025-01-19T00:54:33Z",
                "outcome": "success"
            }
        ]
    }
}
```

### Task Management

#### List Tasks
```http
GET /api/v1/workflows/tasks
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| status | string | Task status | pending |
| assignee | string | Assigned user/role | - |
| workflow_id | string | Filter by workflow | - |

##### Response
```json
{
    "data": {
        "tasks": [
            {
                "task_id": "task12345",
                "workflow_instance_id": "wfi12345",
                "type": "soil_preparation",
                "status": "pending",
                "assignee": {
                    "role": "field_operator",
                    "user_id": "u12345"
                },
                "due_date": "2025-01-20T00:53:33Z",
                "priority": "normal",
                "context": {
                    "field_id": "f12345",
                    "crop_type": "rice"
                }
            }
        ]
    }
}
```

## Workflow Components

### State Types
| Type | Description |
|------|-------------|
| task | Human task |
| service | Automated service |
| decision | Conditional branch |
| parallel | Parallel execution |
| timer | Time-based wait |

### Action Types
| Type | Description |
|------|-------------|
| api_call | API endpoint call |
| service | Internal service |
| notification | Send notification |
| webhook | External webhook |

### Transition Types
| Type | Description |
|------|-------------|
| automatic | Based on condition |
| manual | User triggered |
| timeout | Time based |
| error | Error handling |

## Task Management

### Task States
| State | Description |
|-------|-------------|
| pending | Not started |
| assigned | Assigned to user |
| in_progress | Being worked on |
| completed | Successfully done |
| failed | Failed to complete |

### Task Priorities
| Priority | Description |
|----------|-------------|
| critical | Immediate attention |
| high | Urgent |
| normal | Standard priority |
| low | Non-urgent |

## Error Handling

### Error Types
| Type | Description |
|------|-------------|
| validation | Invalid input |
| execution | Action failed |
| timeout | Time limit exceeded |
| permission | Access denied |

### Error Responses

#### Invalid Workflow (400)
```json
{
    "error": {
        "code": "INVALID_WORKFLOW",
        "message": "Invalid workflow definition",
        "details": [
            {
                "field": "states",
                "code": "INVALID_TRANSITION",
                "message": "Invalid state transition: soil_preparation -> harvesting"
            }
        ]
    }
}
```

#### Execution Error (500)
```json
{
    "error": {
        "code": "EXECUTION_ERROR",
        "message": "Workflow execution failed",
        "details": [
            {
                "state": "soil_preparation",
                "action": "soil_test",
                "code": "SERVICE_ERROR",
                "message": "Soil testing service unavailable"
            }
        ]
    }
}
```

#### State Transition Error (409)
```json
{
    "error": {
        "code": "INVALID_TRANSITION",
        "message": "Invalid state transition",
        "details": [
            {
                "from": "soil_preparation",
                "to": "harvesting",
                "code": "SEQUENCE_ERROR",
                "message": "Cannot transition to harvesting before planting"
            }
        ]
    }
}
```

## Rate Limiting
- Workflow creation: 50 requests per hour
- Instance management: 300 requests per hour
- Task operations: 600 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For workflow events, register webhook endpoints at:
```
POST /api/v1/workflows/webhooks
```

### Webhook Payload Format
```json
{
    "type": "workflow_event",
    "timestamp": "2025-01-19T00:53:33.123Z",
    "event": {
        "type": "state_changed",
        "workflow_instance_id": "wfi12345",
        "details": {
            "from_state": "soil_preparation",
            "to_state": "planting",
            "trigger": "condition_met"
        }
    }
}
```

## Best Practices

### Workflow Design
1. Keep workflows modular
2. Define clear state transitions
3. Include error handling
4. Set appropriate timeouts

### Task Management
1. Assign clear responsibilities
2. Set realistic deadlines
3. Include task instructions
4. Monitor task progress

### Error Handling
1. Define fallback paths
2. Implement retry logic
3. Notify stakeholders
4. Maintain audit trail

### Performance
1. Optimize state transitions
2. Monitor execution times
3. Handle parallel execution
4. Manage resource usage
