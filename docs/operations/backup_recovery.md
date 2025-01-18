# Backup and Recovery Procedures

## Backup Strategy

### Backup Types

#### Database Backups
1. Full Backups
   - Daily full database dumps
   - Stored in encrypted format
   - Retained for 30 days

2. Incremental Backups
   - Hourly transaction log backups
   - Continuous archiving
   - Retained for 7 days

3. Snapshot Backups
   - Weekly system snapshots
   - Include all configurations
   - Retained for 90 days

### Example Backup Scripts

#### Database Backup
```bash
#!/bin/bash
# backup_database.sh

# Configuration
DB_NAME="agritech"
BACKUP_DIR="/backups/database"
DATE=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=30

# Create backup
pg_dump $DB_NAME | gzip > $BACKUP_DIR/full_backup_$DATE.sql.gz

# Encrypt backup
gpg --encrypt \
    --recipient backup@agritech.com \
    $BACKUP_DIR/full_backup_$DATE.sql.gz

# Clean old backups
find $BACKUP_DIR -name "full_backup_*.sql.gz" -mtime +$RETENTION_DAYS -delete
```

#### File Backup
```bash
#!/bin/bash
# backup_files.sh

# Configuration
SOURCE_DIR="/app/data"
BACKUP_DIR="/backups/files"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup
tar -czf $BACKUP_DIR/files_$DATE.tar.gz $SOURCE_DIR

# Sync to remote storage
aws s3 sync $BACKUP_DIR s3://agritech-backups/files/
```

## Recovery Procedures

### Database Recovery

#### Point-in-Time Recovery
```bash
#!/bin/bash
# recover_database.sh

# Configuration
DB_NAME="agritech"
BACKUP_FILE=$1
TARGET_TIME=$2

# Stop application
systemctl stop agritech-api

# Restore database
pg_restore -d $DB_NAME $BACKUP_FILE

# Apply WAL files up to target time
pg_ctl -D /var/lib/postgresql/data promote
psql -d $DB_NAME -c "SELECT pg_wal_replay_resume();"

# Start application
systemctl start agritech-api
```

#### Full Database Restore
```sql
-- Restore procedure
DROP DATABASE IF EXISTS agritech;
CREATE DATABASE agritech;
\c agritech

-- Restore from backup
\i backup_file.sql

-- Verify restoration
SELECT COUNT(*) FROM users;
SELECT COUNT(*) FROM fields;
SELECT COUNT(*) FROM crops;
```

### File Recovery

#### Single File Recovery
```bash
#!/bin/bash
# recover_file.sh

FILE_PATH=$1
BACKUP_DATE=$2

# Find file in backup
aws s3 ls s3://agritech-backups/files/$BACKUP_DATE/

# Restore file
aws s3 cp s3://agritech-backups/files/$BACKUP_DATE/$FILE_PATH ./
```

#### Full System Recovery
```bash
#!/bin/bash
# recover_system.sh

# Configuration
BACKUP_DATE=$1
RESTORE_DIR="/app/restore"

# Restore system files
aws s3 sync s3://agritech-backups/files/$BACKUP_DATE $RESTORE_DIR

# Restore database
./recover_database.sh $BACKUP_DATE

# Verify system integrity
./verify_system.sh
```

## Backup Verification

### Verification Procedures
```python
async def verify_backup(backup_path: str) -> bool:
    """Verify backup integrity and recoverability."""
    try:
        # Check backup file
        if not os.path.exists(backup_path):
            raise FileNotFoundError("Backup file not found")

        # Verify checksum
        expected_checksum = await get_backup_checksum(backup_path)
        actual_checksum = await calculate_checksum(backup_path)
        if expected_checksum != actual_checksum:
            raise ValueError("Backup checksum mismatch")

        # Test restore in temporary database
        await test_restore(backup_path)

        return True
    except Exception as e:
        logger.error(f"Backup verification failed: {str(e)}")
        return False
```

### Testing Recovery
```python
async def test_restore(backup_path: str):
    """Test backup restoration in isolated environment."""
    test_db = f"test_restore_{int(time.time())}"
    try:
        # Create test database
        await create_test_database(test_db)

        # Restore backup
        await restore_backup(backup_path, test_db)

        # Verify data integrity
        await verify_data_integrity(test_db)
    finally:
        # Cleanup
        await drop_test_database(test_db)
```

## Monitoring and Reporting

### Backup Monitoring
```python
class BackupMonitor:
    async def check_backup_status(self):
        """Monitor backup process and report status."""
        metrics = {
            'last_backup_time': None,
            'backup_size': 0,
            'backup_duration': 0,
            'verification_status': False
        }

        try:
            backup_info = await get_latest_backup_info()
            metrics.update({
                'last_backup_time': backup_info.timestamp,
                'backup_size': backup_info.size,
                'backup_duration': backup_info.duration,
                'verification_status': await verify_backup(backup_info.path)
            })
        except Exception as e:
            logger.error(f"Backup monitoring failed: {str(e)}")

        return metrics
```

### Backup Reports
```python
async def generate_backup_report():
    """Generate backup status report."""
    report = {
        'timestamp': datetime.utcnow(),
        'backups': []
    }

    # Collect backup statistics
    for backup in await list_backups():
        report['backups'].append({
            'type': backup.type,
            'timestamp': backup.timestamp,
            'size': backup.size,
            'status': backup.status,
            'verification': backup.verified
        })

    # Send report
    await send_backup_report(report)
```

## Disaster Recovery

### Recovery Time Objectives (RTO)
- Database: 1 hour
- File Storage: 2 hours
- Full System: 4 hours

### Recovery Point Objectives (RPO)
- Database: 5 minutes
- File Storage: 1 hour
- Configuration: 24 hours

### Recovery Procedures
1. Assess Impact
2. Notify Stakeholders
3. Initialize Recovery
4. Verify Recovery
5. Resume Operations

## Security Considerations

### Encryption
- All backups encrypted at rest
- Secure key management
- Regular key rotation
- Access control

### Access Control
- Role-based access
- Audit logging
- Multi-factor authentication
- IP restrictions

## Documentation

### Required Documentation
- Backup procedures
- Recovery procedures
- Verification steps
- Contact information

### Maintenance
- Regular updates
- Version control
- Review process
- Training materials
