# Integrations API

## Overview
The Integrations API enables seamless connectivity with external systems, data import/export capabilities, and integration management. It supports various integration patterns, authentication methods, and data transformation options.

## Endpoints

### List Integrations
```http
GET /api/v1/integrations
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| status | string | Filter by status | active |
| type | string | Filter by type | all |
| page | integer | Page number | 1 |
| per_page | integer | Items per page | 20 |

#### Response
```json
{
    "data": [
        {
            "id": "i12345",
            "name": "Weather Service Integration",
            "type": "weather_service",
            "provider": "weatherapi",
            "status": "active",
            "config": {
                "endpoint": "https://api.weatherservice.com/v1",
                "update_frequency": "15m",
                "data_retention": "90d"
            },
            "auth": {
                "type": "api_key",
                "status": "valid",
                "expires_at": "2026-01-19T00:41:10Z"
            },
            "metrics": {
                "requests_today": 850,
                "errors_today": 2,
                "last_sync": "2025-01-19T00:41:10Z"
            },
            "created_at": "2025-01-01T00:00:00Z",
            "updated_at": "2025-01-19T00:41:10Z"
        }
    ],
    "meta": {
        "page": 1,
        "per_page": 20,
        "total": 5
    }
}
```

### Create Integration
```http
POST /api/v1/integrations
```

#### Request Body
```json
{
    "integration": {
        "name": "Equipment Telemetry Integration",
        "type": "equipment_telemetry",
        "provider": "tractorlink",
        "config": {
            "endpoint": "https://api.tractorlink.com/v2",
            "update_frequency": "5m",
            "data_mapping": {
                "equipment_id": "asset_id",
                "location": {
                    "source": "gps_position",
                    "format": "lat_long"
                },
                "metrics": [
                    {
                        "source": "fuel_level",
                        "target": "fuel_percentage",
                        "transform": "multiply:100"
                    }
                ]
            }
        },
        "auth": {
            "type": "oauth2",
            "credentials": {
                "client_id": "client_12345",
                "client_secret": "secret_12345",
                "scope": "read_telemetry"
            }
        },
        "webhooks": {
            "status_updates": "https://api.agritech.com/webhooks/equipment/status",
            "alerts": "https://api.agritech.com/webhooks/equipment/alerts"
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "i12346",
        "status": "configuring",
        "auth_url": "https://tractorlink.com/oauth/authorize?client_id=client_12345",
        "next_steps": [
            "Complete OAuth authorization",
            "Verify webhook endpoints",
            "Test data mapping"
        ],
        "created_at": "2025-01-19T00:41:10Z"
    }
}
```

### Configure Data Mapping
```http
PUT /api/v1/integrations/{integration_id}/mapping
```

#### Request Body
```json
{
    "mapping": {
        "fields": [
            {
                "source": {
                    "field": "soil_moisture",
                    "format": "percentage"
                },
                "target": {
                    "field": "moisture_level",
                    "format": "decimal"
                },
                "transform": [
                    {
                        "type": "divide",
                        "value": 100
                    },
                    {
                        "type": "round",
                        "decimals": 2
                    }
                ]
            }
        ],
        "validation": {
            "required_fields": ["soil_moisture", "temperature"],
            "range_checks": {
                "soil_moisture": {
                    "min": 0,
                    "max": 100
                }
            }
        }
    }
}
```

#### Response
```json
{
    "data": {
        "mapping_id": "m12345",
        "status": "active",
        "sample_transformation": {
            "input": {
                "soil_moisture": "45%"
            },
            "output": {
                "moisture_level": 0.45
            }
        },
        "updated_at": "2025-01-19T00:41:10Z"
    }
}
```

### Import Data
```http
POST /api/v1/integrations/{integration_id}/import
```

#### Request Body
```json
{
    "import": {
        "source": {
            "type": "file",
            "format": "csv",
            "url": "https://storage.agritech.com/imports/data.csv"
        },
        "options": {
            "skip_header": true,
            "delimiter": ",",
            "date_format": "YYYY-MM-DD",
            "null_values": ["", "NA", "null"]
        },
        "mapping": {
            "columns": {
                "A": "date",
                "B": "field_id",
                "C": "measurement_type",
                "D": "value"
            }
        },
        "validation": {
            "schema": {
                "date": {
                    "type": "date",
                    "required": true
                },
                "value": {
                    "type": "number",
                    "min": 0
                }
            }
        }
    }
}
```

#### Response
```json
{
    "data": {
        "import_id": "imp12345",
        "status": "processing",
        "progress": {
            "total_records": 1000,
            "processed": 0,
            "failed": 0
        },
        "estimated_completion": "2025-01-19T00:42:10Z"
    }
}
```

### Export Data
```http
POST /api/v1/integrations/{integration_id}/export
```

#### Request Body
```json
{
    "export": {
        "data_type": "field_measurements",
        "filters": {
            "date_range": {
                "start": "2025-01-01",
                "end": "2025-01-19"
            },
            "fields": ["f12345"],
            "measurements": ["soil_moisture", "temperature"]
        },
        "format": {
            "type": "csv",
            "options": {
                "delimiter": ",",
                "include_header": true,
                "date_format": "YYYY-MM-DD HH:mm:ss"
            }
        },
        "delivery": {
            "method": "s3",
            "config": {
                "bucket": "exports",
                "prefix": "field_data/"
            }
        }
    }
}
```

#### Response
```json
{
    "data": {
        "export_id": "exp12345",
        "status": "processing",
        "progress": {
            "percentage": 0,
            "records_processed": 0
        },
        "estimated_completion": "2025-01-19T00:42:10Z"
    }
}
```

## Integration Types

### Available Integrations
| Type | Description | Providers |
|------|-------------|-----------|
| weather_service | Weather data providers | weatherapi, openweather |
| equipment_telemetry | Equipment monitoring | tractorlink, agequip |
| soil_sensors | Soil monitoring systems | soilsense, terramonitor |
| satellite_imagery | Satellite image providers | sentinel, planet |
| farm_management | Farm management systems | agrinet, farmsoft |

### Authentication Methods
| Method | Description |
|--------|-------------|
| api_key | Simple API key |
| oauth2 | OAuth 2.0 flow |
| jwt | JWT tokens |
| basic | Basic authentication |

### Data Formats
| Format | Content Types |
|--------|---------------|
| json | application/json |
| csv | text/csv |
| xml | application/xml |
| binary | application/octet-stream |

## Data Transformation

### Transform Operations
| Operation | Description | Example |
|-----------|-------------|---------|
| convert_unit | Unit conversion | km to miles |
| format_date | Date formatting | ISO to local |
| map_values | Value mapping | code to name |
| aggregate | Data aggregation | sum, average |

### Validation Rules
| Rule | Description |
|------|-------------|
| required | Required fields |
| range | Value range check |
| format | Format validation |
| unique | Uniqueness check |

## Error Responses

### Authentication Error (401)
```json
{
    "error": {
        "code": "AUTH_ERROR",
        "message": "Authentication failed",
        "details": [
            {
                "field": "api_key",
                "code": "INVALID_KEY",
                "message": "API key has expired"
            }
        ]
    }
}
```

### Integration Error (400)
```json
{
    "error": {
        "code": "INTEGRATION_ERROR",
        "message": "Integration configuration failed",
        "details": [
            {
                "field": "endpoint",
                "code": "INVALID_URL",
                "message": "Invalid endpoint URL format"
            }
        ]
    }
}
```

### Data Processing Error (422)
```json
{
    "error": {
        "code": "PROCESSING_ERROR",
        "message": "Data processing failed",
        "details": [
            {
                "row": 15,
                "field": "temperature",
                "code": "INVALID_VALUE",
                "message": "Temperature value out of range"
            }
        ]
    }
}
```

## Rate Limiting
- Configuration operations: 100 requests per hour
- Data import: 10 requests per hour
- Data export: 5 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For integration status updates and data sync notifications, register webhook endpoints at:
```
POST /api/v1/integrations/{integration_id}/webhooks
```

### Webhook Payload Format
```json
{
    "type": "integration_status",
    "integration_id": "i12345",
    "timestamp": "2025-01-19T00:41:10.123Z",
    "event": {
        "type": "sync_completed",
        "details": {
            "records_processed": 1000,
            "errors": 0,
            "duration": "65s"
        }
    }
}
```

## OAuth2 Configuration
For OAuth2 integrations, configure the client at:
```
POST /api/v1/integrations/oauth2/clients
```

### Client Configuration Format
```json
{
    "client": {
        "name": "TractorLink Integration",
        "redirect_uris": [
            "https://api.agritech.com/oauth/callback"
        ],
        "scopes": ["read_telemetry", "read_position"]
    }
}
```
