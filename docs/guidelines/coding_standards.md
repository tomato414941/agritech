# Coding Standards

## Code Documentation Standards

### Language Identifiers
Use the following language identifiers for code blocks:
- Python: `python`
- TypeScript/JavaScript: `typescript`
- SQL: `sql`
- YAML: `yaml`
- JSON: `json`
- Bash: `bash`

### Comment Style
1. Block Comments
   - Start with a brief purpose statement
   - Include parameters and return values
   - Note any important assumptions or limitations

2. Inline Comments
   - Keep brief and focused
   - Explain complex logic or business rules
   - Mark important sections with `IMPORTANT:`

### Example Documentation
```python
# Purpose: Calculate field metrics based on sensor data
# Input: Raw sensor readings (temperature, humidity, soil pH)
# Output: Aggregated field metrics for analysis
def calculate_field_metrics(sensor_data: List[SensorReading]) -> FieldMetrics:
    # IMPORTANT: Sensor readings must be from the last 24 hours
    if not validate_sensor_timestamps(sensor_data):
        raise ValueError("Invalid sensor data timestamps")

    # Process each metric type
    temperature = process_temperature(sensor_data)  # Convert to Celsius
    humidity = process_humidity(sensor_data)        # Normalize to 0-100%
    soil_ph = process_soil_ph(sensor_data)         # Validate pH range

    return FieldMetrics(
        temperature=temperature,
        humidity=humidity,
        soil_ph=soil_ph
    )
```

## Python (Backend)

### Style Guide
- Follow PEP 8 style guide
- Use type hints for all functions and methods
- Maximum line length: 88 characters (Black formatter default)
- Docstring format: Google style

### Example Code
```python
from datetime import datetime
from typing import List, Optional

class FieldManager:
    """Manages agricultural field operations and monitoring.

    This class handles field-related operations including crop planning,
    sensor data processing, and yield predictions.

    Attributes:
        field_id: Unique identifier for the field
        crop_type: Current crop being grown
        planting_date: Date when the crop was planted
    """

    def __init__(
        self,
        field_id: str,
        crop_type: str,
        planting_date: Optional[datetime] = None
    ) -> None:
        self.field_id = field_id
        self.crop_type = crop_type
        self.planting_date = planting_date or datetime.now()

    def calculate_yield_estimate(self, sensor_data: List[SensorData]) -> float:
        """Calculate estimated crop yield based on sensor data.

        Args:
            sensor_data: List of sensor readings including temperature,
                        humidity, and soil conditions.

        Returns:
            Estimated yield in metric tons per hectare.

        Raises:
            ValueError: If sensor data is invalid or insufficient.
        """
        # IMPORTANT: Minimum 7 days of sensor data required
        if len(sensor_data) < 7:
            raise ValueError("Insufficient sensor data")

        # Process environmental factors
        temperature_score = self._calculate_temperature_score(sensor_data)
        moisture_score = self._calculate_moisture_score(sensor_data)
        soil_score = self._calculate_soil_score(sensor_data)

        # Calculate final yield estimate
        return self._combine_scores(
            temperature_score,
            moisture_score,
            soil_score
        )
```

## TypeScript (Frontend)

### Style Guide
- Use ESLint with Airbnb configuration
- Use Prettier for formatting
- Use functional components
- Use TypeScript interfaces for prop types

### Example Code
```typescript
import React, { useState, useEffect } from 'react';
import { SensorData, FieldMetrics } from '../types';
import { fetchSensorData, calculateMetrics } from '../api';

interface FieldMonitorProps {
  fieldId: string;
  cropType: string;
  refreshInterval?: number;  // in milliseconds
}

/**
 * FieldMonitor Component
 * 
 * Displays real-time field monitoring data and metrics.
 * Updates automatically based on the specified refresh interval.
 */
const FieldMonitor: React.FC<FieldMonitorProps> = ({
  fieldId,
  cropType,
  refreshInterval = 5000,  // Default: 5 seconds
}) => {
  const [sensorData, setSensorData] = useState<SensorData | null>(null);
  const [metrics, setMetrics] = useState<FieldMetrics | null>(null);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    // IMPORTANT: Clean up interval on component unmount
    const intervalId = setInterval(async () => {
      try {
        // Fetch latest sensor data
        const data = await fetchSensorData(fieldId);
        setSensorData(data);

        // Calculate field metrics
        const fieldMetrics = await calculateMetrics(data);
        setMetrics(fieldMetrics);
      } catch (err) {
        setError(err.message);
      }
    }, refreshInterval);

    return () => clearInterval(intervalId);
  }, [fieldId, refreshInterval]);

  return (
    <div className="field-monitor">
      {error && <AlertBanner message={error} />}
      {sensorData && <SensorDataDisplay data={sensorData} />}
      {metrics && <MetricsDisplay metrics={metrics} />}
    </div>
  );
};

export default FieldMonitor;
```

