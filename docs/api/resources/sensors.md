# Sensors API

## Overview
The Sensors API provides endpoints for managing IoT sensors deployed in agricultural fields, including sensor registration, data collection, and alert configuration.

## Endpoints

### List Sensors
```http
GET /api/v1/sensors
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field ID | - |
| type | string | Filter by sensor type | - |
| status | string | Filter by status (active/inactive/maintenance) | active |
| sort | string | Sort field (-installed_date, type) | -installed_date |
| page | integer | Page number for pagination | 1 |
| per_page | integer | Items per page | 20 |

#### Response
```json
{
    "data": [
        {
            "id": "s12345",
            "field_id": "f12345",
            "type": "soil_moisture",
            "model": "SM200",
            "manufacturer": "AgriSense",
            "status": "active",
            "location": {
                "latitude": 35.6895,
                "longitude": 139.6917,
                "depth": "30cm"
            },
            "installed_date": "2025-01-01T00:00:00Z",
            "last_maintenance": "2025-01-15T00:00:00Z",
            "last_reading": {
                "timestamp": "2025-01-19T00:30:40Z",
                "value": 35.5,
                "unit": "%"
            },
            "battery_level": "85%",
            "signal_strength": "good"
        }
    ],
    "meta": {
        "page": 1,
        "per_page": 20,
        "total": 45
    }
}
```

### Register Sensor
```http
POST /api/v1/sensors
```

#### Request Body
```json
{
    "sensor": {
        "field_id": "f12345",
        "type": "soil_moisture",
        "model": "SM200",
        "manufacturer": "AgriSense",
        "serial_number": "SM200-2025-001",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917,
            "depth": "30cm"
        },
        "calibration": {
            "method": "factory",
            "last_calibrated": "2025-01-01T00:00:00Z",
            "next_calibration": "2025-07-01T00:00:00Z"
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "s12345",
        "field_id": "f12345",
        "type": "soil_moisture",
        "model": "SM200",
        "manufacturer": "AgriSense",
        "serial_number": "SM200-2025-001",
        "status": "active",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917,
            "depth": "30cm"
        },
        "calibration": {
            "method": "factory",
            "last_calibrated": "2025-01-01T00:00:00Z",
            "next_calibration": "2025-07-01T00:00:00Z"
        },
        "installed_date": "2025-01-19T00:30:40Z",
        "created_at": "2025-01-19T00:30:40Z"
    }
}
```

### Get Sensor Data
```http
GET /api/v1/sensors/{sensor_id}/data
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| sensor_id | string | Unique identifier of the sensor |

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| start_time | string | Start time (ISO 8601) | -24h |
| end_time | string | End time (ISO 8601) | now |
| interval | string | Data aggregation interval | 5m |
| statistics | array | Requested statistics (min,max,avg) | [avg] |

#### Response
```json
{
    "data": {
        "sensor_id": "s12345",
        "type": "soil_moisture",
        "unit": "%",
        "interval": "5m",
        "readings": [
            {
                "timestamp": "2025-01-19T00:00:00Z",
                "value": 35.5,
                "statistics": {
                    "min": 35.2,
                    "max": 35.8,
                    "avg": 35.5
                }
            }
        ],
        "summary": {
            "min": 35.2,
            "max": 35.8,
            "avg": 35.5
        }
    }
}
```

### Configure Alerts
```http
POST /api/v1/sensors/{sensor_id}/alerts
```

#### Path Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| sensor_id | string | Unique identifier of the sensor |

#### Request Body
```json
{
    "alert": {
        "type": "threshold",
        "condition": {
            "metric": "soil_moisture",
            "operator": "less_than",
            "value": 30,
            "unit": "%"
        },
        "notification": {
            "channels": ["email", "sms"],
            "recipients": [
                "farmer@example.com",
                "+81-90-1234-5678"
            ]
        },
        "cooldown": "1h"
    }
}
```

