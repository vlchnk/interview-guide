 # SQL

## Содержание
1. [Основы SQL](#основы-sql)
2. [Операции с данными](#операции-с-данными)
3. [Соединения таблиц](#соединения-таблиц)
4. [Агрегация](#агрегация)
5. [Оптимизация](#оптимизация)
6. [Транзакции](#транзакции)
7. [Нормализация](#нормализация)

## Основы SQL

### Создание таблицы
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Типы данных
- **Числовые**: INT, BIGINT, DECIMAL, FLOAT
- **Строковые**: VARCHAR, CHAR, TEXT
- **Дата и время**: DATE, TIME, TIMESTAMP
- **Булевы**: BOOLEAN
- **JSON**: JSON, JSONB

## Операции с данными

### SELECT
```sql
-- Базовый запрос
SELECT * FROM users;

-- С условием
SELECT name, email FROM users WHERE age > 18;

-- Сортировка
SELECT * FROM users ORDER BY created_at DESC;

-- Лимит
SELECT * FROM users LIMIT 10 OFFSET 20;
```

### INSERT
```sql
-- Одиночная вставка
INSERT INTO users (name, email) VALUES ('John', 'john@example.com');

-- Множественная вставка
INSERT INTO users (name, email) VALUES 
    ('John', 'john@example.com'),
    ('Jane', 'jane@example.com');
```

### UPDATE
```sql
UPDATE users 
SET name = 'John Doe', email = 'john.doe@example.com'
WHERE id = 1;
```

### DELETE
```sql
DELETE FROM users WHERE id = 1;
```

## Соединения таблиц

### INNER JOIN
```sql
SELECT users.name, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id;
```

### LEFT JOIN
```sql
SELECT users.name, orders.amount
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```

### RIGHT JOIN
```sql
SELECT users.name, orders.amount
FROM users
RIGHT JOIN orders ON users.id = orders.user_id;
```

### FULL JOIN
```sql
SELECT users.name, orders.amount
FROM users
FULL JOIN orders ON users.id = orders.user_id;
```

## Агрегация

### GROUP BY
```sql
SELECT 
    user_id,
    COUNT(*) as order_count,
    SUM(amount) as total_amount
FROM orders
GROUP BY user_id;
```

### HAVING
```sql
SELECT 
    user_id,
    COUNT(*) as order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;
```

### Оконные функции
```sql
SELECT 
    user_id,
    amount,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY amount DESC) as rank
FROM orders;
```

## Оптимизация

### Индексы
```sql
-- Создание индекса
CREATE INDEX idx_users_email ON users(email);

-- Составной индекс
CREATE INDEX idx_orders_user_date ON orders(user_id, order_date);
```

### EXPLAIN
```sql
EXPLAIN ANALYZE
SELECT * FROM users WHERE email = 'john@example.com';
```

### Оптимизация запросов
1. Использование индексов
2. Избегание SELECT *
3. Оптимизация JOIN
4. Использование LIMIT
5. Кэширование запросов

## Транзакции

### ACID
- **Atomicity**: атомарность
- **Consistency**: согласованность
- **Isolation**: изолированность
- **Durability**: долговечность

### Пример транзакции
```sql
BEGIN;
    UPDATE accounts SET balance = balance - 100 WHERE id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

### Уровни изоляции
1. READ UNCOMMITTED
2. READ COMMITTED
3. REPEATABLE READ
4. SERIALIZABLE

## Нормализация

### 1NF
- Атомарность значений
- Уникальные строки
- Отсутствие повторяющихся групп

### 2NF
- Соответствие 1NF
- Зависимость от полного первичного ключа

### 3NF
- Соответствие 2NF
- Отсутствие транзитивных зависимостей

### Пример нормализации
```sql
-- Денормализованная таблица
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    product_name VARCHAR(100),
    product_price DECIMAL
);

-- Нормализованные таблицы
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id),
    product_id INT REFERENCES products(product_id)
);
```