## SQL

### Style Guide
- Use uppercase for SQL keywords
- Use snake_case for table and column names
- Include comments for complex queries
- Format queries for readability

### Example Code
```sql
-- Purpose: Get field performance metrics for the last 30 days
-- Tables: fields, sensor_data, crop_yields
-- Output: Field metrics with yield predictions

WITH sensor_metrics AS (
    -- Calculate average daily metrics
    SELECT
        field_id,
        DATE(timestamp) as metric_date,
        AVG(temperature) as avg_temperature,
        AVG(humidity) as avg_humidity,
        AVG(soil_moisture) as avg_soil_moisture
    FROM sensor_data
    WHERE timestamp >= CURRENT_DATE - INTERVAL '30 days'
    GROUP BY field_id, DATE(timestamp)
)
SELECT
    f.field_id,
    f.field_name,
    f.crop_type,
    -- IMPORTANT: Weight recent data more heavily
    AVG(sm.avg_temperature) as month_avg_temp,
    AVG(sm.avg_humidity) as month_avg_humidity,
    -- Calculate yield prediction
    CASE
        WHEN f.crop_type = 'corn' THEN calculate_corn_yield(sm.avg_temperature, sm.avg_humidity)
        WHEN f.crop_type = 'wheat' THEN calculate_wheat_yield(sm.avg_temperature, sm.avg_humidity)
        ELSE NULL
    END as predicted_yield
FROM fields f
JOIN sensor_metrics sm ON f.field_id = sm.field_id
GROUP BY f.field_id, f.field_name, f.crop_type;
```

## Configuration Files

### YAML Style Guide
- Use 2 space indentation
- Include comments for non-obvious settings
- Group related settings together
- Use environment variables for sensitive data

### Example YAML Configuration
```yaml
# AgriTech API Server Configuration
# Purpose: Define server settings and integrations

server:
  # Server settings
  host: 0.0.0.0
  port: 8000
  workers: 4

  # IMPORTANT: Security settings
  rate_limit:
    requests: 100
    period: 60  # seconds

database:
  # Database connection
  host: ${DB_HOST}
  port: ${DB_PORT}
  name: ${DB_NAME}
  # IMPORTANT: Use connection pooling
  pool:
    min_size: 5
    max_size: 20

monitoring:
  # Metrics collection
  enabled: true
  interval: 60  # seconds
  exporters:
    - prometheus
    - datadog

integrations:
  weather_api:
    url: https://api.weather.com/v1
    key: ${WEATHER_API_KEY}
    cache_ttl: 3600  # seconds
```

## Environment Variables

### Development
- Use `.env` files for local development
- Never commit `.env` files to version control
- Provide `.env.example` with dummy values

### Example `.env.example`
```bash
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=agritech
DB_USER=agritech_user
DB_PASSWORD=your_secure_password

# API Keys
WEATHER_API_KEY=your_weather_api_key
SOIL_API_KEY=your_soil_api_key

# Server Configuration
SERVER_HOST=0.0.0.0
SERVER_PORT=8000
NODE_ENV=development

# Security
JWT_SECRET=your_jwt_secret
ENCRYPTION_KEY=your_encryption_key
```

## Code Review Process

### Guidelines
- Review all changes before merging
- Use pull request templates
- Check for:
  - Code style compliance
  - Test coverage
  - Documentation updates
  - Security considerations

### Pull Request Template
```markdown
## Description
[Description of the changes]

## Type of change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Security considerations reviewed
- [ ] Environment variables updated
- [ ] Dependencies updated
