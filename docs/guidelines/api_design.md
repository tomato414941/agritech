# API Design Guidelines

## RESTful Endpoints

### Resource Naming
- Use plural nouns for resources
- Use lowercase letters
- Use hyphens for word separation
- Include API version in URL

### Examples
```
GET    /api/v1/fields           # List fields
POST   /api/v1/fields           # Create field
GET    /api/v1/fields/{id}      # Get field
PUT    /api/v1/fields/{id}      # Update field
DELETE /api/v1/fields/{id}      # Delete field
```

### Resource Relationships
```
GET    /api/v1/fields/{id}/crops        # List crops in field
POST   /api/v1/fields/{id}/crops        # Add crop to field
GET    /api/v1/fields/{id}/crops/{id}   # Get specific crop in field
```

## Request/Response Format

### Request Format
```json
{
    "field": {
        "name": "North Field",
        "area": 100.5,
        "unit": "hectares",
        "location": {
            "latitude": 35.6895,
            "longitude": 139.6917
        }
    }
}
```

### Response Format
```json
{
    "data": {
        // Response data
    },
    "meta": {
        "page": 1,
        "per_page": 10,
        "total": 100
    },
    "links": {
        "self": "/api/v1/fields?page=1",
        "next": "/api/v1/fields?page=2",
        "prev": null
    }
}
```

## HTTP Status Codes

### Success Codes
- 200: OK (Success)
- 201: Created
- 204: No Content

### Client Error Codes
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 422: Unprocessable Entity

### Server Error Codes
- 500: Internal Server Error
- 503: Service Unavailable

## Error Handling

### Error Response Format
```json
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "The request was invalid",
        "details": [
            {
                "field": "name",
                "message": "Field name is required"
            }
        ]
    }
}
```

## Query Parameters

### Filtering
```
GET /api/v1/fields?status=active
GET /api/v1/fields?crop_type=wheat
GET /api/v1/fields?min_area=100&max_area=200
```

### Sorting
```
GET /api/v1/fields?sort=name
GET /api/v1/fields?sort=-created_at
```

### Pagination
```
GET /api/v1/fields?page=2&per_page=20
```

## API Documentation

### OpenAPI/Swagger
- Use OpenAPI 3.0 specification
- Include detailed descriptions
- Provide example values
- Document all possible responses

### Example Documentation
```yaml
/fields:
  get:
    summary: List all fields
    parameters:
      - name: status
        in: query
        description: Filter by field status
        schema:
          type: string
          enum: [active, inactive]
    responses:
      200:
        description: List of fields
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/FieldList'
```

## API Versioning

### Version Strategy
- Include version in URL path
- Support at least one previous version
- Deprecation process for old versions
- Version documentation

### Example
```
/api/v1/fields  # Current version
/api/v2/fields  # New version with breaking changes
```