#### Response
```json
{
    "data": {
        "id": "a12345",
        "sensor_id": "s12345",
        "type": "threshold",
        "condition": {
            "metric": "soil_moisture",
            "operator": "less_than",
            "value": 30,
            "unit": "%"
        },
        "notification": {
            "channels": ["email", "sms"],
            "recipients": [
                "farmer@example.com",
                "+81-90-1234-5678"
            ]
        },
        "cooldown": "1h",
        "status": "active",
        "created_at": "2025-01-19T00:30:40Z"
    }
}
```

### Record Maintenance
```http
POST /api/v1/sensors/{sensor_id}/maintenance
```

#### Request Body
```json
{
    "maintenance": {
        "type": "calibration",
        "performed_by": "John Smith",
        "details": {
            "method": "field",
            "reference_readings": [
                {
                    "actual": 35.5,
                    "measured": 34.8
                }
            ]
        },
        "notes": "Annual calibration performed"
    }
}
```

#### Response
```json
{
    "data": {
        "id": "m12345",
        "sensor_id": "s12345",
        "type": "calibration",
        "performed_by": "John Smith",
        "performed_at": "2025-01-19T00:30:40Z",
        "details": {
            "method": "field",
            "reference_readings": [
                {
                    "actual": 35.5,
                    "measured": 34.8
                }
            ]
        },
        "notes": "Annual calibration performed",
        "next_maintenance_due": "2026-01-19T00:30:40Z"
    }
}
```

## Sensor Object

### Attributes
| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique identifier |
| field_id | string | Associated field ID |
| type | string | Sensor type |
| model | string | Sensor model |
| manufacturer | string | Manufacturer name |
| serial_number | string | Serial number |
| status | string | Current status |
| location | object | Installation location |
| calibration | object | Calibration information |
| installed_date | string | Installation date |
| last_maintenance | string | Last maintenance date |
| last_reading | object | Most recent reading |
| battery_level | string | Current battery level |
| signal_strength | string | Current signal strength |

### Supported Sensor Types
| Type | Measurements |
|------|--------------|
| soil_moisture | Soil moisture content (%) |
| soil_temperature | Soil temperature (°C) |
| air_temperature | Air temperature (°C) |
| humidity | Relative humidity (%) |
| light | Light intensity (lux) |
| rainfall | Precipitation (mm) |
| wind | Speed (m/s), Direction (degrees) |

### Status Values
- active: Normal operation
- inactive: Temporarily disabled
- maintenance: Under maintenance
- fault: Error condition

## Alert Configuration

### Alert Types
| Type | Description |
|------|-------------|
| threshold | Single value threshold |
| range | Value range violation |
| trend | Rapid value change |
| offline | Sensor communication loss |

### Operators
- equals
- not_equals
- greater_than
- less_than
- between
- not_between

### Notification Channels
- email
- sms
- push_notification
- webhook

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid sensor parameters",
        "details": [
            {
                "field": "location.depth",
                "code": "INVALID_FORMAT",
                "message": "Depth must be specified in cm or m"
            }
        ]
    }
}
```

### Sensor Not Found (404)
```json
{
    "error": {
        "code": "RESOURCE_NOT_FOUND",
        "message": "Sensor not found",
        "details": [
            {
                "field": "sensor_id",
                "code": "INVALID_ID",
                "message": "Sensor with ID 's12345' does not exist"
            }
        ]
    }
}
```

### Sensor Offline (503)
```json
{
    "error": {
        "code": "SENSOR_OFFLINE",
        "message": "Sensor is currently offline",
        "details": [
            {
                "field": "status",
                "code": "COMMUNICATION_ERROR",
                "message": "No response from sensor for 30 minutes"
            }
        ]
    }
}
```

## Rate Limiting
- Standard endpoints: 1000 requests per hour
- Data retrieval endpoints: 5000 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## WebSocket API
For real-time sensor data, connect to:
```
wss://api.agritech.com/v1/sensors/{sensor_id}/stream
```

### Stream Message Format
```json
{
    "type": "reading",
    "sensor_id": "s12345",
    "timestamp": "2025-01-19T00:30:40.123Z",
    "data": {
        "soil_moisture": 35.5,
        "soil_temperature": 18.2
    }
}
```
