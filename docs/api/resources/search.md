# Search API

## Overview
The Search API provides powerful search capabilities across all agricultural data, including fields, crops, equipment, and related resources. It supports full-text search, advanced filtering, faceted search, and geospatial queries.

## Endpoints

### Global Search

#### Search All Resources
```http
GET /api/v1/search
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| q | string | Search query | - |
| types | array | Resource types to search | all |
| page | integer | Page number | 1 |
| per_page | integer | Items per page | 20 |
| sort | string | Sort field and order | _score:desc |

##### Response
```json
{
    "data": {
        "total_hits": 125,
        "max_score": 0.95,
        "hits": [
            {
                "type": "field",
                "id": "f12345",
                "score": 0.95,
                "highlight": {
                    "name": ["<em>Rice</em> Field North"],
                    "description": ["Organic <em>rice</em> cultivation"]
                },
                "data": {
                    "name": "Rice Field North",
                    "area": "10ha",
                    "crop": "rice",
                    "status": "active"
                }
            }
        ],
        "facets": {
            "types": [
                {
                    "value": "field",
                    "count": 75
                },
                {
                    "value": "crop",
                    "count": 50
                }
            ],
            "status": [
                {
                    "value": "active",
                    "count": 100
                },
                {
                    "value": "inactive",
                    "count": 25
                }
            ]
        },
        "suggestions": [
            {
                "text": "rice",
                "frequency": 1000
            },
            {
                "text": "wheat",
                "frequency": 800
            }
        ]
    }
}
```

### Field Search

#### Search Fields
```http
GET /api/v1/search/fields
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| q | string | Search query | - |
| crop_types | array | Filter by crop types | all |
| status | array | Filter by status | all |
| min_area | number | Minimum area (ha) | - |
| max_area | number | Maximum area (ha) | - |
| location | object | Geospatial filter | - |

##### Example Request
```http
GET /api/v1/search/fields?q=organic+rice&crop_types=rice&min_area=5&location={"within":{"distance":"10km","center":{"lat":35.6762,"lon":139.6503}}}
```

##### Response
```json
{
    "data": {
        "total_hits": 50,
        "hits": [
            {
                "id": "f12345",
                "score": 0.95,
                "data": {
                    "name": "Rice Field North",
                    "area": "10ha",
                    "crop": {
                        "type": "rice",
                        "variety": "koshihikari",
                        "status": "growing"
                    },
                    "location": {
                        "lat": 35.6762,
                        "lon": 139.6503
                    },
                    "soil": {
                        "type": "clay",
                        "ph": 6.5
                    },
                    "certification": {
                        "organic": true,
                        "issued_date": "2024-01-19"
                    }
                },
                "highlight": {
                    "name": ["<em>Rice</em> Field North"],
                    "description": ["<em>Organic</em> <em>rice</em> cultivation"]
                }
            }
        ],
        "facets": {
            "crop_types": [
                {
                    "value": "rice",
                    "count": 30
                },
                {
                    "value": "wheat",
                    "count": 20
                }
            ],
            "soil_types": [
                {
                    "value": "clay",
                    "count": 25
                },
                {
                    "value": "loam",
                    "count": 25
                }
            ]
        },
        "aggregations": {
            "avg_area": {
                "value": 7.5,
                "unit": "ha"
            },
            "organic_percentage": {
                "value": 60,
                "unit": "percent"
            }
        }
    }
}
```

### Equipment Search

#### Search Equipment
```http
GET /api/v1/search/equipment
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| q | string | Search query | - |
| types | array | Equipment types | all |
| status | array | Equipment status | all |
| availability | boolean | Currently available | - |

##### Response
```json
{
    "data": {
        "total_hits": 75,
        "hits": [
            {
                "id": "e12345",
                "score": 0.90,
                "data": {
                    "name": "John Deere Tractor",
                    "model": "8R 410",
                    "type": "tractor",
                    "status": "active",
                    "location": {
                        "field_id": "f12345",
                        "lat": 35.6762,
                        "lon": 139.6503
                    },
                    "specifications": {
                        "power": "410hp",
                        "weight": "18000kg"
                    },
                    "maintenance": {
                        "last_service": "2025-01-10",
                        "next_service": "2025-02-10"
                    }
                }
            }
        ],
        "facets": {
            "types": [
                {
                    "value": "tractor",
                    "count": 30
                },
                {
                    "value": "harvester",
                    "count": 25
                }
            ],
            "status": [
                {
                    "value": "active",
                    "count": 60
                },
                {
                    "value": "maintenance",
                    "count": 15
                }
            ]
        }
    }
}
```

### Sensor Search

#### Search Sensors
```http
GET /api/v1/search/sensors
```

##### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| q | string | Search query | - |
| types | array | Sensor types | all |
| status | array | Sensor status | all |
| field_id | string | Filter by field | - |

##### Response
```json
{
    "data": {
        "total_hits": 100,
        "hits": [
            {
                "id": "s12345",
                "score": 0.85,
                "data": {
                    "name": "Soil Moisture Sensor 1",
                    "type": "soil_moisture",
                    "status": "active",
                    "location": {
                        "field_id": "f12345",
                        "lat": 35.6762,
                        "lon": 139.6503
                    },
                    "readings": {
                        "current": {
                            "value": 35,
                            "unit": "percent",
                            "timestamp": "2025-01-19T00:48:00Z"
                        },
                        "trend": "stable"
                    }
                }
            }
        ],
        "facets": {
            "types": [
                {
                    "value": "soil_moisture",
                    "count": 40
                },
                {
                    "value": "temperature",
                    "count": 35
                }
            ]
        }
    }
}
```

## Search Features

### Query Syntax
| Feature | Example | Description |
|---------|---------|-------------|
| Keywords | rice organic | Full text search |
| Phrases | "winter wheat" | Exact phrase match |
| Field specific | name:rice | Search in specific field |
| Boolean | rice AND organic | Logical operators |
| Wildcards | rice* | Prefix/suffix matching |

### Filters
| Filter Type | Example | Description |
|-------------|---------|-------------|
| Range | area:[5 TO 10] | Numeric range |
| Date | date:>2024-01-01 | Date comparison |
| Geo | location:10km | Geographic radius |
| Terms | status:(active OR pending) | Multiple values |

### Sorting
| Field | Direction | Description |
|-------|-----------|-------------|
| _score | desc | Relevance score |
| name | asc/desc | Alphabetical |
| area | asc/desc | Numeric value |
| date | asc/desc | Date/time |
| _geo | asc | Geographic distance |

### Facets
| Type | Description |
|------|-------------|
| Terms | Value counts |
| Range | Numeric ranges |
| Date | Time periods |
| Geo | Location clusters |

## Index Management

### Get Index Status
```http
GET /api/v1/search/index/status
```

##### Response
```json
{
    "data": {
        "indices": [
            {
                "name": "fields",
                "status": "healthy",
                "documents": {
                    "total": 10000,
                    "pending": 0
                },
                "size": {
                    "primary": "500MB",
                    "total": "1GB"
                },
                "last_updated": "2025-01-19T00:48:00Z"
            }
        ],
        "cluster_health": {
            "status": "green",
            "nodes": 3,
            "shards": {
                "active": 15,
                "relocating": 0
            }
        }
    }
}
```

### Reindex Data
```http
POST /api/v1/search/index/reindex
```

##### Request Body
```json
{
    "indices": ["fields", "equipment"],
    "options": {
        "refresh": true,
        "wait_for_completion": false
    }
}
```

##### Response
```json
{
    "data": {
        "task_id": "task12345",
        "status": "running",
        "started_at": "2025-01-19T00:48:00Z",
        "estimated_completion": "2025-01-19T01:48:00Z"
    }
}
```

## Error Responses

### Invalid Query (400)
```json
{
    "error": {
        "code": "INVALID_QUERY",
        "message": "Invalid search query",
        "details": [
            {
                "field": "q",
                "code": "SYNTAX_ERROR",
                "message": "Unmatched quotation in phrase"
            }
        ]
    }
}
```

### Invalid Filter (400)
```json
{
    "error": {
        "code": "INVALID_FILTER",
        "message": "Invalid filter parameters",
        "details": [
            {
                "field": "location",
                "code": "INVALID_FORMAT",
                "message": "Invalid GeoJSON format"
            }
        ]
    }
}
```

### Index Error (500)
```json
{
    "error": {
        "code": "INDEX_ERROR",
        "message": "Search index error",
        "details": [
            {
                "index": "fields",
                "code": "INDEX_UNAVAILABLE",
                "message": "Index is temporarily unavailable"
            }
        ]
    }
}
```

## Rate Limiting
- Search queries: 600 requests per minute
- Complex queries: 100 requests per minute
- Index operations: 10 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For search index events, register webhook endpoints at:
```
POST /api/v1/search/webhooks
```

### Webhook Payload Format
```json
{
    "type": "search_event",
    "timestamp": "2025-01-19T00:48:00.123Z",
    "event": {
        "type": "index_updated",
        "index": "fields",
        "details": {
            "documents_processed": 1000,
            "duration": "5m"
        }
    }
}
```

## Best Practices

### Query Optimization
1. Use specific field queries when possible
2. Limit wildcard queries to suffix matching
3. Use faceted navigation for filtering
4. Implement pagination for large result sets

### Index Management
1. Schedule reindexing during off-peak hours
2. Monitor index size and performance
3. Use aliases for zero-downtime reindexing
4. Implement regular index backup
