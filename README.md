lesson_9

Создал таблицу 
```
 CREATE TABLE salaries (
    id SERIAL PRIMARY KEY,                -- Уникальный идентификатор записи
    last_name VARCHAR(100) NOT NULL,     -- Фамилия сотрудника
    position VARCHAR(100) NOT NULL,       -- Должность сотрудника
    payment_date DATE NOT NULL,           -- Дата выплаты зарплаты
    amount NUMERIC(10, 2) NOT NULL        -- Сумма зарплаты (например, 1000.00)
);
```
Вставил данные
```
INSERT INTO salaries (last_name, position, payment_date, amount) VALUES
('Ivanov', 'manager', '2023-01-15', 55000.00),
('Petrov', 'developer', '2023-01-15', 65000.00),
('Sidorov', 'analitics', '2023-01-15', 60000.00),
('Ivanov', 'manager', '2023-02-15', 55000.00),
('Petrov', 'developer', '2023-02-15', 65000.00),
('Sidorov', 'analitics', '2023-02-15', 60000.00),
('Ivanov', 'manager', '2023-03-15', 60000.00),
('Petrov', 'developer', '2023-03-15', 70000.00),
('Sidorov', 'analitics', '2023-03-15', 65000.00),
('Ivanov', 'manager', '2023-04-15', 65000.00),
('Petrov', 'developer', '2023-04-15', 75000.00),
('Sidorov', ^Cnalitics', '2023-04-15', 70000.00),
('Ivanov', 'manager', '2023-05-15', 70000.00),
('Petrov', 'developer', '2023-05-15', 80000.00),
('Sidorov', 'analitics', '2023-05-15', 75000.00),
('Ivanov', 'manager', '2023-06-15', 75000.00),
('Petrov', 'developer', '2023-06-15', 85000.00),
('Sidorov', 'analitics', '2023-06-15', 80000.00);
```
На сколько было увеличение с предыдущей зарплатой

```
SELECT
    last_name,
    position,
    payment_date,
    amount,
    LAG(amount, 1, 0) OVER (PARTITION BY last_name ORDER BY payment_date) AS prev_salary,
    amount - LAG(amount, 1, 0) OVER (PARTITION BY last_name ORDER BY payment_date) AS salary_increase
FROM
    salaries
ORDER BY
    last_name, payment_date;
```

На ваших данных

```
SELECT 
    e.first_name,
    e.last_name,
    s.amount AS current_salary,
    LAG(s.amount) OVER (PARTITION BY s.fk_employee ORDER BY s.from_date) AS previous_salary,
    s.amount - LAG(s.amount) OVER (PARTITION BY s.fk_employee ORDER BY s.from_date) AS salary_increase
FROM 
    employee e
JOIN 
    salary s ON e.id = s.fk_employee
ORDER BY 
    e.last_name, s.from_date;

first_name	last_name	current_salary	previous_salary	salary_increase
Eugene	Aristov	100000	null	null
Eugene	Aristov	200000	100000	100000
Eugene	Aristov	300000	200000	100000
Ivan	Ivanov	200000	null	null
Petr	Petrov	200000	null	null
```

```
SELECT 
    e.first_name,
    e.last_name,
    s.amount AS current_salary,
    COALESCE(LAG(s.amount) OVER (PARTITION BY s.fk_employee ORDER BY s.from_date), 0) AS previous_salary,
    s.amount - COALESCE(LAG(s.amount) OVER (PARTITION BY s.fk_employee ORDER BY s.from_date), 0) AS salary_increase
FROM 
    employee e
JOIN 
    salary s ON e.id = s.fk_employee
ORDER BY 
    e.last_name, s.from_date;

first_name	last_name	current_salary	previous_salary	salary_increase
Eugene	Aristov	100000	0	100000
Eugene	Aristov	200000	100000	100000
Eugene	Aristov	300000	200000	100000
Ivan	Ivanov	200000	0	200000
Petr	Petrov	200000	0	200000
```
