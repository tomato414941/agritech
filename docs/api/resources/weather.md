# Weather Data API

## Overview
The Weather Data API provides access to current weather conditions, forecasts, and historical weather data for agricultural fields. It integrates data from on-site weather stations, third-party weather services, and satellite imagery.

## Endpoints

### Get Current Weather
```http
GET /api/v1/weather/current
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Get weather for specific field | - |
| latitude | number | Location latitude | - |
| longitude | number | Location longitude | - |
| units | string | Unit system (metric/imperial) | metric |

#### Response
```json
{
    "data": {
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917,
            "elevation": 40.5
        },
        "timestamp": "2025-01-19T00:33:27Z",
        "conditions": {
            "temperature": {
                "value": 12.5,
                "unit": "°C"
            },
            "humidity": {
                "value": 65,
                "unit": "%"
            },
            "wind": {
                "speed": {
                    "value": 5.2,
                    "unit": "m/s"
                },
                "direction": {
                    "degrees": 180,
                    "cardinal": "S"
                },
                "gusts": {
                    "value": 8.1,
                    "unit": "m/s"
                }
            },
            "precipitation": {
                "last_hour": {
                    "value": 0.5,
                    "unit": "mm"
                },
                "probability": 30
            },
            "pressure": {
                "value": 1013.2,
                "unit": "hPa"
            },
            "solar_radiation": {
                "value": 250.5,
                "unit": "W/m²"
            },
            "soil_conditions": {
                "temperature": {
                    "value": 10.2,
                    "unit": "°C"
                },
                "moisture": {
                    "value": 35.5,
                    "unit": "%"
                }
            }
        },
        "data_sources": [
            "on_site_station",
            "satellite"
        ]
    }
}
```

### Get Weather Forecast
```http
GET /api/v1/weather/forecast
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Get forecast for specific field | - |
| latitude | number | Location latitude | - |
| longitude | number | Location longitude | - |
| days | integer | Number of forecast days | 7 |
| interval | string | Forecast interval (hourly/daily) | daily |
| units | string | Unit system (metric/imperial) | metric |

#### Response
```json
{
    "data": {
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        },
        "generated_at": "2025-01-19T00:33:27Z",
        "forecast": [
            {
                "timestamp": "2025-01-20T00:00:00Z",
                "conditions": {
                    "temperature": {
                        "min": {
                            "value": 8.5,
                            "unit": "°C"
                        },
                        "max": {
                            "value": 15.2,
                            "unit": "°C"
                        }
                    },
                    "precipitation": {
                        "probability": 60,
                        "amount": {
                            "value": 5.2,
                            "unit": "mm"
                        }
                    },
                    "wind": {
                        "speed": {
                            "value": 4.5,
                            "unit": "m/s"
                        },
                        "direction": "SW"
                    }
                },
                "agricultural_metrics": {
                    "gdd": {
                        "value": 8.5,
                        "unit": "°C·day"
                    },
                    "eto": {
                        "value": 3.2,
                        "unit": "mm"
                    }
                }
            }
        ],
        "alerts": [
            {
                "type": "frost_warning",
                "severity": "moderate",
                "start_time": "2025-01-21T03:00:00Z",
                "end_time": "2025-01-21T09:00:00Z",
                "details": "Risk of frost damage to crops"
            }
        ]
    }
}
```

### Get Historical Weather
```http
GET /api/v1/weather/historical
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Get history for specific field | - |
| start_date | string | Start date (ISO 8601) | -7d |
| end_date | string | End date (ISO 8601) | now |
| interval | string | Data interval (hourly/daily) | daily |
| metrics | array | Requested weather metrics | all |
| units | string | Unit system (metric/imperial) | metric |

#### Response
```json
{
    "data": {
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        },
        "interval": "daily",
        "history": [
            {
                "date": "2025-01-18",
                "temperature": {
                    "min": {
                        "value": 7.2,
                        "unit": "°C"
                    },
                    "max": {
                        "value": 14.5,
                        "unit": "°C"
                    },
                    "avg": {
                        "value": 10.8,
                        "unit": "°C"
                    }
                },
                "precipitation": {
                    "amount": {
                        "value": 2.5,
                        "unit": "mm"
                    }
                },
                "gdd": {
                    "value": 7.8,
                    "unit": "°C·day"
                }
            }
        ],
        "summary": {
            "temperature": {
                "min": 7.2,
                "max": 14.5,
                "avg": 10.8
            },
            "total_precipitation": 2.5,
            "total_gdd": 7.8
        }
    }
}
```

### Get Weather Alerts
```http
GET /api/v1/weather/alerts
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Get alerts for specific field | - |
| types | array | Alert types to include | all |
| severity | string | Minimum severity level | all |

