# Monitoring Guide

## Monitoring Overview

### Monitoring Layers
1. Infrastructure Monitoring
2. Application Performance Monitoring
3. Business Metrics Monitoring
4. Security Monitoring

## Infrastructure Monitoring

### Server Metrics
- CPU Usage
- Memory Utilization
- Disk Space
- Network I/O

### Example Prometheus Configuration
```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'api'
    static_configs:
      - targets: ['localhost:8000']
```

### Alert Rules
```yaml
groups:
  - name: hardware
    rules:
      - alert: HighCPUUsage
        expr: cpu_usage_percent > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: High CPU usage detected

      - alert: LowDiskSpace
        expr: disk_free_percent < 20
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: Low disk space warning
```

## Application Performance Monitoring

### API Metrics
- Request Rate
- Response Time
- Error Rate
- Endpoint Usage

### Example FastAPI Middleware
```python
@app.middleware("http")
async def add_metrics(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    duration = time.time() - start_time
    
    metrics.request_duration.observe(
        duration,
        method=request.method,
        path=request.url.path
    )
    return response
```

### Database Metrics
- Query Performance
- Connection Pool Status
- Transaction Rate
- Lock Statistics

### Example SQL Monitoring
```python
from sqlalchemy import event

@event.listens_for(Engine, "before_cursor_execute")
def before_cursor_execute(conn, cursor, statement, parameters, context, executemany):
    conn.info.setdefault('query_start_time', []).append(time.time())

@event.listens_for(Engine, "after_cursor_execute")
def after_cursor_execute(conn, cursor, statement, parameters, context, executemany):
    total = time.time() - conn.info['query_start_time'].pop()
    metrics.db_query_duration.observe(total, query=statement)
```

## Business Metrics

### Key Performance Indicators
- Active Users
- Field Registrations
- Crop Planning Usage
- Report Generation

### Example Metrics Collection
```python
class MetricsService:
    async def track_user_activity(self, user_id: UUID, action: str):
        metrics.user_actions.inc(
            action=action,
            user_id=str(user_id)
        )

    async def track_field_metrics(self, field_id: UUID):
        field = await self.field_repository.get_by_id(field_id)
        metrics.total_field_area.set(
            field.area,
            user_id=str(field.user_id)
        )
```

## Security Monitoring

### Security Metrics
- Failed Login Attempts
- API Rate Limit Breaches
- Permission Violations
- Token Revocations

### Example Security Monitoring
```python
class SecurityMonitor:
    async def track_login_attempt(self, email: str, success: bool):
        metrics.login_attempts.inc(
            success=success,
            email_domain=email.split('@')[1]
        )

    async def track_rate_limit(self, ip: str, endpoint: str):
        metrics.rate_limits.inc(
            ip=ip,
            endpoint=endpoint
        )
```

## Logging Configuration

### Structured Logging
```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'json': {
            '()': 'pythonjsonlogger.jsonlogger.JsonFormatter',
            'fmt': '%(levelname)s %(asctime)s %(request_id)s %(message)s'
        }
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'json'
        }
    },
    'loggers': {
        'app': {
            'handlers': ['console'],
            'level': 'INFO'
        }
    }
}
```

## Alerting Configuration

### Alert Channels
1. Email Notifications
2. Slack Integration
3. SMS Alerts (Critical)
4. On-Call Rotation

### Example Alert Manager Configuration
```yaml
route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  receiver: 'team-emails'

receivers:
  - name: 'team-emails'
    email_configs:
      - to: 'team@agritech.com'
        from: 'alerts@agritech.com'
        smarthost: 'smtp.gmail.com:587'
        auth_username: '${SMTP_USER}'
        auth_password: '${SMTP_PASSWORD}'

  - name: 'slack-notifications'
    slack_configs:
      - api_url: '${SLACK_WEBHOOK_URL}'
        channel: '#alerts'
```

## Dashboards

### System Dashboard
```json
{
  "dashboard": {
    "title": "System Overview",
    "panels": [
      {
        "title": "CPU Usage",
        "type": "graph",
        "targets": [
          {
            "expr": "cpu_usage_percent"
          }
        ]
      },
      {
        "title": "Memory Usage",
        "type": "graph",
        "targets": [
          {
            "expr": "memory_usage_bytes"
          }
        ]
      }
    ]
  }
}
```

### Application Dashboard
```json
{
  "dashboard": {
    "title": "API Performance",
    "panels": [
      {
        "title": "Request Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_requests_total[5m])"
          }
        ]
      },
      {
        "title": "Error Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_errors_total[5m])"
          }
        ]
      }
    ]
  }
}
```

## Maintenance Procedures

### Regular Checks
- Log Analysis
- Metric Review
- Alert Configuration
- Dashboard Updates

### Incident Response
1. Alert Verification
2. Impact Assessment
3. Resolution Steps
4. Post-Mortem Analysis

## Documentation

### Runbooks
- Alert Response Procedures
- Troubleshooting Guides
- Escalation Paths
- Contact Information

### Metrics Documentation
- Metric Definitions
- Collection Methods
- Retention Policies
- Access Controls
