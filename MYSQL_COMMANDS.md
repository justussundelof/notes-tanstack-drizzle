# MySQL Command Reference

## Connect to MySQL
```bash
# Connect as root
mysql -u root -p

# Connect to specific database
mysql -u username -p database_name

# Connect to remote host
mysql -h hostname -u username -p database_name
```

## Database Commands

### Show Databases
```sql
SHOW DATABASES;
```

### Create Database
```sql
CREATE DATABASE database_name;
CREATE DATABASE IF NOT EXISTS database_name;
CREATE DATABASE database_name CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Use/Select Database
```sql
USE database_name;
```

### Delete Database
```sql
DROP DATABASE database_name;
DROP DATABASE IF EXISTS database_name;
```

### Show Current Database
```sql
SELECT DATABASE();
```

## User Management

### Show Users
```sql
SELECT User, Host FROM mysql.user;
```

### Create User
```sql
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  -- Any host
```

### Delete User
```sql
DROP USER 'username'@'localhost';
```

### Change Password
```sql
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';
SET PASSWORD FOR 'username'@'localhost' = 'new_password';
```

### Grant Privileges
```sql
-- Grant all privileges on specific database
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';

-- Grant specific privileges
GRANT SELECT, INSERT, UPDATE ON database_name.* TO 'username'@'localhost';

-- Grant all on all databases (admin)
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost';

-- Apply changes
FLUSH PRIVILEGES;
```

### Revoke Privileges
```sql
REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'localhost';
FLUSH PRIVILEGES;
```

### Show Privileges
```sql
SHOW GRANTS FOR 'username'@'localhost';
SHOW GRANTS;  -- Current user
```

## Table Commands

### Show Tables
```sql
SHOW TABLES;
SHOW TABLES FROM database_name;
```

### Show Table Structure
```sql
DESCRIBE table_name;
DESC table_name;
SHOW COLUMNS FROM table_name;
```

### Show Create Statement
```sql
SHOW CREATE TABLE table_name;
SHOW CREATE DATABASE database_name;
```

### Create Table
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Delete Table
```sql
DROP TABLE table_name;
DROP TABLE IF EXISTS table_name;

-- Delete data but keep structure
TRUNCATE TABLE table_name;
```

### Alter Table
```sql
-- Add column
ALTER TABLE table_name ADD COLUMN column_name VARCHAR(50);

-- Drop column
ALTER TABLE table_name DROP COLUMN column_name;

-- Modify column
ALTER TABLE table_name MODIFY COLUMN column_name INT;

-- Rename column
ALTER TABLE table_name RENAME COLUMN old_name TO new_name;

-- Rename table
ALTER TABLE old_table_name RENAME TO new_table_name;
```

## Data Commands

### Select Data
```sql
SELECT * FROM table_name;
SELECT column1, column2 FROM table_name;
SELECT * FROM table_name WHERE condition;
SELECT * FROM table_name ORDER BY column_name DESC;
SELECT * FROM table_name LIMIT 10;
SELECT COUNT(*) FROM table_name;
```

### Insert Data
```sql
INSERT INTO table_name (column1, column2) VALUES ('value1', 'value2');
INSERT INTO table_name (column1, column2) VALUES
    ('value1', 'value2'),
    ('value3', 'value4');
```

### Update Data
```sql
UPDATE table_name SET column1 = 'value1' WHERE condition;
UPDATE table_name SET column1 = 'value1', column2 = 'value2' WHERE id = 1;
```

### Delete Data
```sql
DELETE FROM table_name WHERE condition;
DELETE FROM table_name;  -- Delete all rows (careful!)
```

## Information & Status

### Show Database Size
```sql
SELECT
    table_schema AS 'Database',
    ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Size (MB)'
FROM information_schema.tables
GROUP BY table_schema;
```

### Show Table Sizes
```sql
SELECT
    table_name AS 'Table',
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)'
FROM information_schema.tables
WHERE table_schema = 'database_name'
ORDER BY (data_length + index_length) DESC;
```

### Show Process List
```sql
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;
```

### Kill Process
```sql
KILL process_id;
```

### Show Variables
```sql
SHOW VARIABLES;
SHOW VARIABLES LIKE 'max_connections';
```

### Show Status
```sql
SHOW STATUS;
SHOW STATUS LIKE 'Threads_connected';
```

## Export & Import

### Export Database (Command Line)
```bash
# Export entire database
mysqldump -u username -p database_name > backup.sql

# Export specific tables
mysqldump -u username -p database_name table1 table2 > backup.sql

# Export all databases
mysqldump -u username -p --all-databases > all_databases.sql

# Export with compression
mysqldump -u username -p database_name | gzip > backup.sql.gz
```

### Import Database (Command Line)
```bash
# Import from SQL file
mysql -u username -p database_name < backup.sql

# Import compressed file
gunzip < backup.sql.gz | mysql -u username -p database_name

# Import from MySQL prompt
SOURCE /path/to/backup.sql;
```

## Index Management

### Show Indexes
```sql
SHOW INDEX FROM table_name;
```

### Create Index
```sql
CREATE INDEX index_name ON table_name (column_name);
CREATE UNIQUE INDEX index_name ON table_name (column_name);
CREATE INDEX index_name ON table_name (column1, column2);  -- Composite
```

### Drop Index
```sql
DROP INDEX index_name ON table_name;
ALTER TABLE table_name DROP INDEX index_name;
```

## Useful Utilities

### Check Table
```sql
CHECK TABLE table_name;
```

### Repair Table
```sql
REPAIR TABLE table_name;
```

### Optimize Table
```sql
OPTIMIZE TABLE table_name;
```

### Analyze Table
```sql
ANALYZE TABLE table_name;
```

## Quick Reset Commands

### Reset Root Password (if locked out)
```bash
# Stop MySQL
sudo systemctl stop mysql

# Start without password
sudo mysqld_safe --skip-grant-tables &

# Connect and reset
mysql -u root
```
```sql
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
FLUSH PRIVILEGES;
EXIT;
```

### Delete and Recreate Database
```sql
DROP DATABASE IF EXISTS notes_db;
CREATE DATABASE notes_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Reset All Tables in Database
```sql
-- Show command to drop all tables
SELECT CONCAT('DROP TABLE IF EXISTS `', table_name, '`;')
FROM information_schema.tables
WHERE table_schema = 'database_name';

-- Copy output and execute
```

## Docker MySQL Commands

```bash
# Start MySQL container
docker run --name mysql-notes \
  -e MYSQL_ROOT_PASSWORD=password \
  -e MYSQL_DATABASE=notes_db \
  -p 3306:3306 \
  -d mysql:8.0

# Stop container
docker stop mysql-notes

# Start existing container
docker start mysql-notes

# Remove container
docker rm mysql-notes

# Execute MySQL command in container
docker exec -it mysql-notes mysql -u root -p

# View logs
docker logs mysql-notes

# Backup from Docker
docker exec mysql-notes mysqldump -u root -ppassword notes_db > backup.sql

# Restore to Docker
docker exec -i mysql-notes mysql -u root -ppassword notes_db < backup.sql
```

## Common Patterns

### Create User & Database Setup
```sql
-- Complete setup for new project
CREATE DATABASE notes_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'notes_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON notes_db.* TO 'notes_user'@'localhost';
FLUSH PRIVILEGES;
```

### Check Connection
```sql
SELECT 1;
SELECT NOW();
SELECT USER();
SELECT VERSION();
```