#### Response
```json
{
    "data": {
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        },
        "alerts": [
            {
                "id": "w12345",
                "type": "frost_warning",
                "severity": "moderate",
                "start_time": "2025-01-21T03:00:00Z",
                "end_time": "2025-01-21T09:00:00Z",
                "conditions": {
                    "temperature": {
                        "min": {
                            "value": -2.0,
                            "unit": "°C"
                        }
                    }
                },
                "affected_crops": [
                    "wheat",
                    "barley"
                ],
                "recommendations": [
                    "Consider frost protection measures",
                    "Monitor crop conditions closely"
                ]
            }
        ]
    }
}
```

### Configure Weather Alerts
```http
POST /api/v1/weather/alerts/config
```

#### Request Body
```json
{
    "config": {
        "field_id": "f12345",
        "alert_types": [
            {
                "type": "frost",
                "threshold": {
                    "temperature": 2.0,
                    "unit": "°C"
                },
                "lead_time": "12h"
            },
            {
                "type": "heavy_rain",
                "threshold": {
                    "amount": 50.0,
                    "unit": "mm",
                    "period": "24h"
                }
            }
        ],
        "notification": {
            "channels": ["email", "sms"],
            "recipients": [
                "farmer@example.com",
                "+81-90-1234-5678"
            ]
        }
    }
}
```

#### Response
```json
{
    "data": {
        "id": "wc12345",
        "field_id": "f12345",
        "alert_types": [
            {
                "type": "frost",
                "threshold": {
                    "temperature": 2.0,
                    "unit": "°C"
                },
                "lead_time": "12h"
            },
            {
                "type": "heavy_rain",
                "threshold": {
                    "amount": 50.0,
                    "unit": "mm",
                    "period": "24h"
                }
            }
        ],
        "notification": {
            "channels": ["email", "sms"],
            "recipients": [
                "farmer@example.com",
                "+81-90-1234-5678"
            ]
        },
        "created_at": "2025-01-19T00:33:27Z"
    }
}
```

## Weather Metrics

### Available Metrics
| Metric | Unit (Metric) | Unit (Imperial) |
|--------|---------------|-----------------|
| temperature | °C | °F |
| humidity | % | % |
| wind_speed | m/s | mph |
| wind_direction | degrees | degrees |
| precipitation | mm | inches |
| pressure | hPa | inHg |
| solar_radiation | W/m² | W/m² |
| soil_temperature | °C | °F |
| soil_moisture | % | % |

### Agricultural Indices
| Index | Description | Unit |
|-------|-------------|------|
| GDD | Growing Degree Days | °C·day / °F·day |
| ETo | Reference Evapotranspiration | mm/day / inches/day |
| NDVI | Normalized Difference Vegetation Index | - |
| SPI | Standardized Precipitation Index | - |

### Alert Types
| Type | Description |
|------|-------------|
| frost | Frost risk warning |
| heavy_rain | Heavy precipitation warning |
| drought | Drought conditions alert |
| heat_stress | High temperature stress warning |
| disease_risk | Disease favorable conditions |
| pest_risk | Pest favorable conditions |

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid request parameters",
        "details": [
            {
                "field": "days",
                "code": "INVALID_RANGE",
                "message": "Forecast days must be between 1 and 14"
            }
        ]
    }
}
```

### Location Not Found (404)
```json
{
    "error": {
        "code": "LOCATION_ERROR",
        "message": "Weather data not available for location",
        "details": [
            {
                "field": "coordinates",
                "code": "OUTSIDE_COVERAGE",
                "message": "Location is outside coverage area"
            }
        ]
    }
}
```

### Service Unavailable (503)
```json
{
    "error": {
        "code": "SERVICE_UNAVAILABLE",
        "message": "Weather service temporarily unavailable",
        "details": [
            {
                "code": "PROVIDER_ERROR",
                "message": "Third-party weather service is not responding"
            }
        ]
    }
}
```

## Rate Limiting
- Current weather: 1000 requests per hour
- Forecast data: 500 requests per hour
- Historical data: 200 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## WebSocket API
For real-time weather updates, connect to:
```
wss://api.agritech.com/v1/weather/stream?field_id=f12345
```

### Stream Message Format
```json
{
    "type": "weather_update",
    "field_id": "f12345",
    "timestamp": "2025-01-19T00:33:27.123Z",
    "data": {
        "temperature": 12.5,
        "humidity": 65,
        "wind_speed": 5.2
    }
}
```
