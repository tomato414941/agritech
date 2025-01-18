# Crops API

## Overview
The Crops API provides endpoints for managing crop data, including planting records, growth stages, and harvesting information.

## Endpoints

### List Crops
```http
GET /api/v1/crops
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field ID | - |
| status | string | Filter by status (planted/harvested) | - |
| type | string | Filter by crop type | - |
| planted_after | string | Filter by planting date (ISO 8601) | - |
| planted_before | string | Filter by planting date (ISO 8601) | - |
| sort | string | Sort field (-planted_date, type) | -planted_date |
| page | integer | Page number for pagination | 1 |
| per_page | integer | Items per page | 20 |

#### Response
```json
{
    "data": [
        {
            "id": "c12345",
            "field_id": "f12345",
            "type": "wheat",
            "variety": "winter wheat",
            "status": "planted",
            "planted_date": "2025-01-01T00:00:00Z",
            "expected_harvest_date": "2025-07-01T00:00:00Z",
            "growth_stage": "vegetative",
            "health_status": "healthy",
            "created_at": "2025-01-01T00:00:00Z",
            "updated_at": "2025-01-19T00:28:07Z"
        }
    ],
    "meta": {
        "page": 1,
        "per_page": 20,
        "total": 45
    },
    "links": {
        "self": "/api/v1/crops?page=1",
        "next": "/api/v1/crops?page=2",
        "prev": null
    }
}
```

### Create Crop Record
```http
POST /api/v1/crops
```

#### Request Body
```json
{
    "crop": {
        "field_id": "f12345",
        "type": "wheat",
        "variety": "winter wheat",
        "planted_date": "2025-01-01T00:00:00Z",
        "expected_harvest_date": "2025-07-01T00:00:00Z",
        "planting_density": {
            "value": 150,
            "unit": "kg/ha"
        },
        "seed_details": {
            "supplier": "AgriSeed Co.",
            "batch_number": "WW2025-001"
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "c12345",
        "field_id": "f12345",
        "type": "wheat",
        "variety": "winter wheat",
        "status": "planted",
        "planted_date": "2025-01-01T00:00:00Z",
        "expected_harvest_date": "2025-07-01T00:00:00Z",
        "planting_density": {
            "value": 150,
            "unit": "kg/ha"
        },
        "seed_details": {
            "supplier": "AgriSeed Co.",
            "batch_number": "WW2025-001"
        },
        "created_at": "2025-01-19T00:28:07Z",
        "updated_at": "2025-01-19T00:28:07Z"
    }
}
```

### Get Crop Details
```http
GET /api/v1/crops/{crop_id}
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| crop_id | string | Unique identifier of the crop record |

#### Response
```json
{
    "data": {
        "id": "c12345",
        "field_id": "f12345",
        "type": "wheat",
        "variety": "winter wheat",
        "status": "planted",
        "planted_date": "2025-01-01T00:00:00Z",
        "expected_harvest_date": "2025-07-01T00:00:00Z",
        "growth_stage": "vegetative",
        "health_status": "healthy",
        "planting_density": {
            "value": 150,
            "unit": "kg/ha"
        },
        "seed_details": {
            "supplier": "AgriSeed Co.",
            "batch_number": "WW2025-001"
        },
        "growth_data": {
            "current_height": {
                "value": 45,
                "unit": "cm"
            },
            "leaf_stage": "tillering",
            "last_measured": "2025-01-15T00:00:00Z"
        },
        "treatments": [
            {
                "type": "fertilizer",
                "product": "NPK 20-20-20",
                "application_date": "2025-01-10T00:00:00Z",
                "rate": {
                    "value": 200,
                    "unit": "kg/ha"
                }
            }
        ],
        "created_at": "2025-01-01T00:00:00Z",
        "updated_at": "2025-01-19T00:28:07Z"
    }
}
```

### Update Crop Record
```http
PUT /api/v1/crops/{crop_id}
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| crop_id | string | Unique identifier of the crop record |

#### Request Body
```json
{
    "crop": {
        "growth_stage": "reproductive",
        "health_status": "good",
        "growth_data": {
            "current_height": {
                "value": 50,
                "unit": "cm"
            },
            "leaf_stage": "heading"
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "c12345",
        "field_id": "f12345",
        "type": "wheat",
        "variety": "winter wheat",
        "status": "planted",
        "growth_stage": "reproductive",
        "health_status": "good",
        "growth_data": {
            "current_height": {
                "value": 50,
                "unit": "cm"
            },
            "leaf_stage": "heading",
            "last_measured": "2025-01-19T00:28:07Z"
        },
        "updated_at": "2025-01-19T00:28:07Z"
    }
}
```

### Record Harvest
```http
POST /api/v1/crops/{crop_id}/harvest
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| crop_id | string | Unique identifier of the crop record |

#### Request Body
```json
{
    "harvest": {
        "date": "2025-07-01T00:00:00Z",
        "yield": {
            "value": 5.5,
            "unit": "tons/ha"
        },
        "quality_metrics": {
            "moisture_content": "13%",
            "protein_content": "12.5%",
            "test_weight": "78 kg/hl"
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "c12345",
        "status": "harvested",
        "harvest_data": {
            "date": "2025-07-01T00:00:00Z",
            "yield": {
                "value": 5.5,
                "unit": "tons/ha"
            },
            "quality_metrics": {
                "moisture_content": "13%",
                "protein_content": "12.5%",
                "test_weight": "78 kg/hl"
            }
        },
        "updated_at": "2025-01-19T00:28:07Z"
    }
}
```

## Crop Object

### Attributes
| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique identifier |
| field_id | string | Associated field ID |
| type | string | Crop type |
| variety | string | Specific variety |
| status | string | Current status |
| planted_date | string | Planting date (ISO 8601) |
| expected_harvest_date | string | Expected harvest date |
| growth_stage | string | Current growth stage |
| health_status | string | Current health status |
| planting_density | object | Seeding rate information |
| seed_details | object | Seed source information |
| growth_data | object | Current growth measurements |
| treatments | array | List of applied treatments |
| harvest_data | object | Harvest information |
| created_at | string | Creation timestamp |
| updated_at | string | Last update timestamp |

### Validation Rules
1. field_id must reference an existing field
2. planted_date must not be in the future
3. expected_harvest_date must be after planted_date
4. type must be from approved crop types list
5. planting_density must have valid unit conversion

### Status Transitions
```
planted → growing → harvested
      ↘ failed
```

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid crop parameters",
        "details": [
            {
                "field": "planted_date",
                "code": "FUTURE_DATE",
                "message": "Planting date cannot be in the future"
            }
        ]
    }
}
```

### Crop Not Found (404)
```json
{
    "error": {
        "code": "RESOURCE_NOT_FOUND",
        "message": "Crop record not found",
        "details": [
            {
                "field": "crop_id",
                "code": "INVALID_ID",
                "message": "Crop with ID 'c12345' does not exist"
            }
        ]
    }
}
```

### Invalid State Transition (409)
```json
{
    "error": {
        "code": "INVALID_STATE_TRANSITION",
        "message": "Cannot perform requested operation",
        "details": [
            {
                "field": "status",
                "code": "INVALID_TRANSITION",
                "message": "Cannot harvest a failed crop"
            }
        ]
    }
}
```

## Rate Limiting
- 1000 requests per hour per API key
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset
