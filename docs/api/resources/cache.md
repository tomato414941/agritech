# Cache API

## Overview
The Cache API provides cache management and optimization capabilities for improving system performance. It enables control over data caching, invalidation strategies, and cache performance monitoring.

## Endpoints

### Cache Management

#### Set Cache Entry
```http
PUT /api/v1/cache/entries
```

##### Request Body
```json
{
    "entry": {
        "key": "field_status_f12345",
        "value": {
            "field_id": "f12345",
            "status": "active",
            "last_updated": "2025-01-19T00:58:32Z",
            "metrics": {
                "soil_moisture": 35,
                "temperature": 28
            }
        },
        "ttl": 3600,
        "tags": ["field", "status"],
        "options": {
            "compression": true,
            "priority": "high",
            "stale_while_revalidate": 300
        }
    }
}
```

##### Response
```json
{
    "data": {
        "key": "field_status_f12345",
        "stored_at": "2025-01-19T00:58:32Z",
        "expires_at": "2025-01-19T01:58:32Z",
        "size": "1.2KB"
    }
}
```

#### Get Cache Entry
```http
GET /api/v1/cache/entries/{key}
```

##### Response
```json
{
    "data": {
        "key": "field_status_f12345",
        "value": {
            "field_id": "f12345",
            "status": "active",
            "last_updated": "2025-01-19T00:58:32Z",
            "metrics": {
                "soil_moisture": 35,
                "temperature": 28
            }
        },
        "metadata": {
            "created_at": "2025-01-19T00:58:32Z",
            "expires_at": "2025-01-19T01:58:32Z",
            "hits": 150,
            "size": "1.2KB"
        }
    }
}
```

### Cache Invalidation

#### Invalidate Cache Entries
```http
POST /api/v1/cache/invalidate
```

##### Request Body
```json
{
    "invalidation": {
        "type": "pattern",
        "pattern": "field_status_*",
        "tags": ["field", "status"],
        "options": {
            "soft_delete": true,
            "delay": "5s",
            "cascade": true
        }
    }
}
```

##### Response
```json
{
    "data": {
        "invalidation_id": "inv12345",
        "matched_keys": 100,
        "status": "processing",
        "estimated_completion": "2025-01-19T00:58:37Z"
    }
}
```

### Cache Statistics

#### Get Cache Statistics
```http
GET /api/v1/cache/stats
```

##### Response
```json
{
    "data": {
        "summary": {
            "total_entries": 10000,
            "total_size": "100MB",
            "hit_rate": 0.95,
            "miss_rate": 0.05
        },
        "by_type": {
            "field_status": {
                "entries": 5000,
                "size": "50MB",
                "hit_rate": 0.98
            },
            "sensor_data": {
                "entries": 5000,
                "size": "50MB",
                "hit_rate": 0.92
            }
        },
        "performance": {
            "average_latency": {
                "get": "2ms",
                "set": "5ms"
            },
            "throughput": {
                "reads": 1000,
                "writes": 100,
                "unit": "ops/second"
            }
        }
    }
}
```

## Cache Types

### Storage Types
| Type | Description |
|------|-------------|
| memory | In-memory cache |
| distributed | Distributed cache |
| local | Local file cache |
| hybrid | Multi-level cache |

### Entry Types
| Type | Description |
|------|-------------|
| simple | Key-value pair |
| hash | Hash map |
| list | Ordered list |
| set | Unique values |

### Compression Types
| Type | Description |
|------|-------------|
| none | No compression |
| gzip | GZIP compression |
| lz4 | LZ4 compression |
| custom | Custom compression |

## Cache Policies

### Eviction Policies
| Policy | Description |
|--------|-------------|
| lru | Least recently used |
| lfu | Least frequently used |
| fifo | First in, first out |
| priority | Priority based |

### TTL Policies
| Policy | Description |
|--------|-------------|
| fixed | Fixed duration |
| sliding | Reset on access |
| custom | Custom rules |
| infinite | Never expire |

### Invalidation Strategies
| Strategy | Description |
|----------|-------------|
| immediate | Instant removal |
| delayed | Delayed removal |
| soft | Mark as invalid |
| cascade | Recursive removal |

## Error Responses

### Invalid Key (400)
```json
{
    "error": {
        "code": "INVALID_KEY",
        "message": "Invalid cache key format",
        "details": [
            {
                "field": "key",
                "code": "FORMAT_ERROR",
                "message": "Key contains invalid characters"
            }
        ]
    }
}
```

### Cache Full (507)
```json
{
    "error": {
        "code": "CACHE_FULL",
        "message": "Cache capacity exceeded",
        "details": [
            {
                "code": "CAPACITY_EXCEEDED",
                "message": "Cache is at 100% capacity (100MB/100MB)"
            }
        ]
    }
}
```

### Operation Failed (500)
```json
{
    "error": {
        "code": "OPERATION_FAILED",
        "message": "Cache operation failed",
        "details": [
            {
                "operation": "set",
                "code": "WRITE_ERROR",
                "message": "Unable to write to cache storage"
            }
        ]
    }
}
```

## Rate Limiting
- Cache operations: 10000 requests per minute
- Statistics retrieval: 60 requests per minute
- Invalidation operations: 10 requests per minute
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For cache events, register webhook endpoints at:
```
POST /api/v1/cache/webhooks
```

### Webhook Payload Format
```json
{
    "type": "cache_event",
    "timestamp": "2025-01-19T00:58:32.123Z",
    "event": {
        "type": "cache_full",
        "details": {
            "current_size": "100MB",
            "capacity": "100MB",
            "evicted_entries": 100
        }
    }
}
```

## Best Practices

### Cache Design
1. Use appropriate key naming
2. Set reasonable TTLs
3. Implement cache warming
4. Monitor hit rates

### Performance Optimization
1. Use compression when appropriate
2. Implement multi-level caching
3. Configure optimal cache sizes
4. Monitor cache efficiency

### Data Consistency
1. Implement versioning
2. Use appropriate invalidation
3. Handle race conditions
4. Maintain data freshness

### Resource Management
1. Monitor memory usage
2. Implement size limits
3. Configure eviction policies
4. Clean up expired entries
