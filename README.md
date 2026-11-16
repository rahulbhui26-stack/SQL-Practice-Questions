# SQL-Practice-Questions
SQL Query related to real world projects

1. Employees earning more than their department average
SELECT *
FROM Employees e
WHERE salary > (
    SELECT AVG(salary)
    FROM Employees
    WHERE department_id = e.department_id
);

2. Employees table with unique and non-empty email
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) NOT NULL UNIQUE
);

3. Orders without a matching customer
SELECT o.*
FROM Orders o
LEFT JOIN Customers c
ON o.customer_id = c.customer_id
WHERE c.customer_id IS NULL;

4. Products table with price greater than 0
CREATE TABLE Products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10,2) CHECK (price > 0)
);

5. Employees working in IT
SELECT e.name
FROM Employees e
INNER JOIN Departments d
ON e.department_id = d.department_id
WHERE d.department_name = 'IT';

6. Second highest salary
SELECT MAX(salary)
FROM Employee
WHERE salary < (
    SELECT MAX(salary)
    FROM Employee
);

7. Department table with auto-increment ID
CREATE TABLE Department (
    dept_id INT AUTO_INCREMENT PRIMARY KEY,
    dept_name VARCHAR(100)
);

8. LEFT JOIN and remove NULL matches
SELECT *
FROM Employees e
LEFT JOIN Departments d
ON e.department_id = d.department_id
WHERE d.department_id IS NOT NULL;

Meaning: Shows only employees having a matching department.
9. Add foreign key to Orders
ALTER TABLE Orders
ADD FOREIGN KEY (customer_id)
REFERENCES Customers(customer_id);

10. Customers with more than 3 orders
SELECT c.customer_id, c.customer_name
FROM Customers c
JOIN Orders o
ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
HAVING COUNT(o.order_id) > 3;

11. Composite primary key
CREATE TABLE Enrollments (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id)
);

12. Employees earning more than their managers
SELECT e.name AS employee,
       m.name AS manager
FROM Employees e
JOIN Employees m
ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;

13. Default status as Active
CREATE TABLE Users (
    user_id INT PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'Active'
);

14. Products that were never ordered
SELECT *
FROM Products p
WHERE NOT EXISTS (
    SELECT 1
    FROM OrderItems oi
    WHERE oi.product_id = p.product_id
);

15. CASCADE vs SET NULL
CASCADE: Deletes child record automatically.
FOREIGN KEY (customer_id)
REFERENCES Customers(customer_id)
ON DELETE CASCADE

SET NULL: Makes the child foreign key NULL.
FOREIGN KEY (customer_id)
REFERENCES Customers(customer_id)
ON DELETE SET NULL

16. FULL OUTER JOIN students and courses
SELECT *
FROM Students s
FULL OUTER JOIN Courses c
ON s.course_id = c.course_id;

Meaning: Shows matching and non-matching records from both tables.
17. Age must be 18 or older
ALTER TABLE Employees
ADD CONSTRAINT chk_age CHECK (age >= 18);

18. Department with highest number of employees
SELECT department_id, COUNT(*) AS employee_count
FROM Employees
GROUP BY department_id
ORDER BY employee_count DESC;

The first row is the department with the most employees.
19. Order date defaults to current date/time
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

20. Employees in departments located in New York
SELECT *
FROM Employees
WHERE department_id IN (
    SELECT department_id
    FROM Departments
    WHERE location = 'New York'
);

21. Drop a constraint
ALTER TABLE Employees
DROP CONSTRAINT constraint_name;

22. CROSS JOIN Colors and Sizes
SELECT c.color_name, s.size_name
FROM Colors c
CROSS JOIN Sizes s;

Meaning: Creates every possible color-size combination.
23. Phone number can be NULL but must be unique
CREATE TABLE Users (
    user_id INT PRIMARY KEY,
    phone_number VARCHAR(20) UNIQUE
);

24. Employees with minimum salary
SELECT *
FROM Employees
WHERE salary = (
    SELECT MIN(salary)
    FROM Employees
);

25. Account type with limited values
CREATE TABLE Accounts (
    account_id INT PRIMARY KEY,
    account_type VARCHAR(20)
    CHECK (account_type IN ('Savings', 'Checking', 'Credit'))
);

26. RIGHT JOIN departments and employees
SELECT d.department_name, e.name
FROM Employees e
RIGHT JOIN Departments d
ON e.department_id = d.department_id;

Meaning: Shows all departments, even departments without employees.
27. Start date before end date
CREATE TABLE Projects (
    project_id INT PRIMARY KEY,
    start_date DATE,
    end_date DATE,
    CHECK (start_date < end_date)
);

