# Analytics API

## Overview
The Analytics API provides insights, reports, and predictions based on agricultural data collected across the platform. It enables data-driven decision making by analyzing patterns in crop performance, weather impacts, and operational efficiency.

## Endpoints

### Get Crop Performance Analytics
```http
GET /api/v1/analytics/crops
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field | - |
| crop_type | string | Filter by crop type | - |
| start_date | string | Analysis start date | -1y |
| end_date | string | Analysis end date | now |
| metrics | array | Requested metrics | all |
| interval | string | Analysis interval | month |

#### Response
```json
{
    "data": {
        "period": {
            "start": "2024-01-19",
            "end": "2025-01-19"
        },
        "crops": [
            {
                "type": "wheat",
                "variety": "winter wheat",
                "total_area": {
                    "value": 150,
                    "unit": "ha"
                },
                "yield": {
                    "average": {
                        "value": 5.2,
                        "unit": "tons/ha"
                    },
                    "trend": "+3.5%",
                    "comparison": {
                        "vs_previous": "+0.3 tons/ha",
                        "vs_regional": "+0.5 tons/ha"
                    }
                },
                "growth_stages": [
                    {
                        "stage": "emergence",
                        "average_duration": "12 days",
                        "gdd_accumulated": {
                            "value": 150,
                            "unit": "°C·day"
                        }
                    }
                ],
                "inputs": {
                    "fertilizer": {
                        "total": {
                            "value": 450,
                            "unit": "kg/ha"
                        },
                        "cost_per_ha": {
                            "value": 125,
                            "unit": "USD"
                        }
                    }
                },
                "health_indices": {
                    "ndvi": {
                        "average": 0.75,
                        "trend": "stable"
                    }
                }
            }
        ]
    }
}
```

### Get Field Analytics
```http
GET /api/v1/analytics/fields
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field | - |
| metrics | array | Requested metrics | all |
| period | string | Analysis period | year |

#### Response
```json
{
    "data": {
        "field_id": "f12345",
        "size": {
            "value": 25,
            "unit": "ha"
        },
        "soil_health": {
            "organic_matter": {
                "current": {
                    "value": 3.2,
                    "unit": "%"
                },
                "trend": "+0.2%"
            },
            "ph": {
                "current": 6.8,
                "trend": "stable"
            },
            "nutrients": {
                "nitrogen": {
                    "current": {
                        "value": 45,
                        "unit": "ppm"
                    },
                    "trend": "declining"
                }
            }
        },
        "water_management": {
            "irrigation_efficiency": "85%",
            "water_usage": {
                "total": {
                    "value": 2500,
                    "unit": "m³"
                },
                "per_ha": {
                    "value": 100,
                    "unit": "m³/ha"
                }
            }
        },
        "productivity": {
            "yield_potential": {
                "value": 6.5,
                "unit": "tons/ha"
            },
            "yield_gap": {
                "value": 1.3,
                "unit": "tons/ha"
            }
        }
    }
}
```

### Get Weather Impact Analysis
```http
GET /api/v1/analytics/weather-impact
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field | - |
| crop_type | string | Filter by crop type | - |
| event_types | array | Weather event types | all |

#### Response
```json
{
    "data": {
        "period": "2024-2025",
        "weather_events": [
            {
                "type": "frost",
                "occurrences": 3,
                "impact": {
                    "affected_area": {
                        "value": 15,
                        "unit": "ha"
                    },
                    "estimated_loss": {
                        "yield": "5%",
                        "value": {
                            "amount": 12500,
                            "currency": "USD"
                        }
                    }
                }
            }
        ],
        "correlations": [
            {
                "factor": "rainfall_timing",
                "crop_stage": "flowering",
                "correlation": 0.85,
                "impact": "positive"
            }
        ]
    }
}
```

### Get Operational Analytics
```http
GET /api/v1/analytics/operations
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| operation_type | string | Filter by operation | all |
| start_date | string | Analysis start date | -1y |
| end_date | string | Analysis end date | now |

#### Response
```json
{
    "data": {
        "period": {
            "start": "2024-01-19",
            "end": "2025-01-19"
        },
        "task_efficiency": {
            "completion_rate": "92%",
            "on_time_rate": "85%",
            "average_delay": "1.2 days"
        },
        "resource_utilization": {
            "equipment": [
                {
                    "type": "tractor",
                    "usage_hours": 850,
                    "utilization_rate": "75%",
                    "maintenance_cost": {
                        "value": 5200,
                        "currency": "USD"
                    }
                }
            ],
            "materials": [
                {
                    "type": "fertilizer",
                    "total_used": {
                        "value": 25000,
                        "unit": "kg"
                    },
                    "efficiency": "98%"
                }
            ]
        },
        "cost_analysis": {
            "per_hectare": {
                "value": 850,
                "currency": "USD"
            },
            "breakdown": [
                {
                    "category": "fertilizer",
                    "percentage": 35
                }
            ]
        }
    }
}
```

