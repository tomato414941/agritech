# Import API

## Overview
The Import API enables data import, validation, and mapping capabilities. It supports various data formats, schema validation, data transformation, and error handling for reliable data ingestion.

## Endpoints

### Data Import

#### Create Import Job
```http
POST /api/v1/import/jobs
```

##### Request Body
```json
{
    "import": {
        "type": "data_import",
        "resource": "fields",
        "source": {
            "type": "file",
            "format": "csv",
            "url": "https://example.com/fields.csv",
            "encoding": "UTF-8",
            "compression": "gzip"
        },
        "mapping": {
            "field_name": "name",
            "field_area_ha": "area",
            "field_crop": "crop_type",
            "planting_date": {
                "source": "plant_date",
                "transform": "date",
                "format": "YYYY-MM-DD"
            },
            "coordinates": {
                "source": ["latitude", "longitude"],
                "transform": "geolocation"
            }
        },
        "validation": {
            "rules": {
                "name": {
                    "type": "string",
                    "required": true,
                    "max_length": 100
                },
                "area": {
                    "type": "number",
                    "min": 0.1,
                    "max": 10000
                },
                "crop_type": {
                    "type": "string",
                    "enum": ["rice", "wheat", "corn"]
                }
            },
            "on_error": "skip_record"
        },
        "options": {
            "batch_size": 1000,
            "dry_run": false,
            "update_existing": true,
            "conflict_resolution": "update"
        }
    }
}
```

##### Response
```json
{
    "data": {
        "job_id": "import12345",
        "status": "validating",
        "created_at": "2025-01-19T00:51:08Z",
        "estimated_completion": "2025-01-19T00:56:08Z",
        "progress": {
            "phase": "validation",
            "percentage": 0,
            "current_record": 0,
            "total_records": 1000
        }
    }
}
```

#### Get Import Job Status
```http
GET /api/v1/import/jobs/{job_id}
```

##### Response
```json
{
    "data": {
        "job_id": "import12345",
        "status": "completed",
        "created_at": "2025-01-19T00:51:08Z",
        "completed_at": "2025-01-19T00:56:08Z",
        "statistics": {
            "total_records": 1000,
            "processed": 1000,
            "succeeded": 980,
            "failed": 20,
            "updated": 150,
            "created": 830
        },
        "errors": [
            {
                "record": 45,
                "field": "area",
                "error": "Value exceeds maximum limit",
                "value": "12000"
            }
        ],
        "warnings": [
            {
                "type": "duplicate_record",
                "count": 5,
                "resolution": "updated"
            }
        ]
    }
}
```

### Data Validation

#### Validate Import File
```http
POST /api/v1/import/validate
```

##### Request Body
```json
{
    "validation": {
        "source": {
            "type": "file",
            "format": "csv",
            "url": "https://example.com/fields.csv"
        },
        "schema": {
            "type": "field_import",
            "version": "1.0"
        },
        "options": {
            "max_errors": 100,
            "validate_all": true
        }
    }
}
```

##### Response
```json
{
    "data": {
        "valid": false,
        "errors": [
            {
                "row": 45,
                "column": "area",
                "value": "12000",
                "rule": "maximum",
                "constraint": 10000,
                "message": "Value exceeds maximum limit"
            }
        ],
        "warnings": [
            {
                "type": "missing_optional",
                "field": "soil_type",
                "count": 10
            }
        ],
        "statistics": {
            "total_rows": 1000,
            "valid_rows": 980,
            "invalid_rows": 20
        }
    }
}
```

### Data Mapping

#### Get Available Mappings
```http
GET /api/v1/import/mappings
```

##### Response
```json
{
    "data": {
        "resources": [
            {
                "type": "fields",
                "version": "1.0",
                "fields": [
                    {
                        "name": "field_name",
                        "type": "string",
                        "required": true,
                        "mappable_to": ["name", "title", "label"]
                    },
                    {
                        "name": "area",
                        "type": "number",
                        "unit": "hectares",
                        "mappable_to": ["area_ha", "field_size", "hectares"]
                    }
                ],
                "transformations": [
                    {
                        "name": "date",
                        "description": "Date format conversion",
                        "supported_formats": ["YYYY-MM-DD", "MM/DD/YYYY"]
                    },
                    {
                        "name": "geolocation",
                        "description": "Coordinate pair to geolocation",
                        "input_format": ["lat,lon", "lon,lat"]
                    }
                ]
            }
        ]
    }
}
```

## Import Formats

### Supported Formats
| Format | MIME Type | Description |
|--------|-----------|-------------|
| CSV | text/csv | Comma-separated values |
| JSON | application/json | JSON format |
| Excel | application/vnd.openxmlformats-officedocument.spreadsheetml.sheet | Excel workbook |
| XML | application/xml | XML format |

### Format Options
| Option | Description |
|--------|-------------|
| encoding | Character encoding |
| delimiter | Field separator |
| quote_char | Quote character |
| escape_char | Escape character |
| compression | Compression type |

## Data Validation

### Validation Rules
| Rule | Description |
|------|-------------|
| required | Field must be present |
| type | Data type check |
| format | Format validation |
| range | Numeric range check |
| enum | Value from set |
| regex | Pattern matching |

### Error Handling
| Strategy | Description |
|----------|-------------|
| fail_fast | Stop on first error |
| skip_record | Skip invalid records |
| use_default | Use default values |
| transform | Apply transformation |

## Data Transformation

### Transform Types
| Type | Description |
|------|-------------|
| date | Date format conversion |
| number | Numeric conversion |
| string | String manipulation |
| geolocation | Coordinate conversion |

### Transformation Options
| Option | Description |
|--------|-------------|
| format | Output format |
| timezone | Time zone handling |
| rounding | Numeric rounding |
| truncation | String truncation |

## Error Responses

### Validation Error (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Import validation failed",
        "details": [
            {
                "row": 45,
                "field": "area",
                "code": "INVALID_VALUE",
                "message": "Value exceeds maximum limit"
            }
        ]
    }
}
```

### Format Error (400)
```json
{
    "error": {
        "code": "FORMAT_ERROR",
        "message": "Invalid file format",
        "details": [
            {
                "code": "PARSE_ERROR",
                "message": "Unable to parse CSV file: invalid delimiter"
            }
        ]
    }
}
```

### Import Failed (500)
```json
{
    "error": {
        "code": "IMPORT_FAILED",
        "message": "Import job failed",
        "details": [
            {
                "code": "DATABASE_ERROR",
                "message": "Unable to write to database"
            }
        ]
    }
}
```

## Rate Limiting
- File validation: 60 requests per hour
- Data import: 10 requests per hour
- Mapping retrieval: 100 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For import job notifications, register webhook endpoints at:
```
POST /api/v1/import/webhooks
```

### Webhook Payload Format
```json
{
    "type": "import_event",
    "timestamp": "2025-01-19T00:51:08.123Z",
    "event": {
        "type": "import_completed",
        "job_id": "import12345",
        "details": {
            "total_records": 1000,
            "succeeded": 980,
            "failed": 20
        }
    }
}
```

## Best Practices

### Data Preparation
1. Clean data before import
2. Validate file format and encoding
3. Use appropriate data types
4. Handle missing values appropriately

### Performance Optimization
1. Use batch processing
2. Compress large files
3. Schedule imports during off-peak hours
4. Monitor resource usage

### Error Handling
1. Implement retry logic
2. Log validation errors
3. Provide clear error messages
4. Maintain audit trail

### Security Considerations
1. Validate file contents
2. Scan for malware
3. Enforce file size limits
4. Implement access controls
