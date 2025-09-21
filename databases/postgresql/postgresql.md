# PostgreSQL Interview Questions & Answers 🐘

> Comprehensive collection of PostgreSQL interview questions and answers for .NET developers. Covering database design, optimization, advanced features, and integration with .NET applications.

## 📋 Table of Contents

- [Basic Concepts](#basic-concepts)
- [Data Types & Storage](#data-types--storage)
- [Query Optimization](#query-optimization)
- [Indexing Strategies](#indexing-strategies)
- [Advanced Features](#advanced-features)
- [Performance Tuning](#performance-tuning)
- [Security & Administration](#security--administration)
- [.NET Integration](#net-integration)
- [Practical Scenarios](#practical-scenarios)

---

## 🎯 Basic Concepts

### Q1: What is PostgreSQL and how does it differ from other databases?

**Answer:**
PostgreSQL is an open-source, object-relational database management system (ORDBMS) that emphasizes extensibility and standards compliance.

**Key Differences:**

- **vs MySQL**: Better ACID compliance, more advanced features, better handling of complex queries
- **vs SQL Server**: Open-source, cross-platform, more flexible data types (JSON, arrays, custom types)
- **vs Oracle**: Free, more modern features, better JSON support, easier to set up

**Key Features:**

- ACID compliant
- Extensible (custom data types, functions, operators)
- Advanced indexing (B-tree, Hash, GIN, GiST, BRIN)
- Full-text search capabilities
- JSON/JSONB support
- Array data types
- Window functions
- Common Table Expressions (CTEs)

### Q2: Explain PostgreSQL's architecture and main components.

**Answer:**
PostgreSQL follows a client-server architecture with these main components:

**Core Components:**

1. **Postmaster Process**: Main server process that manages connections
2. **Backend Processes**: One per client connection, handles queries
3. **Background Processes**:
   - WAL Writer (Write-Ahead Logging)
   - Checkpointer
   - Autovacuum
   - Stats Collector
   - Logger

**Memory Areas:**

- **Shared Buffers**: Caches frequently accessed data pages
- **WAL Buffers**: Temporary storage for WAL records
- **Work Memory**: Per-connection memory for sorting/hashing
- **Maintenance Work Memory**: For maintenance operations

**Storage:**

- **Data Files**: Store actual table data
- **WAL Files**: Transaction log for crash recovery
- **Configuration Files**: postgresql.conf, pg_hba.conf, pg_ident.conf

---

## 🗃️ Data Types & Storage

### Q3: What are the different data types available in PostgreSQL?

**Answer:**
PostgreSQL offers extensive data types:

**Numeric Types:**

```sql
-- Integer types
SMALLINT    -- 2 bytes, -32,768 to 32,767
INTEGER     -- 4 bytes, -2,147,483,648 to 2,147,483,647
BIGINT      -- 8 bytes, -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807

-- Decimal types
DECIMAL(10,2)  -- Exact numeric, 10 digits total, 2 after decimal
NUMERIC(10,2)  -- Same as DECIMAL
REAL          -- 4 bytes, 6 decimal digits precision
DOUBLE PRECISION -- 8 bytes, 15 decimal digits precision
```

**Character Types:**

```sql
CHAR(10)     -- Fixed-length, padded with spaces
VARCHAR(50)  -- Variable-length with limit
TEXT         -- Variable-length, no limit
```

**Date/Time Types:**

```sql
DATE         -- Date only (YYYY-MM-DD)
TIME         -- Time only (HH:MM:SS)
TIMESTAMP    -- Date and time
TIMESTAMPTZ  -- Timestamp with timezone
INTERVAL     -- Time intervals
```

**Boolean Type:**

```sql
BOOLEAN      -- true, false, or NULL
```

**Binary Data:**

```sql
BYTEA        -- Binary data (bytea)
```

**JSON Types:**

```sql
JSON         -- JSON data (stored as text)
JSONB        -- Binary JSON (more efficient for queries)
```

**Array Types:**

```sql
INTEGER[]    -- Array of integers
TEXT[]       -- Array of text
```

### Q4: What's the difference between JSON and JSONB in PostgreSQL?

**Answer:**

| Feature         | JSON                 | JSONB                   |
| --------------- | -------------------- | ----------------------- |
| **Storage**     | Stored as text       | Stored in binary format |
| **Performance** | Slower queries       | Faster queries          |
| **Indexing**    | Limited indexing     | Full indexing support   |
| **Duplicates**  | Preserves duplicates | Removes duplicates      |
| **Key Order**   | Preserves key order  | No key order guarantee  |
| **Space**       | More space efficient | Less space efficient    |

**Example:**

```sql
-- JSON example
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    data JSON
);

INSERT INTO products (data) VALUES
('{"name": "Laptop", "price": 999.99, "specs": {"ram": "16GB", "storage": "512GB"}}');

-- JSONB example
CREATE TABLE products_binary (
    id SERIAL PRIMARY KEY,
    data JSONB
);

INSERT INTO products_binary (data) VALUES
('{"name": "Laptop", "price": 999.99, "specs": {"ram": "16GB", "storage": "512GB"}}');

-- Querying JSONB is more efficient
SELECT * FROM products_binary WHERE data->>'name' = 'Laptop';
SELECT * FROM products_binary WHERE data->'specs'->>'ram' = '16GB';
```

### Q5: What are PostgreSQL sequences and how do you use them?

**Answer:**
PostgreSQL sequences are special database objects that generate unique numeric values automatically. They're commonly used for creating auto-incrementing primary keys and ensuring unique identifiers across your database.

**What is a Sequence?**
A sequence is essentially a counter that PostgreSQL manages for you. Each time you request a value from a sequence, it returns the next number in the series and increments its internal counter.

**Key Characteristics:**

- **Thread-safe**: Multiple connections can safely request values simultaneously
- **Persistent**: Values survive database restarts
- **Customizable**: You can control the starting value, increment, min/max values
- **Efficient**: Optimized for high-performance ID generation

**Common Use Cases:**
The most typical use is for auto-incrementing primary keys:

```sql
-- Creating a table with an auto-incrementing ID
CREATE TABLE users (
    id SERIAL PRIMARY KEY,  -- SERIAL automatically creates a sequence
    name VARCHAR(100),
    email VARCHAR(255)
);
```

The `SERIAL` data type is actually shorthand that creates a sequence behind the scenes.

**Manual Sequence Creation:**
You can also create sequences explicitly:

```sql
-- Create a custom sequence
CREATE SEQUENCE my_sequence
    START WITH 1000
    INCREMENT BY 5
    MINVALUE 1000
    MAXVALUE 999999
    CACHE 10;

-- Use the sequence
SELECT nextval('my_sequence');  -- Returns 1000
SELECT nextval('my_sequence');  -- Returns 1005
SELECT nextval('my_sequence');  -- Returns 1010
```

**Useful Functions:**

- `nextval('sequence_name')` - Get the next value
- `currval('sequence_name')` - Get the current value (must call nextval first in session)
- `setval('sequence_name', value)` - Set the sequence to a specific value

**Advanced Sequence Usage:**

```sql
-- Using sequences in INSERT statements
INSERT INTO users (id, name, email)
VALUES (nextval('users_id_seq'), 'John Doe', 'john@example.com');

-- Using sequences in DEFAULT values
CREATE TABLE orders (
    id INTEGER DEFAULT nextval('order_sequence'),
    order_number VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Reset a sequence
SELECT setval('my_sequence', 1, false);  -- Reset to 1, next nextval() will return 1
SELECT setval('my_sequence', 1, true);   -- Reset to 1, next nextval() will return 2

-- Get sequence information
SELECT * FROM pg_sequences WHERE sequencename = 'my_sequence';
```

**SERIAL Data Types:**
PostgreSQL provides several SERIAL types for convenience:

```sql
-- Different SERIAL types
CREATE TABLE example (
    id SMALLSERIAL,      -- 2 bytes, 1 to 32,767
    code SERIAL,         -- 4 bytes, 1 to 2,147,483,647 (most common)
    big_id BIGSERIAL     -- 8 bytes, 1 to 9,223,372,036,854,775,807
);
```

**Sequence Management:**

```sql
-- Alter sequence properties
ALTER SEQUENCE my_sequence
    RESTART WITH 1000
    INCREMENT BY 2
    MAXVALUE 999999
    CYCLE;  -- Start over when max value is reached

-- Drop a sequence
DROP SEQUENCE IF EXISTS my_sequence;

-- Rename a sequence
ALTER SEQUENCE old_name RENAME TO new_name;
```

Sequences are particularly valuable in distributed systems and high-concurrency scenarios where you need guaranteed unique values without the overhead of checking existing records.

---

## ⚡ Query Optimization

### Q6: How do you optimize slow queries in PostgreSQL?

**Answer:**
Query optimization involves multiple strategies:

**1. Use EXPLAIN ANALYZE:**

```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
SELECT * FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE o.created_at > '2023-01-01';
```

**2. Index Optimization:**

```sql
-- Create appropriate indexes
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_customers_id ON customers(id);

-- Composite indexes for multi-column queries
CREATE INDEX idx_orders_customer_created ON orders(customer_id, created_at);
```

**3. Query Rewriting:**

```sql
-- Instead of subqueries, use JOINs
-- Slow:
SELECT * FROM customers
WHERE id IN (SELECT customer_id FROM orders WHERE amount > 1000);

-- Fast:
SELECT DISTINCT c.* FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.amount > 1000;
```

**4. Limit Result Sets:**

```sql
-- Use LIMIT and OFFSET efficiently
SELECT * FROM orders
ORDER BY created_at DESC
LIMIT 20 OFFSET 0;
```

**5. Use Window Functions:**

```sql
-- Instead of correlated subqueries
SELECT
    customer_id,
    order_date,
    amount,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) as rn
FROM orders;
```

### Q7: What are the different types of indexes in PostgreSQL and when to use them?

**Answer:**

**1. B-tree Index (Default):**

```sql
-- Good for equality and range queries
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_amount ON orders(amount);

-- Supports: =, <, >, <=, >=, BETWEEN, IN, IS NULL, IS NOT NULL
SELECT * FROM orders WHERE customer_id = 123;
SELECT * FROM orders WHERE amount BETWEEN 100 AND 500;
```

**2. Hash Index:**

```sql
-- Good for equality only
CREATE INDEX idx_orders_status ON orders USING HASH (status);

-- Supports only: =
SELECT * FROM orders WHERE status = 'completed';
```

**3. GIN (Generalized Inverted Index):**

```sql
-- Good for array and full-text search
CREATE INDEX idx_products_tags ON products USING GIN (tags);
CREATE INDEX idx_products_description ON products USING GIN (to_tsvector('english', description));

-- Array operations
SELECT * FROM products WHERE tags @> ARRAY['electronics'];
-- Full-text search
SELECT * FROM products WHERE to_tsvector('english', description) @@ to_tsquery('laptop');
```

**4. GiST (Generalized Search Tree):**

```sql
-- Good for geometric data and custom operators
CREATE INDEX idx_locations_coordinates ON locations USING GiST (coordinates);

-- Geometric queries
SELECT * FROM locations WHERE coordinates && ST_MakeBox2D(ST_Point(0,0), ST_Point(1,1));
```

**5. BRIN (Block Range Index):**

```sql
-- Good for large tables with natural ordering
CREATE INDEX idx_logs_timestamp ON logs USING BRIN (timestamp);

-- Range queries on large datasets
SELECT * FROM logs WHERE timestamp BETWEEN '2023-01-01' AND '2023-01-31';
```

---

## 🚀 Advanced Features

### Q8: Explain Common Table Expressions (CTEs) and Window Functions in PostgreSQL.

**Answer:**

**Common Table Expressions (CTEs):**

```sql
-- Basic CTE
WITH recent_orders AS (
    SELECT customer_id, COUNT(*) as order_count
    FROM orders
    WHERE created_at > '2023-01-01'
    GROUP BY customer_id
)
SELECT c.name, ro.order_count
FROM customers c
JOIN recent_orders ro ON c.id = ro.customer_id;

-- Recursive CTE
WITH RECURSIVE category_tree AS (
    -- Base case
    SELECT id, name, parent_id, 1 as level
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    -- Recursive case
    SELECT c.id, c.name, c.parent_id, ct.level + 1
    FROM categories c
    JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree ORDER BY level, name;
```

**Window Functions:**

```sql
-- Ranking functions
SELECT
    customer_id,
    order_date,
    amount,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) as row_num,
    RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC) as amount_rank,
    DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC) as dense_rank
FROM orders;

-- Aggregate window functions
SELECT
    customer_id,
    order_date,
    amount,
    SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date) as running_total,
    AVG(amount) OVER (PARTITION BY customer_id) as avg_amount,
    LAG(amount, 1) OVER (PARTITION BY customer_id ORDER BY order_date) as prev_amount,
    LEAD(amount, 1) OVER (PARTITION BY customer_id ORDER BY order_date) as next_amount
FROM orders;
```

### Q9: How do you handle transactions and concurrency in PostgreSQL?

**Answer:**

**Transaction Isolation Levels:**

```sql
-- Read Uncommitted (not supported in PostgreSQL)
-- Read Committed (default)
BEGIN;
SELECT * FROM accounts WHERE id = 1; -- Sees committed data
COMMIT;

-- Repeatable Read
BEGIN ISOLATION LEVEL REPEATABLE READ;
SELECT * FROM accounts WHERE id = 1; -- Same data throughout transaction
COMMIT;

-- Serializable
BEGIN ISOLATION LEVEL SERIALIZABLE;
-- Highest isolation, prevents phantom reads
COMMIT;
```

**Locking:**

```sql
-- Row-level locking
BEGIN;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE; -- Exclusive lock
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- Advisory locks
SELECT pg_advisory_lock(123); -- Application-level lock
-- ... do work ...
SELECT pg_advisory_unlock(123);
```

**MVCC (Multi-Version Concurrency Control):**

- Each row has system columns: `xmin`, `xmax`, `ctid`
- Readers don't block writers, writers don't block readers
- Dead tuples are cleaned up by VACUUM

---

## 🔧 Performance Tuning

### Q10: How do you monitor and tune PostgreSQL performance?

**Answer:**

**1. Key Configuration Parameters:**

```sql
-- Check current settings
SELECT name, setting, unit, context
FROM pg_settings
WHERE name IN ('shared_buffers', 'work_mem', 'maintenance_work_mem', 'effective_cache_size');

-- Recommended settings for different scenarios
-- shared_buffers: 25% of RAM
-- work_mem: 4MB for sorting/hashing per connection
-- maintenance_work_mem: 256MB for maintenance operations
-- effective_cache_size: 75% of RAM
```

**2. Monitoring Queries:**

```sql
-- Enable query logging
-- In postgresql.conf:
-- log_statement = 'all'
-- log_min_duration_statement = 1000  -- Log queries taking > 1 second

-- View active queries
SELECT pid, usename, application_name, client_addr, state, query
FROM pg_stat_activity
WHERE state = 'active';

-- View slow queries
SELECT query, calls, total_time, mean_time, rows
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 10;
```

**3. Statistics and Monitoring:**

```sql
-- Table statistics
SELECT schemaname, tablename, n_tup_ins, n_tup_upd, n_tup_del, n_live_tup, n_dead_tup
FROM pg_stat_user_tables;

-- Index usage
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read, idx_tup_fetch
FROM pg_stat_user_indexes;

-- Database size
SELECT pg_size_pretty(pg_database_size('your_database'));
```

### Q11: How do you handle database maintenance and VACUUM operations?

**Answer:**

**VACUUM Operations:**

```sql
-- Regular VACUUM (reclaims space, updates statistics)
VACUUM;

-- VACUUM ANALYZE (also updates query planner statistics)
VACUUM ANALYZE;

-- Full VACUUM (locks table, reclaims more space)
VACUUM FULL;

-- VACUUM specific table
VACUUM orders;

-- Check table bloat
SELECT
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as size,
    pg_size_pretty(pg_relation_size(schemaname||'.'||tablename)) as table_size,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename) - pg_relation_size(schemaname||'.'||tablename)) as index_size
FROM pg_tables
WHERE schemaname = 'public';
```

**Autovacuum Configuration:**

```sql
-- Check autovacuum settings
SELECT name, setting, unit, context
FROM pg_settings
WHERE name LIKE 'autovacuum%';

-- Recommended settings:
-- autovacuum = on
-- autovacuum_max_workers = 3
-- autovacuum_naptime = 1min
-- autovacuum_vacuum_threshold = 50
-- autovacuum_analyze_threshold = 50
-- autovacuum_vacuum_scale_factor = 0.2
-- autovacuum_analyze_scale_factor = 0.1
```

---

## 🔒 Security & Administration

### Q12: How do you implement security best practices in PostgreSQL?

**Answer:**

**1. Authentication and Authorization:**

```sql
-- Create roles
CREATE ROLE app_user WITH LOGIN PASSWORD 'secure_password';
CREATE ROLE readonly_user WITH LOGIN PASSWORD 'readonly_password';

-- Grant permissions
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- Read-only user
GRANT CONNECT ON DATABASE mydb TO readonly_user;
GRANT USAGE ON SCHEMA public TO readonly_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly_user;
```

**2. Row Level Security (RLS):**

```sql
-- Enable RLS on table
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- Create policy for user-specific data
CREATE POLICY user_orders_policy ON orders
    FOR ALL TO app_user
    USING (customer_id = current_setting('app.current_user_id')::int);

-- Set user context
SET app.current_user_id = '123';
```

**3. Encryption:**

```sql
-- Encrypt sensitive data
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Hash passwords
INSERT INTO users (username, password_hash)
VALUES ('john', crypt('password123', gen_salt('bf')));

-- Verify password
SELECT * FROM users
WHERE username = 'john'
AND password_hash = crypt('password123', password_hash);

-- Encrypt data
INSERT INTO sensitive_data (encrypted_field)
VALUES (pgp_sym_encrypt('sensitive data', 'encryption_key'));
```

### Q13: How do you backup and restore PostgreSQL databases?

**Answer:**

**1. Logical Backups (pg_dump):**

```bash
# Full database backup
pg_dump -h localhost -U username -d database_name > backup.sql

# Schema only
pg_dump -h localhost -U username -d database_name --schema-only > schema.sql

# Data only
pg_dump -h localhost -U username -d database_name --data-only > data.sql

# Compressed backup
pg_dump -h localhost -U username -d database_name | gzip > backup.sql.gz

# Restore
psql -h localhost -U username -d database_name < backup.sql
```

**2. Physical Backups (pg_basebackup):**

```bash
# Create base backup
pg_basebackup -h localhost -U username -D /backup/location -Ft -z -P

# Restore from physical backup
# 1. Stop PostgreSQL
# 2. Remove data directory
# 3. Restore from backup
# 4. Configure recovery settings
```

**3. Point-in-Time Recovery (PITR):**

```sql
-- Enable WAL archiving
-- In postgresql.conf:
-- wal_level = replica
-- archive_mode = on
-- archive_command = 'cp %p /backup/wal/%f'

-- Create restore point
SELECT pg_create_restore_point('before_major_changes');

-- Restore to specific point
-- In recovery.conf:
-- restore_command = 'cp /backup/wal/%f %p'
-- recovery_target_time = '2023-01-01 12:00:00'
```

---

## 🔗 .NET Integration

### Q14: How do you connect to PostgreSQL from .NET applications?

**Answer:**

**1. Using Npgsql (Recommended):**

```csharp
// Install-Package Npgsql
using Npgsql;

// Connection string
string connectionString = "Host=localhost;Database=mydb;Username=myuser;Password=mypassword";

// Basic connection
using (var connection = new NpgsqlConnection(connectionString))
{
    connection.Open();

    using (var command = new NpgsqlCommand("SELECT * FROM users WHERE id = @id", connection))
    {
        command.Parameters.AddWithValue("@id", 1);
        using (var reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine($"Name: {reader["name"]}");
            }
        }
    }
}
```

**2. Using Entity Framework Core:**

```csharp
// Install-Package Npgsql.EntityFrameworkCore.PostgreSQL
// Install-Package Microsoft.EntityFrameworkCore.Design

// DbContext
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }

    public DbSet<User> Users { get; set; }
    public DbSet<Order> Orders { get; set; }
}

// Configuration
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(connectionString));

// Usage
public class UserService
{
    private readonly ApplicationDbContext _context;

    public UserService(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<User> GetUserAsync(int id)
    {
        return await _context.Users.FindAsync(id);
    }

    public async Task<List<User>> GetUsersWithOrdersAsync()
    {
        return await _context.Users
            .Include(u => u.Orders)
            .ToListAsync();
    }
}
```

### Q15: How do you handle JSON data in .NET with PostgreSQL?

**Answer:**

**1. Using JSONB with Entity Framework:**

```csharp
// Model with JSONB
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Specifications { get; set; } // JSONB column
}

// DbContext configuration
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Specifications)
        .HasColumnType("jsonb");
}

// Querying JSON data
public async Task<List<Product>> GetProductsBySpecAsync(string specKey, string specValue)
{
    return await _context.Products
        .Where(p => EF.Functions.JsonContains(p.Specifications,
            $"{{\"{specKey}\": \"{specValue}\"}}"))
        .ToListAsync();
}

// Using JSON functions
public async Task<List<Product>> GetProductsWithMemory(string memorySize)
{
    return await _context.Products
        .Where(p => p.Specifications.Contains($"\"memory\": \"{memorySize}\""))
        .ToListAsync();
}
```

**2. Using Dapper with JSON:**

```csharp
// Install-Package Dapper
// Install-Package Npgsql

public class ProductRepository
{
    private readonly string _connectionString;

    public ProductRepository(string connectionString)
    {
        _connectionString = connectionString;
    }

    public async Task<List<Product>> GetProductsAsync()
    {
        using var connection = new NpgsqlConnection(_connectionString);

        var sql = @"
            SELECT id, name, specifications
            FROM products
            WHERE specifications @> @specFilter";

        var parameters = new { specFilter = "{\"category\": \"electronics\"}" };

        return (await connection.QueryAsync<Product>(sql, parameters)).ToList();
    }
}
```

---

## 🎯 Practical Scenarios

### Q16: Design a database schema for an e-commerce system with PostgreSQL.

**Answer:**

```sql
-- Users and Authentication
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Categories (hierarchical)
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    parent_id INTEGER REFERENCES categories(id),
    slug VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Products
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    sku VARCHAR(50) UNIQUE NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    category_id INTEGER REFERENCES categories(id),
    inventory_count INTEGER DEFAULT 0,
    is_active BOOLEAN DEFAULT true,
    specifications JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Product Images
CREATE TABLE product_images (
    id SERIAL PRIMARY KEY,
    product_id INTEGER REFERENCES products(id) ON DELETE CASCADE,
    image_url VARCHAR(500) NOT NULL,
    alt_text VARCHAR(200),
    sort_order INTEGER DEFAULT 0,
    is_primary BOOLEAN DEFAULT false
);

-- Orders
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    order_number VARCHAR(20) UNIQUE NOT NULL,
    status VARCHAR(20) DEFAULT 'pending',
    subtotal DECIMAL(10,2) NOT NULL,
    tax_amount DECIMAL(10,2) DEFAULT 0,
    shipping_amount DECIMAL(10,2) DEFAULT 0,
    total_amount DECIMAL(10,2) NOT NULL,
    shipping_address JSONB NOT NULL,
    billing_address JSONB NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Order Items
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id) ON DELETE CASCADE,
    product_id INTEGER REFERENCES products(id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    total_price DECIMAL(10,2) NOT NULL
);

-- Indexes for performance
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_products_sku ON products(sku);
CREATE INDEX idx_products_active ON products(is_active);
CREATE INDEX idx_orders_user ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created ON orders(created_at);
CREATE INDEX idx_order_items_order ON order_items(order_id);
CREATE INDEX idx_order_items_product ON order_items(product_id);

-- Full-text search index
CREATE INDEX idx_products_search ON products USING GIN (to_tsvector('english', name || ' ' || description));

-- JSONB indexes for specifications
CREATE INDEX idx_products_specs ON products USING GIN (specifications);
```

### Q17: How would you optimize a slow query that joins multiple tables?

**Answer:**

**Problem Query:**

```sql
-- Slow query joining multiple tables
SELECT
    u.username,
    o.order_number,
    o.created_at,
    p.name as product_name,
    oi.quantity,
    oi.unit_price
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE o.created_at >= '2023-01-01'
AND o.status = 'completed'
ORDER BY o.created_at DESC;
```

**Optimization Steps:**

**1. Analyze the Query:**

```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
-- [query above]
```

**2. Create Appropriate Indexes:**

```sql
-- Composite index for the WHERE clause
CREATE INDEX idx_orders_created_status ON orders(created_at, status);

-- Covering index for orders
CREATE INDEX idx_orders_covering ON orders(user_id, created_at, status)
INCLUDE (order_number);

-- Index on order_items for the JOIN
CREATE INDEX idx_order_items_order_product ON order_items(order_id, product_id);

-- Index on products for the JOIN
CREATE INDEX idx_products_id_name ON products(id, name);
```

**3. Rewrite the Query:**

```sql
-- Use CTE to limit data early
WITH recent_orders AS (
    SELECT id, user_id, order_number, created_at
    FROM orders
    WHERE created_at >= '2023-01-01'
    AND status = 'completed'
    ORDER BY created_at DESC
    LIMIT 1000  -- Limit results early
)
SELECT
    u.username,
    ro.order_number,
    ro.created_at,
    p.name as product_name,
    oi.quantity,
    oi.unit_price
FROM recent_orders ro
JOIN users u ON ro.user_id = u.id
JOIN order_items oi ON ro.id = oi.order_id
JOIN products p ON oi.product_id = p.id
ORDER BY ro.created_at DESC;
```

**4. Consider Materialized Views for Complex Queries:**

```sql
-- Create materialized view for frequently accessed data
CREATE MATERIALIZED VIEW order_summary AS
SELECT
    o.id as order_id,
    o.order_number,
    o.created_at,
    u.username,
    COUNT(oi.id) as item_count,
    SUM(oi.total_price) as order_total
FROM orders o
JOIN users u ON o.user_id = u.id
JOIN order_items oi ON o.id = oi.order_id
WHERE o.status = 'completed'
GROUP BY o.id, o.order_number, o.created_at, u.username;

-- Create index on materialized view
CREATE INDEX idx_order_summary_created ON order_summary(created_at);

-- Refresh materialized view periodically
REFRESH MATERIALIZED VIEW order_summary;
```

---

## 🎓 Interview Tips

### For Candidates:

1. **Understand the Basics**: Know PostgreSQL's unique features (JSONB, arrays, custom types)
2. **Performance Focus**: Be ready to discuss indexing strategies and query optimization
3. **Real-world Experience**: Prepare examples from actual projects
4. **Security Awareness**: Understand authentication, authorization, and data protection
5. **.NET Integration**: Know how to work with PostgreSQL in .NET applications

### For Interviewers:

1. **Start Simple**: Begin with basic concepts and build complexity
2. **Practical Scenarios**: Use real-world problems, not theoretical questions
3. **Performance Focus**: Ask about optimization strategies and monitoring
4. **Security Questions**: Include authentication, authorization, and data protection
5. **Integration Knowledge**: Test understanding of .NET integration patterns

---

## 📚 Additional Resources

- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/)
- [Npgsql Documentation](https://www.npgsql.org/)
- [Entity Framework Core with PostgreSQL](https://docs.microsoft.com/en-us/ef/core/providers/npgsql/)
- [PostgreSQL Performance Tuning](https://wiki.postgresql.org/wiki/Performance_Optimization)
- [PostgreSQL Security Best Practices](https://www.postgresql.org/docs/current/security.html)

---

[⬆️ Back to Top](#postgresql-interview-questions--answers-)