### Get Predictions
```http
GET /api/v1/analytics/predictions
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| field_id | string | Filter by field | - |
| crop_id | string | Filter by crop | - |
| prediction_type | string | Type of prediction | yield |

#### Response
```json
{
    "data": {
        "yield_prediction": {
            "crop_id": "c12345",
            "predicted_yield": {
                "value": 5.8,
                "unit": "tons/ha"
            },
            "confidence": "85%",
            "factors": [
                {
                    "name": "soil_moisture",
                    "impact": "positive",
                    "weight": 0.35
                }
            ]
        },
        "harvest_window": {
            "optimal_start": "2025-07-01",
            "optimal_end": "2025-07-10",
            "confidence": "80%"
        },
        "risk_assessment": {
            "pest_risk": "low",
            "disease_risk": "medium",
            "weather_risk": "low"
        }
    }
}
```

### Generate Report
```http
POST /api/v1/analytics/reports
```

#### Request Body
```json
{
    "report": {
        "type": "crop_performance",
        "period": {
            "start": "2024-01-19",
            "end": "2025-01-19"
        },
        "fields": ["f12345"],
        "metrics": [
            "yield",
            "costs",
            "efficiency"
        ],
        "format": "pdf"
    }
}
```

#### Response
```json
{
    "data": {
        "report_id": "r12345",
        "status": "generating",
        "estimated_completion": "2025-01-19T00:38:47Z",
        "download_url": "https://api.agritech.com/reports/r12345.pdf"
    }
}
```

## Analytics Metrics

### Crop Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| yield | Crop yield | tons/ha |
| growth_rate | Growth rate | cm/day |
| biomass | Plant biomass | kg/ha |
| leaf_area | Leaf area index | m²/m² |
| nutrient_uptake | Nutrient absorption | kg/ha |
| water_use | Water consumption | mm |

### Soil Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| organic_matter | Organic matter content | % |
| ph | Soil pH | pH |
| nutrients | NPK levels | ppm |
| moisture | Soil moisture | % |
| compaction | Soil compaction | kPa |

### Operational Metrics
| Metric | Description | Unit |
|--------|-------------|------|
| task_completion | Task completion rate | % |
| resource_usage | Resource utilization | % |
| cost_efficiency | Cost per hectare | USD/ha |
| labor_efficiency | Labor hours per hectare | h/ha |

## Prediction Models

### Available Models
| Model | Description | Accuracy Range |
|-------|-------------|----------------|
| yield | Crop yield prediction | 80-90% |
| disease | Disease risk prediction | 75-85% |
| pest | Pest outbreak prediction | 70-80% |
| harvest | Optimal harvest timing | 85-95% |

### Model Parameters
| Parameter | Description |
|-----------|-------------|
| historical_data | Past performance data |
| weather_data | Weather forecasts |
| soil_conditions | Soil sensor data |
| management_practices | Applied farming practices |

## Error Responses

### Invalid Request (400)
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid analysis parameters",
        "details": [
            {
                "field": "period",
                "code": "INVALID_RANGE",
                "message": "Analysis period cannot exceed 5 years"
            }
        ]
    }
}
```

### Insufficient Data (422)
```json
{
    "error": {
        "code": "INSUFFICIENT_DATA",
        "message": "Not enough data for analysis",
        "details": [
            {
                "field": "soil_sensors",
                "code": "MISSING_DATA",
                "message": "No soil sensor data available for specified period"
            }
        ]
    }
}
```

### Processing Error (500)
```json
{
    "error": {
        "code": "PROCESSING_ERROR",
        "message": "Analysis computation failed",
        "details": [
            {
                "code": "MODEL_ERROR",
                "message": "Prediction model failed to converge"
            }
        ]
    }
}
```

## Rate Limiting
- Basic analytics: 500 requests per hour
- Predictions: 200 requests per hour
- Report generation: 50 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For long-running analytics jobs, register webhook endpoints at:
```
POST /api/v1/analytics/webhooks
```

### Webhook Payload Format
```json
{
    "type": "analysis_complete",
    "analysis_id": "a12345",
    "timestamp": "2025-01-19T00:37:47.123Z",
    "status": "completed",
    "results_url": "https://api.agritech.com/analytics/a12345/results"
}
```