28. Employees earning above company average
SELECT *
FROM Employees
WHERE salary > (
    SELECT AVG(salary)
    FROM Employees
);

29. OrderItems with composite key and foreign keys
CREATE TABLE OrderItems (
    order_id INT,
    item_id INT,
    PRIMARY KEY (order_id, item_id),
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (item_id) REFERENCES Items(item_id)
);

30. Customers and items purchased
SELECT c.customer_name, od.item_name
FROM Customers c
JOIN Orders o
ON c.customer_id = o.customer_id
JOIN OrderDetails od
ON o.order_id = od.order_id;

31. Average sales per region above $10,000
SELECT region, avg_sales
FROM (
    SELECT region, AVG(sales) AS avg_sales
    FROM Sales
    GROUP BY region
) x
WHERE avg_sales > 10000;

32. Rename a column
ALTER TABLE Employees
RENAME COLUMN old_name TO new_name;

33. Departments without employees
SELECT d.*
FROM Departments d
LEFT JOIN Employees e
ON d.department_id = e.department_id
WHERE e.employee_id IS NULL;

34. Logins deleted when user is deleted
CREATE TABLE Logins (
    login_id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id)
    REFERENCES Users(user_id)
    ON DELETE CASCADE
);

35. Salary greater than ALL employees in Department 10
SELECT *
FROM Employees
WHERE salary > ALL (
    SELECT salary
    FROM Employees
    WHERE department_id = 10
);

36. Change column data type and keep NOT NULL
SQL Server:
ALTER TABLE Employees
ALTER COLUMN salary DECIMAL(10,2) NOT NULL;

37. Top 3 highest-paid employees from each department
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY department_id
               ORDER BY salary DESC
           ) AS rn
    FROM Employees
) x
WHERE rn <= 3;

38. UNION two employee tables
SELECT employee_name
FROM Domestic_Branch

UNION

SELECT employee_name
FROM International_Branch;

UNION removes duplicates.
39. Inventory with non-negative stock
CREATE TABLE Inventory (
    product_id INT PRIMARY KEY,
    stock_quantity INT DEFAULT 0
    CHECK (stock_quantity >= 0)
);

40. Total revenue per category
SELECT c.category_name,
       SUM(od.quantity * od.unit_price) AS revenue
FROM Categories c
JOIN Products p
ON c.category_id = p.category_id
JOIN OrderDetails od
ON p.product_id = od.product_id
JOIN Orders o
ON od.order_id = o.order_id
GROUP BY c.category_name;

41. UNIQUE combination of first and last name
ALTER TABLE Employees
ADD CONSTRAINT unique_name
UNIQUE (first_name, last_name);

42. Products priced higher than at least one Electronics product
SELECT *
FROM Products
WHERE price > ANY (
    SELECT price
    FROM Products
    WHERE category = 'Electronics'
);

ANY: Higher than at least one value.
43. DEFAULT vs NOT NULL when inserting NULL
DEFAULT:
status VARCHAR(20) DEFAULT 'Active'

If you don't provide a value, SQL uses 'Active'.

If you explicitly insert NULL, it normally stores NULL.

NOT NULL:
status VARCHAR(20) NOT NULL

You cannot insert NULL.
44. Employees with manager name
SELECT e.name AS employee,
       COALESCE(m.name, 'No Manager') AS manager
FROM Employees e
LEFT JOIN Employees m
ON e.manager_id = m.employee_id;

45. Payments table
CREATE TABLE Payments (
    payment_id INT PRIMARY KEY,
    amount DECIMAL(10,2) NOT NULL
    CHECK (amount > 0)
);

46. Customer with the largest transaction
SELECT c.customer_name
FROM Customers c
JOIN Orders o
ON c.customer_id = o.customer_id
WHERE o.amount = (
    SELECT MAX(amount)
    FROM Orders
);

47. Copy table structure including constraints
PostgreSQL:
CREATE TABLE NewEmployees
(LIKE Employees INCLUDING ALL);

48. Active project assignments
SELECT e.name AS employee,
       p.project_name
FROM Employees e
JOIN EmployeeProjects ep
ON e.employee_id = ep.employee_id
JOIN Projects p
ON ep.project_id = p.project_id
WHERE p.status = 'Active';

49. Employee name and department employee count
SELECT e.name,
       (
           SELECT COUNT(*)
           FROM Employees e2
           WHERE e2.department_id = e.department_id
       ) AS department_count
FROM Employees e;

50. Drop a foreign key constraint
ALTER TABLE Orders
DROP CONSTRAINT fk_orders_customer;
