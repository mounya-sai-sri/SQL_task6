# SQL_task6


-- 🧹 Drop existing tables if they exist to avoid errors
DROP TABLE IF EXISTS Orders;
DROP TABLE IF EXISTS Customers;

-- 🏗️ Create Customers table
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    city VARCHAR(50)
);

-- 🏗️ Create Orders table with a foreign key relationship to Customers
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    amount DECIMAL(10, 2),
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

-- 📥 Insert data into Customers table
INSERT INTO Customers (customer_id, name, city) VALUES
(1, 'Alice', 'New York'),
(2, 'Bob', 'Chicago'),
(3, 'Charlie', 'Los Angeles'),
(4, 'David', 'Houston');

-- 📥 Insert data into Orders table
-- Note: order_id 104 has customer_id 5, which does not exist in Customers
INSERT INTO Orders (order_id, customer_id, order_date, amount) VALUES
(101, 1, '2024-06-01', 250.00),
(102, 1, '2024-06-05', 180.00),
(103, 2, '2024-06-10', 340.00),
(104, 5, '2024-06-15', 90.00);

-- 1️⃣ Scalar Subquery Example
-- 🧠 This query selects each customer's name along with the total number of customers
SELECT 
    name,
    (SELECT COUNT(*) FROM Customers) AS total_customers  -- scalar subquery
FROM Customers;

-- 2️⃣ Correlated Subquery Example
-- 🔄 This subquery depends on the outer query. It calculates total order amount for each customer.
SELECT 
    c.name,
    (SELECT SUM(o.amount) 
     FROM Orders o 
     WHERE o.customer_id = c.customer_id) AS total_order_amount
FROM Customers c;

-- 3️⃣ Subquery using IN
-- 📥 Select customers who have placed at least one order
SELECT name
FROM Customers
WHERE customer_id IN (
    SELECT customer_id FROM Orders  -- may return multiple values
);

-- 4️⃣ Subquery using EXISTS
-- ✅ This is similar to IN but often more efficient
-- It returns customers only if a matching order exists
SELECT c.name
FROM Customers c
WHERE EXISTS (
    SELECT 1 
    FROM Orders o 
    WHERE o.customer_id = c.customer_id
);

-- 5️⃣ Subquery using = (Scalar)
-- 🎯 This finds customers whose total order amount is exactly 430
-- Alice has 250 + 180 = 430, so she will be returned
SELECT name
FROM Customers
WHERE (
    SELECT SUM(amount)
    FROM Orders
    WHERE Orders.customer_id = Customers.customer_id
) = 430;
