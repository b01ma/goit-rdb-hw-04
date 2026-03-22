# goit-rdb-hw-04

Woolf University · Neoversity GoIT · Relational Database · Homework #4

## Опис домашнього завдання у форматі питання/відповідь

### 1) Питання: Як створити БД `LibraryManagement` і всі потрібні таблиці зі зв'язками?

**Відповідь:**

```sql
CREATE SCHEMA IF NOT EXISTS LibraryManagement;
USE LibraryManagement;

CREATE TABLE authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255) NOT NULL
);

CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(255) NOT NULL
);

CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR,
    author_id INT,
    genre_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL
);

CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    user_id INT,
    borrow_date DATE,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

---

### 2) Питання: Як заповнити таблиці тестовими даними (1-2 рядки)?

**Відповідь:**

```sql
INSERT INTO authors (author_name)
VALUES
    ('Fei-Fei Li'),
    ('George Orwell');

INSERT INTO genres (genre_name)
VALUES
    ('Poetry'),
    ('Dystopian');

INSERT INTO books (title, publication_year, author_id, genre_id)
VALUES
    ('The Worlds I See', 2023, 1, 1),
    ('1984', 1949, 2, 2);

INSERT INTO users (username, email)
VALUES
    ('dev_user', 'dev@example.com'),
    ('student_ukr', 'student@edu.ua');

INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date)
VALUES
    (1, 1, '2026-03-20', NULL),
    (2, 2, '2026-03-21', '2026-03-22');
```

---

### 3) Питання: Як об'єднати всі таблиці з теми 3 через `FROM` та `INNER JOIN`?

**Відповідь:**

```sql
SELECT
    od.*,
    o.*,
    c.*,
    p.*,
    cat.*,
    e.*,
    sh.*,
    sup.*
FROM order_details od
INNER JOIN orders o ON od.order_id = o.id
INNER JOIN customers c ON o.customer_id = c.id
INNER JOIN products p ON od.product_id = p.id
INNER JOIN categories cat ON p.category_id = cat.id
INNER JOIN employees e ON o.employee_id = e.employee_id
INNER JOIN shippers sh ON o.shipper_id = sh.id
INNER JOIN suppliers sup ON p.supplier_id = sup.id;
```

**Перевірка кількості рядків:**

```sql
SELECT COUNT(*) AS total_rows
FROM order_details od
INNER JOIN orders o ON od.order_id = o.id
INNER JOIN customers c ON o.customer_id = c.id
INNER JOIN products p ON od.product_id = p.id
INNER JOIN categories cat ON p.category_id = cat.id
INNER JOIN employees e ON o.employee_id = e.employee_id
INNER JOIN shippers sh ON o.shipper_id = sh.id
INNER JOIN suppliers sup ON p.supplier_id = sup.id;
```

---

### 4) Питання: Як виконати додаткові запити (COUNT, LEFT/RIGHT, фільтри, групування, HAVING, ORDER BY, LIMIT/OFFSET)?

**Відповідь 4.1 — Базовий агрегований запит за умовою `employee_id > 3 AND <= 10`:**

```sql
SELECT
    cat.name AS category_name,
    COUNT(*) AS row_count,
    AVG(od.quantity) AS avg_quantity
FROM order_details od
INNER JOIN orders o ON od.order_id = o.id
INNER JOIN customers c ON o.customer_id = c.id
INNER JOIN products p ON od.product_id = p.id
INNER JOIN categories cat ON p.category_id = cat.id
INNER JOIN employees e ON o.employee_id = e.employee_id
INNER JOIN shippers sh ON o.shipper_id = sh.id
INNER JOIN suppliers sup ON p.supplier_id = sup.id
WHERE e.employee_id > 3
  AND e.employee_id <= 10
GROUP BY cat.name
HAVING AVG(od.quantity) > 21
ORDER BY row_count DESC
LIMIT 4 OFFSET 1;
```

**Відповідь 4.2 — Порахувати кількість рядків через `COUNT`:**

```sql
SELECT COUNT(*) AS filtered_total
FROM (
    SELECT cat.name
    FROM order_details od
    INNER JOIN orders o ON od.order_id = o.id
    INNER JOIN customers c ON o.customer_id = c.id
    INNER JOIN products p ON od.product_id = p.id
    INNER JOIN categories cat ON p.category_id = cat.id
    INNER JOIN employees e ON o.employee_id = e.employee_id
    INNER JOIN shippers sh ON o.shipper_id = sh.id
    INNER JOIN suppliers sup ON p.supplier_id = sup.id
    WHERE e.employee_id > 3
      AND e.employee_id <= 10
    GROUP BY cat.name
    HAVING AVG(od.quantity) > 21
) t;
```

**Відповідь 4.3 — Замінити частину `INNER JOIN` на `LEFT JOIN` / `RIGHT JOIN`:**

```sql
SELECT COUNT(*) AS rows_with_left_join
FROM order_details od
LEFT JOIN orders o ON od.order_id = o.id
LEFT JOIN customers c ON o.customer_id = c.id
INNER JOIN products p ON od.product_id = p.id
INNER JOIN categories cat ON p.category_id = cat.id
INNER JOIN employees e ON o.employee_id = e.employee_id
INNER JOIN shippers sh ON o.shipper_id = sh.id
INNER JOIN suppliers sup ON p.supplier_id = sup.id;
```

**Пояснення:** при `LEFT/RIGHT JOIN` кількість рядків може збільшитись або залишитись такою самою, бо зберігаються рядки з «лівої»/«правої» таблиці навіть без відповідностей в іншій таблиці; при `INNER JOIN` залишаються тільки повні збіги.

---

## Примітка

- Для звіту додайте скриншоти всіх запитів і результатів їх виконання.
- Точні числові результати (`COUNT`) залежать від даних у вашій БД.