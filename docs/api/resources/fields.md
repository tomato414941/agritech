# Fields API

## Overview
The Fields API provides endpoints for managing agricultural fields in the AgriTech platform.

## Endpoints

### List Fields
```http
GET /api/v1/fields
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| status | string | Filter by field status (active/inactive) | active |
| crop_type | string | Filter by current crop type | - |
| min_area | number | Minimum field area in hectares | - |
| max_area | number | Maximum field area in hectares | - |
| sort | string | Sort field (-created_at, name, area) | -created_at |
| page | integer | Page number for pagination | 1 |
| per_page | integer | Items per page | 20 |

#### Response
```json
{
    "data": [
        {
            "id": "f12345",
            "name": "North Field",
            "area": 100.5,
            "unit": "hectares",
            "status": "active",
            "location": {
                "latitude": 35.6895,
                "longitude": 139.6917
            },
            "current_crop": {
                "type": "wheat",
                "planted_date": "2025-01-01"
            },
            "created_at": "2025-01-01T00:00:00Z",
            "updated_at": "2025-01-19T00:25:21Z"
        }
    ],
    "meta": {
        "page": 1,
        "per_page": 20,
        "total": 45
    },
    "links": {
        "self": "/api/v1/fields?page=1",
        "next": "/api/v1/fields?page=2",
        "prev": null
    }
}
```

### Create Field
```http
POST /api/v1/fields
```

#### Request Body
```json
{
    "field": {
        "name": "East Field",
        "area": 75.3,
        "unit": "hectares",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "f12346",
        "name": "East Field",
        "area": 75.3,
        "unit": "hectares",
        "status": "active",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        },
        "created_at": "2025-01-19T00:25:21Z",
        "updated_at": "2025-01-19T00:25:21Z"
    }
}
```

### Get Field Details
```http
GET /api/v1/fields/{field_id}
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| field_id | string | Unique identifier of the field |

#### Response
```json
{
    "data": {
        "id": "f12345",
        "name": "North Field",
        "area": 100.5,
        "unit": "hectares",
        "status": "active",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        },
        "current_crop": {
            "type": "wheat",
            "planted_date": "2025-01-01"
        },
        "soil_data": {
            "ph": 6.5,
            "organic_matter": "2.5%",
            "last_tested": "2025-01-15T00:00:00Z"
        },
        "weather_station": {
            "id": "ws789",
            "distance": "0.5km"
        },
        "created_at": "2025-01-01T00:00:00Z",
        "updated_at": "2025-01-19T00:25:21Z"
    }
}
```

### Update Field
```http
PUT /api/v1/fields/{field_id}
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| field_id | string | Unique identifier of the field |

#### Request Body
```json
{
    "field": {
        "name": "North Field Updated",
        "area": 110.5,
        "status": "inactive"
    }
}
```

#### Response
```json
{
    "data": {
        "id": "f12345",
        "name": "North Field Updated",
        "area": 110.5,
        "unit": "hectares",
        "status": "inactive",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        },
        "updated_at": "2025-01-19T00:25:21Z"
    }
}
```

### Delete Field
```http
DELETE /api/v1/fields/{field_id}
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| field_id | string | Unique identifier of the field |

#### Response
```
204 No Content
```

## Field Object

### Attributes
| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique identifier |
| name | string | Field name (1-100 chars) |
| area | number | Field size in hectares |
| unit | string | Unit of measurement (hectares/acres) |
| status | string | Field status (active/inactive) |
| location | object | Geographical coordinates |
| current_crop | object | Currently planted crop details |
| soil_data | object | Latest soil test results |
| weather_station | object | Associated weather station |
| created_at | string | Creation timestamp (ISO 8601) |
| updated_at | string | Last update timestamp (ISO 8601) |

### Validation Rules
1. Name must be unique within a farm
2. Area must be positive and non-zero
3. Location coordinates must be valid
4. Status must be either "active" or "inactive"

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid field parameters",
        "details": [
            {
                "field": "area",
                "code": "INVALID_RANGE",
                "message": "Field area must be greater than 0"
            }
        ]
    }
}
```

### Field Not Found (404)
```json
{
    "error": {
        "code": "RESOURCE_NOT_FOUND",
        "message": "Field not found",
        "details": [
            {
                "field": "field_id",
                "code": "INVALID_ID",
                "message": "Field with ID 'f12345' does not exist"
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
