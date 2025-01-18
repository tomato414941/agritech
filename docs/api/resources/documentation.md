# Documentation API

## Overview
The Documentation API provides programmatic access to API documentation, examples, and specifications. It enables automatic generation of documentation, management of code samples, and version tracking of API changes.

## Endpoints

### Get API Specification
```http
GET /api/v1/docs/specification
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| version | string | API version | latest |
| format | string | Spec format (openapi, raml) | openapi |
| include | array | Additional content | [] |

#### Response
```json
{
    "data": {
        "version": "1.0.0",
        "format": "openapi",
        "spec": {
            "openapi": "3.0.0",
            "info": {
                "title": "AgriTech API",
                "version": "1.0.0",
                "description": "Agricultural Management Platform API"
            },
            "servers": [
                {
                    "url": "https://api.agritech.com/v1",
                    "description": "Production API Server"
                }
            ],
            "paths": {
                "/fields": {
                    "get": {
                        "summary": "List Fields",
                        "description": "Retrieve a list of agricultural fields"
                    }
                }
            },
            "components": {
                "schemas": {
                    "Field": {
                        "type": "object",
                        "properties": {
                            "id": {
                                "type": "string"
                            }
                        }
                    }
                }
            }
        },
        "generated_at": "2025-01-19T00:42:56Z"
    }
}
```

### Get Code Examples
```http
GET /api/v1/docs/examples
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| endpoint | string | API endpoint | - |
| language | string | Programming language | all |
| framework | string | Framework/SDK | - |

#### Response
```json
{
    "data": {
        "endpoint": "/fields",
        "method": "GET",
        "examples": [
            {
                "language": "python",
                "framework": "requests",
                "code": "import requests\n\napi_key = 'your_api_key'\nurl = 'https://api.agritech.com/v1/fields'\nheaders = {'Authorization': f'Bearer {api_key}'}\n\nresponse = requests.get(url, headers=headers)\nfields = response.json()",
                "description": "List fields using Python requests library"
            },
            {
                "language": "javascript",
                "framework": "fetch",
                "code": "const apiKey = 'your_api_key';\nconst url = 'https://api.agritech.com/v1/fields';\n\nfetch(url, {\n  headers: {\n    'Authorization': `Bearer ${apiKey}`\n  }\n})\n.then(response => response.json())\n.then(data => console.log(data));",
                "description": "List fields using JavaScript fetch API"
            }
        ]
    }
}
```

### Get API Changes
```http
GET /api/v1/docs/changes
```

#### Query Parameters
| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| from_version | string | Start version | - |
| to_version | string | End version | latest |
| type | string | Change type | all |

#### Response
```json
{
    "data": {
        "changes": [
            {
                "version": "1.0.1",
                "date": "2025-01-15",
                "type": "addition",
                "category": "endpoint",
                "description": "Added new endpoint for batch field updates",
                "details": {
                    "endpoint": "/fields/batch",
                    "method": "PUT",
                    "summary": "Update multiple fields in a single request"
                },
                "migration_guide": {
                    "steps": [
                        "Update API client to use new batch endpoint",
                        "Adjust request payload format"
                    ]
                }
            }
        ]
    }
}
```

### Generate SDK
```http
POST /api/v1/docs/sdk/generate
```

#### Request Body
```json
{
    "sdk": {
        "language": "python",
        "name": "agritech-python",
        "version": "1.0.0",
        "options": {
            "async_support": true,
            "type_hints": true,
            "tests": true
        },
        "documentation": {
            "format": "sphinx",
            "include_examples": true
        }
    }
}
```

#### Response
```json
{
    "data": {
        "sdk_id": "sdk12345",
        "status": "generating",
        "download_url": "https://api.agritech.com/docs/sdk/python-1.0.0.zip",
        "documentation_url": "https://docs.agritech.com/sdk/python/1.0.0",
        "estimated_completion": "2025-01-19T00:43:56Z"
    }
}
```

## Documentation Resources

### API Specification Formats
| Format | Description | Schema |
|--------|-------------|---------|
| openapi | OpenAPI/Swagger | 3.0.0 |
| raml | RAML | 1.0 |
| graphql | GraphQL Schema | - |

### Programming Languages
| Language | Frameworks/Libraries |
|----------|---------------------|
| python | requests, aiohttp |
| javascript | fetch, axios |
| java | okhttp, retrofit |
| go | net/http, resty |
| ruby | rest-client, faraday |

### Documentation Formats
| Format | Use Case |
|--------|----------|
| markdown | General documentation |
| sphinx | Python documentation |
| javadoc | Java documentation |
| swagger-ui | API explorer |

## Version Management

### Version Components
| Component | Description |
|-----------|-------------|
| major | Breaking changes |
| minor | New features |
| patch | Bug fixes |

### Change Types
| Type | Description |
|------|-------------|
| addition | New features/endpoints |
| modification | Changed behavior |
| deprecation | Planned removal |
| removal | Removed feature |

## SDK Generation

### SDK Features
| Feature | Description |
|---------|-------------|
| async_support | Async/await support |
| type_hints | Type annotations |
| error_handling | Exception handling |
| retry_logic | Automatic retries |
| rate_limiting | Rate limit handling |

### Documentation Options
| Option | Description |
|--------|-------------|
| api_reference | API method docs |
| examples | Code examples |
| tutorials | Getting started |
| migration | Version migration |

## Error Responses

### Invalid Version (400)
```json
{
    "error": {
        "code": "INVALID_VERSION",
        "message": "Invalid version specified",
        "details": [
            {
                "field": "version",
                "code": "VERSION_NOT_FOUND",
                "message": "Version '2.0.0' does not exist"
            }
        ]
    }
}
```

### Generation Error (500)
```json
{
    "error": {
        "code": "GENERATION_ERROR",
        "message": "SDK generation failed",
        "details": [
            {
                "code": "COMPILATION_ERROR",
                "message": "Failed to compile SDK source code"
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
        "message": "Invalid format specified",
        "details": [
            {
                "field": "format",
                "code": "UNSUPPORTED_FORMAT",
                "message": "Format 'wiki' is not supported"
            }
        ]
    }
}
```

## Rate Limiting
- Documentation retrieval: 1000 requests per hour
- SDK generation: 10 requests per hour
- Example code retrieval: 500 requests per hour
- Rate limit headers included in response:
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

## Webhooks
For documentation update notifications, register webhook endpoints at:
```
POST /api/v1/docs/webhooks
```

### Webhook Payload Format
```json
{
    "type": "doc_update",
    "version": "1.0.1",
    "timestamp": "2025-01-19T00:42:56.123Z",
    "changes": [
        {
            "type": "addition",
            "path": "/fields/batch",
            "description": "Added new batch update endpoint"
        }
    ]
}
```

## Documentation Templates
Access standard documentation templates at:
```
GET /api/v1/docs/templates/{template_type}
```

### Available Templates
| Type | Description |
|------|-------------|
| api_reference | API endpoint reference |
| getting_started | Getting started guide |
| authentication | Authentication guide |
| migration | Version migration guide |
| best_practices | API best practices |

### Template Format
```json
{
    "template": {
        "format": "markdown",
        "sections": [
            {
                "title": "Overview",
                "content": "# {api_name}\n\n{api_description}"
            },
            {
                "title": "Authentication",
                "content": "## Authentication\n\nThis API uses {auth_method} for authentication."
            }
        ],
        "variables": {
            "api_name": "AgriTech API",
            "api_description": "Agricultural Management Platform API",
            "auth_method": "Bearer tokens"
        }
    }
}
```
